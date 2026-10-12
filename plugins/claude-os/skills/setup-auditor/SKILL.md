---
name: setup-auditor
description: Audita CLAUDE.md, reglas, skills, hooks y memoria contra la guía vigente de Anthropic para el modelo en uso: veredicto por regla con fuente, y conflictos entre capas. Para 'auditá mi setup'.
---

# Setup Auditor

Un setup de Claude es arqueología. Cada instrucción del CLAUDE.md se escribió para arreglar un problema que tenía un modelo *anterior*, y nadie vuelve a sacarla cuando el modelo deja de tener ese problema. Las instrucciones quedan, cuestan tokens y varias ahora empeoran el resultado.

Anthropic hizo esta limpieza con su propio producto: al salir Opus 5 borraron más del 80% del system prompt de Claude Code y anduvo mejor sin eso. Su consejo publicado es probar cada seis meses qué pasa sin el CLAUDE.md, sin las skills y sin los hooks.

Esta skill hace esa auditoría en serio en vez de adivinar: lee lo que hay, baja lo que Anthropic recomienda hoy para el modelo que está corriendo, y da un veredicto por instrucción con la línea de la fuente atrás.

## Las dos reglas que hacen que esto funcione

**1. Nunca hardcodear la guía. Siempre bajarla en vivo.** El consejo por modelo cambia cada vez que sale uno. Una skill con las reglas adentro se convierte en el mismo artefacto viejo que vino a encontrar. Por eso el paso 2 siempre va y lee las páginas actuales.

**2. La introspección del modelo no es evidencia.** "¿Ya harías esto sin que te lo digan?" la contesta el mismo modelo que va a decir que sí a casi todo. Una regla que suena obvia suele ser cicatriz de una falla real. La única forma honesta de saber si sobra es sacarla y ver si falla. Por eso acá la pregunta no es "¿lo harías igual?" sino "¿nació de una falla documentada?", y las que no tienen falla atrás van a una lista de prueba, no a la basura.

## Seguridad

- **Nunca borrar, archivar ni editar nada en la sesión de auditoría.** Esta skill produce un informe. Lo que se aplica se aplica en otra sesión, fila por fila, con confirmación explícita.
- Si en la sesión siguiente se aplica algo: backup primero (`cp <archivo> <archivo>.pre-audit`), nada se borra de forma permanente, todo lo que sale va a una carpeta `_archivo/` dentro de la misma estación.
- Una regla que parece redundante puede existir por una falla real. Cuando una candidata a BORRAR parece deliberada, decirlo y preguntar en vez de asumir que es basura.
- No tocar archivos fuera de los que el usuario nombró o de las ubicaciones estándar del paso 1.

## Paso 1. Inventario

Buscar en estas ubicaciones y reportar qué existe antes de analizar nada.

```bash
# Instrucciones
ls -la ~/.claude/CLAUDE.md ./CLAUDE.md ./AGENTS.md ./.claude/CLAUDE.md ./.claude/rules/ 2>/dev/null
wc -lw ~/.claude/CLAUDE.md ./CLAUDE.md 2>/dev/null

# Memoria (persiste entre sesiones y también es instrucción)
ls ~/.claude/projects/*/memory/MEMORY.md 2>/dev/null
wc -l ~/.claude/projects/*/memory/MEMORY.md 2>/dev/null

# Skills, agentes, hooks
ls -d ~/.claude/skills/*/ ./.claude/skills/*/ 2>/dev/null | wc -l
ls -d ~/.claude/agents/*/ ~/.claude/agents/*.md 2>/dev/null
grep -l "hooks" ~/.claude/settings.json ./.claude/settings.json 2>/dev/null
```

Reportar una tabla: artefacto, ruta, líneas y palabras. El tamaño importa: un CLAUDE.md de 400 líneas es la señal más fuerte de que la auditoría está atrasada. Esos números son el "antes" del paso 4.

Las skills se auditan por su `description` y por las instrucciones de comportamiento que llevan adentro, no por sus referencias de datos.

## Paso 2. Bajar la guía vigente (nunca saltear, nunca cachear)

Primero saber en qué modelo se está (`/model`, o preguntar). Después bajar:

1. `https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices`, la referencia viva entre modelos
2. La página específica del modelo en uso, linkeada desde la anterior. Hay una por modelo vigente, por ejemplo `.../prompt-engineering/prompting-claude-opus-5`
3. `https://claude.com/blog/best-practices-for-prompt-engineering`, el post general

Si una bajada falla, decirlo y marcar los chequeos afectados como NO CORRIDO. Nunca reemplazar una bajada fallida por guía recordada.

## Paso 3. Los chequeos

Cuatro pasadas, en este orden. La de borrado va primera porque es donde está la ganancia.

### 3a. Candidatas a BORRAR (contra la guía bajada)

Buscar cada patrón en los archivos. Citar la línea encontrada y la línea de la guía que la condena.

| # | Buscar | Por qué se va |
| --- | --- | --- |
| 1 | Pasos de verificación explícitos: "verificá antes de terminar", "incluí un paso final de verificación", "usá un subagente para verificar", "probá que anda" | Anthropic: sacarlos en Opus 5 y posteriores, causan sobre-verificación y gastan tokens sin ganancia de calidad. Aplica también a scaffolding del harness que agrega un paso de verificación aparte |
| 2 | Instrucciones de re-chequeo: "revisá dos veces tu respuesta", "re-verificá antes de responder", "cuestioná tu propio trabajo" | El modelo ya se autocorrige. Estas se suman a su comportamiento y agregan costo sin mejorar nada |
| 3 | Cualquier regla que le diga al modelo que no piense o no razone | Aumenta la filtración de tags internos al output visible. Sacarla |
| 4 | Prompts de revisión que digan "reportá solo problemas graves" o "sé conservador" | Lo toma literal y reporta menos. Pedir todo y filtrar en una segunda pasada |
| 5 | Workarounds de visión afinados para un modelo viejo | Re-validar. Casi nunca hacen falta ya |
| 6 | Defaults de esfuerzo heredados de un modelo anterior | Re-correr un barrido de esfuerzo. `low` y `medium` sostienen la calidad a una fracción del costo |
| 7 | Scaffolding de tags XML y role-play pesado tratados como estructura obligatoria | Antes recomendado, hoy opcional. Mantener donde desambigua contenido mixto de verdad, sacar donde es ceremonia |

**Guardia contra falsos positivos en el chequeo 1 y el 4.** "Antes de decir que un fix anda, probá el comportamiento real" o "reportá solo lo que podés señalar con evidencia" son reglas de VERACIDAD, no de verificación ceremonial ni de filtro por severidad. Lo mismo para cualquier línea que prohíba inventar o reportar como hecho algo no visto. Esas son MANTENER. El chequeo 1 dispara solo sobre pasos de verificación agregados por encima del trabajo ya hecho; el 4 solo sobre instrucciones que achican lo reportado *por severidad o confianza*. Equivocarse acá empeora el setup en vez de mejorarlo.

### 3b. Debilidad vieja (por regla, sin fuente externa)

Para cada regla que sobrevivió 3a, contestar una sola pregunta: **¿corrige una debilidad que el modelo actual ya no tiene?**

No se contesta por introspección ("yo ya haría esto"). Se contesta así:

- **Tiene falla documentada atrás** (un MEMORY.md con el episodio, un comentario en el archivo, el usuario lo recuerda, o la regla lleva su porqué escrito): MANTENER, aunque suene obvia. Es cicatriz.
- **No tiene falla rastreable** y describe un comportamiento que la guía bajada en el paso 2 dice que el modelo actual tiene por defecto: PARKEAR. Va a una lista de prueba: se saca una semana y vuelve solo si falla.
- **No tiene falla rastreable y la guía no dice nada**: MANTENER, y anotar que la guía no la cubre.

Un veredicto PARKEAR nunca se aplica en esta sesión. Es una hipótesis a probar, no una decisión.

### 3c. Conflictos entre capas

Las instrucciones viven en varias capas que se cargan juntas: CLAUDE.md global, CLAUDE.md de proyecto, archivos de reglas, MEMORY.md, descripciones de skills, hooks. Nadie las lee en conjunto, así que se contradicen sin que nadie lo note, y el modelo resuelve la contradicción de forma distinta cada sesión.

Cruzar todas las capas y reportar cada par que choque: misma situación, instrucción distinta. Ejemplos del tipo de cosa que aparece: un CLAUDE.md que dice que "doc" es un Google Doc y una memoria que dice que "documento" es un .docx local; una regla que manda publicar los mockups como Artifact y una memoria que dice que Artifact nunca va por default.

Por cada conflicto: las dos líneas, sus archivos, y cuál parece más nueva (fecha del MEMORY.md, historial de git si hay). No resolverlo: eso lo decide el usuario. Si una de las dos ya está cubierta por la otra sin chocar, es duplicado, no conflicto: va a una lista aparte de candidatas a unificar, y tampoco se unifica en esta sesión.

### 3d. Candidatas a AGREGAR

Los modelos nuevos son más verbosos y más ansiosos. Revisar si el setup tiene cada una y marcar el hueco. Si ya la tiene en cualquier capa, no proponerla.

| # | Instrucción faltante | Por qué hace falta ahora |
| --- | --- | --- |
| 1 | Una instrucción explícita de concisión | Las respuestas por defecto salen más largas que en modelos anteriores. El esfuerzo controla el razonamiento, no el largo del output, así que el largo hay que pedirlo |
| 2 | Calibración de largo para entregables escritos | Los archivos a disco salen más largos y rellenados si no se calibra |
| 3 | Cadencia de narración en trabajo agéntico | El modelo anuncia lo que va a hacer. Describir la cadencia que se quiere, con ejemplos positivos y no con prohibiciones |
| 4 | Un límite de alcance por tarea | Expande el alcance y agrega pasos no pedidos. Acotarlo explícito para tareas chicas |
| 5 | Un tope de delegación a subagentes | Delega fácil, y eso multiplica el costo en tareas chicas |

## Paso 4. Salida

Entregar esto, en este orden.

1. **Veredicto en una línea.** `Tu setup tiene 312 reglas en 2.400 palabras. 9 deberían irse, 6 van a prueba, 3 pares se contradicen, 2 faltan.`
2. **Tabla de borrado.** Una fila por hit: la línea, archivo y número de línea, qué chequeo la atrapó y la frase de la guía de Anthropic. Sin hit no hay fila: nunca rellenar la tabla para parecer exhaustivo.
3. **Tabla de parkeo.** Una fila por regla sin falla rastreable: la línea, archivo, y qué dice la guía sobre ese comportamiento por defecto.
4. **Tabla de conflictos.** Una fila por par: las dos líneas, sus archivos, cuál parece más nueva. Debajo, la lista de duplicados.
5. **Tabla de agregados.** Una fila por hueco, con el texto exacto para pegar.
6. **Antes y después proyectado.** Cantidad de reglas y palabras hoy, y cuántas quedarían si se aplicara todo lo propuesto. Es proyección, no resultado.
7. **El experimento de los seis meses.** Decir la jugada más fuerte sin vueltas: renombrar el archivo, correr una semana sin él, y volver a agregar solo lo que de verdad faltó. `mv CLAUDE.md CLAUDE.md.parked`
8. **Lo que no se chequeó.** Cualquier bajada fallida, archivo ilegible o chequeo que no corrió. Marcado como NO CORRIDO.

Después, parar. No aplicar nada hasta que el usuario diga qué filas se accionan, y aplicarlas en una sesión aparte.

## Al reescribir una regla

Si el usuario pide REESCRIBIR, acortar sin perder el porqué. Una regla que dice "las imágenes van adjuntas, nunca embebidas: Gmail bloquea el base64 y queda un cuadrito roto" se puede comprimir, pero el "Gmail bloquea el base64" se queda. Es lo que permite aplicar el criterio en una situación nueva en vez de obedecer el literal. "En la menor cantidad de palabras posible" es un objetivo equivocado: el objetivo es la menor cantidad de palabras que conserve la razón.

## Para después de la auditoría

Una regla nueva entra recién cuando el usuario corrige lo mismo dos veces. Una corrección sola es un caso; dos es un patrón. Si el setup ya tiene esta regla escrita, no duplicarla.

## La versión pegable (sin Claude Code)

No todo el mundo corre Claude Code. Dar esto a quien vive en el chat: pega su CLAUDE.md o sus instrucciones personalizadas debajo.

```text
Estás auditando mis instrucciones de Claude para encontrar qué quedó viejo.

Primero leé la guía vigente de Anthropic:
- platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices
- la página de prompting específica del modelo que estoy usando, linkeada desde la anterior
- claude.com/blog/best-practices-for-prompt-engineering

Después recorré mis instrucciones de abajo, línea por línea, y dame una tabla con una fila por instrucción:

| Mi instrucción | Veredicto | Por qué | La línea de Anthropic |

El veredicto es BORRAR, MANTENER, REESCRIBIR o PARKEAR.

Para cada BORRAR y REESCRIBIR, citá la frase de la guía de Anthropic que lo justifica. Si no la encontrás, marcá MANTENER y decí que la guía no lo cubre. No adivines y no inventes una fuente.

PARKEAR es para reglas que describen un comportamiento que, según la guía, el modelo actual ya tiene por defecto, y que no llevan escrito el porqué ni una falla atrás. No contestes "yo ya haría esto" por introspección: eso no es evidencia. Si la regla lleva su porqué o parece nacida de una falla real, es MANTENER aunque suene obvia.

Prestá atención especial a instrucciones que te piden verificar tu trabajo, revisar dos veces, ser conservador o no pensar. Esas se escribieron para modelos viejos y hoy empeoran el output. Pero "no inventes" y "probá antes de decir que anda" son reglas de veracidad, no de verificación: esas se mantienen.

Después buscá pares de instrucciones que se contradigan entre sí (misma situación, instrucción distinta) y listalos sin resolverlos.

Por último, decime las cuatro o cinco instrucciones que me faltan y que los modelos nuevos necesitan, con el texto exacto para agregar.

Reportá todo lo que encuentres. Yo decido qué se corta. No reescribas nada que no te pida.

Mis instrucciones:
[pegar acá]
```

## Fallas a evitar

- **Reportar una auditoría limpia que no se corrió.** Si una bajada falló o un archivo no se pudo leer, decir NO CORRIDO. Nunca un tilde verde por defecto.
- **Inventar una línea de fuente.** Cada BORRAR cita texto real de una página bajada en esta corrida.
- **Contestar por introspección.** "Esto ya lo haría solo" no es un veredicto, es la falla que esta skill existe para evitar.
- **Rellenar la tabla.** Nueve hallazgos reales valen más que veinte con once dudosos.
- **Borrar una regla que se ganó el lugar.** La cicatriz no siempre es basura. Marcar y preguntar.
- **Comprimir hasta perder el porqué.** Una regla sin razón se obedece literal y falla en el caso nuevo.
- **Cachear la guía.** Bajar en cada corrida. Un auditor viejo es el problema, no la solución.
- **Aplicar en la misma sesión.** Auditar y archivar son dos actos distintos, con una confirmación entre medio.
