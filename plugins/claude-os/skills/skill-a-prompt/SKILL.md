---
name: skill-a-prompt
description: Convierte una skill (SKILL.md + referencias) en un prompt de texto pegable en cualquier IA o como instrucciones de un GPT. Se activa con "pasá esta skill a prompt", "convertila para ChatGPT/GPT".
metadata:
  version: 1.0.0
---

# Skill a Prompt

Tomás una skill completa —su SKILL.md y todo lo que tenga en `references/`— y la traducís a un prompt de texto que funciona sin el mecanismo de skills: sin carga automática, sin filesystem propio, sin herramientas conectadas. Alguien lo pega una vez (en el campo de instrucciones de un GPT, en custom instructions, al arrancar cualquier chat) y el modelo actúa con el mismo criterio que tenía la skill original.

## Cuándo se usa

Para convertir una skill ya escrita en un prompt portable, cuando el destino no tiene sistema de skills (ChatGPT, un GPT personalizado, un chat suelto) o cuando alguien sin acceso a la skill instalada necesita el mismo comportamiento.

No usar para redactar un prompt nuevo desde cero, ni para pulir o adaptar un prompt que ya existe como texto — eso no parte de una skill completa. No usar para producir un `.docx` con diseño de marca: la salida acá es siempre texto plano pegable, listo en el chat.

## Qué necesito antes de empezar

| Insumo | Obligatorio | Si falta |
|---|---|---|
| La skill a convertir: SKILL.md completo + todo archivo de `references/` (o `resources/`, `assets/` si los tiene) | sí | pedirla entera. No convertir a partir de un resumen o de memoria de la skill — hay que leer el archivo real |
| Destino del prompt: GPT personalizado (campo Instructions) / custom instructions de ChatGPT personal / Project de Claude.ai / prompt suelto para pegar al arrancar cualquier chat | sí | preguntar — define el presupuesto de caracteres y si hace falta separar un bloque de material de referencia |
| Quién lo va a usar (Mati solo / equipo de Muta / un cliente / desconocido) | no | si no se aclara, tratarlo como si pudiera llegar a manos de cualquiera y aplicar igual el paso 5 (de-hardcodeo) — nunca al revés |

## Proceso

1. Leer la skill completa: el SKILL.md entero y cada archivo de `references/`. No arrancar sin haberlos leído todos.
2. Medir complejidad: líneas del SKILL.md + cantidad de archivos de referencia. Define cuánto hay que comprimir (tabla en Referencia).
3. Fijar el presupuesto de caracteres según el destino (tabla en Referencia).
4. Mapear cada sección de la skill a los 9 componentes del prompt de salida (tabla en Referencia). Si una sección de la skill no tiene equivalente claro, no forzarla — se deja afuera antes que rellenar con relleno.
5. Reescribir lo que no transfiere literal, siguiendo la tabla "Qué no transfiere igual" de Referencia: llamadas a herramientas (Read, Bash, MCP, buscar en Drive/Asana/etc.) pasan a ser "pedile al usuario que pegue o describa X"; operaciones de filesystem o de otra skill se convierten en instrucción manual; scripts y assets no ejecutables quedan anotados como referencia, no como algo que el prompt pueda correr; si la skill deriva a otra skill (como `estructuras-copy → carrusel`), marcarlo como dependencia aparte, no inventar el contenido de la otra skill.
5.1. Si el proceso de la skill original elige entre varias opciones nombradas (una fórmula, una estructura, un framework, una plantilla), la instrucción de **declarar cuál eligió y por qué, en una nota aparte después del entregable**, va a Restricciones — nunca a Formato de salida. Es la diferencia entre P1 (nunca se corta) y P2 (se corta bajo presupuesto ajustado): si esa instrucción queda solo en Formato de salida, la compresión del paso 9 se la lleva puesta y el resultado deja de ser auditable — no hay forma de saber si el criterio de elección fue el correcto.
6. Sacar todo nombre propio, ruta de carpeta personal (`Clientes/<nombre>`, `~/Claude/...`) y fuente de datos asumida como única (ej. "sacalo de Fathom" cuando el dato podría venir de cualquier grabador). Dejarlo genérico o como pregunta al usuario. Esto se hace siempre, no es opcional: un prompt que sale de acá puede terminar en cualquier mano.
7. Escribir el bloque de prompt con las 9 secciones en el orden fijo de Formato de salida, sin omitir ningún encabezado aunque el contenido sea breve. Cerrar con la línea de re-anclaje (ver Referencia) — un prompt suelto no se re-lee solo como una skill cargada; sin esa línea se diluye en chats largos.
8. Si el contenido de la skill no entra completo en el presupuesto, separar lo que sobra (ejemplos largos, tablas completas, casos borde) en el segundo bloque de Material de referencia. Nunca lo resumas de forma que pierda el criterio — cortalo por prioridad P1/P2/P3, no por longitud pareja.
8.1. Cuando un paso del proceso original enumera los componentes de un criterio (los términos de una fórmula, los tipos de una categoría, los ejes de una evaluación), la enumeración entera se conserva en el prompt aunque se pierda la explicación de cada ítem. Nombrar los cuatro términos de una ecuación cuesta una línea; explicarlos cuesta una página: la compresión saca la explicación, nunca la lista. Un proceso que dice "definí el resultado buscado" donde el original decía "resultado buscado × probabilidad ÷ demora × esfuerzo" no quedó más corto, quedó incompleto — y quien use el prompt no tiene forma de enterarse de que le falta, porque lo que se perdió no dejó hueco visible.
9. Contar los caracteres del bloque de prompt. Si se pasa del presupuesto, recortar en el orden de la tabla de compresión de Referencia. Los encabezados nunca se sacan, solo se comprime lo que hay debajo.
10. Antes de mostrar nada, verificar el prompt contra la skill fuente: recorrer sus secciones de arriba abajo y confirmar, una por una, que las reglas de criterio, las enumeraciones y las condiciones de salida que clasificaste como P1 están efectivamente en el prompt, y que ninguna quedó condicionada a un caso más angosto que el original (si la fuente dice "siempre", el prompt no puede decir "si te piden X"). Lo que falte, entra; si no entra, sale algo de P2 primero. Este paso no es opcional ni se saltea cuando el prompt "quedó bien": con compresión alta, dos corridas de esta misma skill sobre la misma fuente pierden cosas distintas cada una, y la verificación es lo único que hace el resultado repetible en vez de dependiente de qué tuvo presente la corrida.
11. Mostrar los dos bloques completos en el chat, sin truncar ni resumir con "...", con el conteo de caracteres del bloque de prompt y una línea de cómo pegar cada uno según el destino elegido. El conteo es una estimación: si el destino tiene tope duro, dejar margen real en vez de apuntar al límite.

## Formato de salida

```
## PROMPT — pegar en [destino elegido] ([N] caracteres)

## Rol
Sos [expertise extraída de la skill]. Te especializás en [dominio específico].

## Tarea
Tu tarea principal es [objetivo extraído de la skill].
Seguí este proceso:
1. [paso condensado]
2. [paso condensado]
...

## Objetivo
[resultado esperado, extraído de la skill]

## Audiencia
[para quién es el output. Si la skill no lo dice: "Usuarios en general que buscan ayuda en [dominio]. Ajustá la complejidad según el nivel que muestre la persona."]

## Tono
[reglas de voz extraídas de la skill. Si no dice nada: "Directo y claro, sin corporativismo. Igualá el registro de quien pregunta."]

## Restricciones
- [regla nunca/siempre]
- [límite de caracteres o formato]
- [estándar de calidad]

## Insumos
Antes de arrancar, pedile al usuario:
1. [insumo obligatorio de la skill original]
2. [insumo obligatorio]

## Ejemplo de salida
[1-2 ejemplos breves, recortados a estructura y patrón clave]

## Formato de salida
[estructura y reglas de formato del output final]

## Material de referencia
[si hubo bloque aparte: qué contiene y cuándo consultarlo. Si no hubo: "Todo el contenido de la skill entró en este prompt, no hace falta material aparte."]

## Recordatorio
Antes de cada respuesta, releé Restricciones y Formato de salida arriba — no asumas que los recordás de mensajes anteriores en esta conversación.

---

## MATERIAL DE REFERENCIA — [cómo pegarlo según el destino: subir como archivo / segundo mensaje fijado / campo de "knowledge"]

[contenido que no entró en el prompt: ejemplos completos, tablas largas, casos borde, plantillas extensas — organizado con los mismos títulos que traía la skill]
```

Si todo el contenido entró en el bloque de prompt, el segundo bloque no se genera — se dice en una línea que no hace falta.

## Referencia

### Los 9 componentes (orden fijo, no se omite ninguno)

| # | Componente | Sale de la skill | Formato en el prompt |
|---|---|---|---|
| 1 | Rol | `description` del frontmatter, párrafo de intro, cualquier "Sos..." | "Sos [rol]. Te especializás en [dominio]." |
| 2 | Tarea | Secciones de proceso, pasos numerados | Pasos numerados, condensados a 1-2 líneas cada uno |
| 3 | Objetivo | Sección de propósito, descripciones de output | "Tu objetivo es [producir X] que cumpla [estándar]." |
| 4 | Audiencia | Menciones de público objetivo, "antes de empezar" | Quién recibe el resultado, ajusta complejidad |
| 5 | Tono | Guías de voz, listas de "nunca uses" | Enunciados directos de cómo escribir |
| 6 | Restricciones | "Reglas", "Nunca/Siempre", límites de caracteres | Lista de bullets con reglas duras |
| 7 | Insumos | "Qué necesito antes de empezar", preguntas de contexto | "Antes de arrancar, pedile al usuario: 1... 2..." |
| 8 | Ejemplo de salida | Sección de ejemplos, plantillas de output | 1-2 ejemplos cortos, recortados a estructura |
| 9 | Formato de salida | Sección de formato, plantilla literal | Estructura y reglas de formato final |

### Qué no transfiere igual

| En la skill original | En el prompt de salida |
|---|---|
| Llamadas a herramientas (Read, Bash, MCP, buscar en Drive/Asana) | "Pedile al usuario que pegue o describa [eso]" |
| Comandos dinámicos, automatizaciones | Documentado como paso manual |
| Operaciones de filesystem, rutas | "Pedile que suba o pegue el contenido" |
| Scripts y `assets/` no ejecutables | Anotados como referencia, con aviso de que no corren solos |
| Referencia a otra skill (`derivá a X`) | Marcado como dependencia — esa otra skill se convierte aparte, no se inventa su contenido acá |
| Frontmatter técnico (`version`, `argument-hint`, `allowed-tools`) | No se traslada, es metadata específica de Claude |
| Nombres propios, rutas de carpeta personal, fuente de datos asumida como única | Genérico o convertido en pregunta al usuario |

### Presupuesto de caracteres por destino

| Destino | Presupuesto del bloque de prompt | Notas |
|---|---|---|
| Instrucciones de un GPT personalizado | ~7.500 (tope real 8.000) | Dejar margen de seguridad de 500 |
| Custom instructions de ChatGPT personal | ~1.400 por campo (dos campos: "sobre vos" / "cómo responder") | Mucho más chico — priorizar Rol + Restricciones + Tono, el resto va a Material de referencia |
| Project / Instrucciones de Claude.ai | Sin tope duro, apuntar a ~4.000 igual | Un prompt muy largo se diluye aunque el campo lo acepte |
| Prompt suelto (primer mensaje de cualquier chat) | Sin límite real, apuntar a ~3.000 | Se puede reforzar con el segundo bloque como mensaje fijado si la herramienta lo permite |

### Prioridad de compresión si el prompt se pasa del presupuesto

**P1 — nunca se saca:** Rol, Tarea (pasos condensados), Objetivo, Restricciones críticas (incluida la de declarar qué opción eligió y por qué, si el proceso original elige entre varias — ver paso 5.1 del Proceso), referencia al Material de referencia.
**P2 — entra si el presupuesto alcanza:** 1-2 ejemplos cortos, Insumos, Formato de salida, Tono.
**P3 — siempre va al Material de referencia, nunca al prompt corto:** ejemplos completos, tablas largas, casos borde, plantillas extensas, explicaciones de varios párrafos. La *explicación* de cada ítem de una enumeración de criterio es P3; la enumeración en sí es P1 y se queda en el prompt (paso 8.1).

Técnicas de recorte, en este orden: párrafos → bullets; tablas anchas → listas clave-valor; secciones parecidas se funden en una; se saca metadata que no aporta ("cuándo se dispara" no hace falta dentro del prompt, ya está pegado a propósito); un ejemplo se reduce a estructura y patrón, no a la pieza completa.

### Por qué el "Recordatorio" final

Una skill vive siempre cargada — sus reglas están presentes en cada turno sin que nadie las repita. Un prompt pegado una sola vez no tiene eso: en una conversación larga el modelo puede perder de vista una restricción que dijo al principio. La línea de recordatorio compensa eso pidiendo una relectura activa antes de cada respuesta, en vez de asumir memoria perfecta de instrucciones lejanas.
