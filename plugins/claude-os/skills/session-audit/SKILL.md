---
name: session-audit
description: Audita la sesion o el sistema del Claude OS y guarda lo aprendido en el archivo dueño. Usar ante 'audita la sesion', 'guarda lo que aprendimos' o al cerrar un trabajo con correcciones.
---

# session-audit: Auditoría del Claude OS
*v5, 12 de noviembre de 2026*

## PASO PREVIO: Fijar la raíz del sistema (obligatorio, ambos modos)

Ubicá la carpeta del Claude OS: la que contiene `CLAUDE.md`, `MEMORY.md` y las estaciones. Guardá su ruta absoluta y usala en todos los comandos de acá en adelante.

Todos los comandos van con `"$RAIZ"` como ruta absoluta, sin `cd`: el `cd` mueve la carpeta de trabajo de la sesión y los links a archivos que se le pasan a Mati dejan de abrir.

```bash
RAIZ="<ruta absoluta de la carpeta del OS>"
ls "$RAIZ/CLAUDE.md" "$RAIZ/MEMORY.md"
```

Si no hay carpeta conectada o no encontrás esos archivos, pedí acceso. **No inventes rutas ni simules el contenido de los archivos.**

Con la raíz fijada, elegí el modo:

```
1. ¿Mati pidió auditar el SISTEMA (los archivos, la estructura, el orden)?
   → Modo Sistema: leé `modo-sistema.md`, en la carpeta de esta skill,
     y seguí desde ahí. No se lee en el Modo Sesión.

2. ¿Pidió auditar la SESIÓN (lo que pasó en esta conversación)?
   → SECCIÓN A (Modo Sesión).

3. ¿La frase admite las dos lecturas ("auditá esto", "revisá cómo venimos")?
   → Preguntá cuál de los dos. El Modo Sistema lee decenas de archivos:
     equivocarse de modo cuesta caro en las dos direcciones.
```

**Regla de destino (aplica a ambos modos):**
La define el `CLAUDE.md` raíz (sección "Memoria"). En resumen: prescriptivo (reglas de comportamiento) → `CLAUDE.md`; factual (estado, decisiones, contactos, datos que cambian) → `MEMORY.md`. Ante un cambio de criterio, gana lo que diga el raíz.

**Regla de dueño (aplica a ambos modos, se aplica antes que la de nivel):**
Cada acción o herramienta tiene sus reglas en un solo archivo, su dueño: el que declara el `CLAUDE.md` raíz (buscá la lista de acciones con su archivo, esté en la sección que esté; si no hay lista, la tabla de Referencias). Ejemplos de acción: mails y mensajes, una plataforma de anuncios, el gestor de tareas, el almacenamiento, credenciales, un tipo de documento. Toda regla, trampa de una herramienta o aprendizaje sobre esa acción va a su dueño, sin importar en qué estación apareció ni qué tan local parezca: "el mail del resumen de una reunión va pegado en el cuerpo" vive en el archivo de mails, no en la estación de reuniones. La regla de nivel decide solo para lo que no tiene dueño.

Si la acción la ejecuta una skill propia (un mail que arma una skill, un documento que genera otra) y la corrección toca algo que esa skill define, el destino es la fuente de la skill en su repo, que se versiona y se publica: si la regla va al dueño pero la skill dice otra cosa, la skill gana en cada corrida y la corrección no sirve. Si la skill no define ese punto, va al dueño. Si una acción que se repite en varias estaciones no tiene dueño todavía, no se reparte: se propone crearlo (📁 en el PASO 4). Motivo: con la regla de nivel sola, cada estación guardaba "su" parte como verdadera solo para ella, y las reglas de mandar un mail terminaron repartidas en la raíz, tres estaciones, un archivo de herramientas y ocho entradas de memoria automática.

**La memoria automática no es un destino.** Claude Code guarda por su cuenta correcciones en `~/.claude/projects/<proyecto>/memory/` durante la sesión, y Cowork no las ve. Ninguna regla se propone para ahí; las que ya estén ahí se migran a su dueño (ver PASO 1, filtro de duplicados).

**Regla de nivel (aplica a ambos modos):**

Una regla vive en el nivel más bajo donde sigue siendo verdadera.

1. **Global**: verdadero siempre, en cualquier estación. Comportamiento e identidad de marca.
2. **Estación**: verdadero solo para ese tipo de trabajo. Aplica a cualquier profundidad: si una estación tiene sub-estaciones o proyectos adentro (`Diseno-web/web-muta-digital/`, `Clientes/Raices-Ypora/`), la misma prueba corre entre padre e hijo. Una regla que vale para un solo proyecto no vive en el `CLAUDE.md` del padre.
3. **Pieza**: verdadero solo para ese tipo de documento (presupuesto, informe, presentación, SOP).
4. **Medio de salida**: verdadero solo al exportar a cierto formato.

Prueba: ¿sigue siendo verdad si dejo de hacer PDFs? ¿Y si dejo de hacer presupuestos? Dos noes seguidos y no es global.

Los niveles 3 y 4 no son un escalón más abajo que el 2: son otro eje. Una regla puede ser de estación y de pieza a la vez ("en los presupuestos de este cliente, al exportar a PDF..."). Cuando pasa, manda el nivel 2: vive en la estación, redactada con la condición de formato adelante.

Una restricción de nivel 3 o 4 se escribe siempre con su condición adelante: "al exportar a PDF, ...". Nunca como prohibición absoluta, porque una prohibición sin condición se aplica también mientras se explora, y ahí no corresponde.

**Global fuera del OS:** si la regla aplica a TODO Claude (no solo a esta carpeta, ej: preferencias de trato, seguridad, permisos), no la escribas en ningún archivo del OS. Marcala como 🌐 GLOBAL y avisale a Mati que corresponde a su CLAUDE.md global privado, que él edita manualmente.

Si dudás, preguntá. Nunca asumir raíz por defecto.

**Regla de fechado (aplica a ambos modos):**
Toda entrada nueva en cualquier `MEMORY.md` se prefija con `[YYYY-MM]` (ej: `[2026-07] El contrato de Baguette arranca en julio 2026.`). Sin el prefijo no hay forma de detectar datos viejos en futuras auditorías. Las reglas de `CLAUDE.md` no se fechan.

**Umbrales (aplican a ambos modos):**
- `CLAUDE.md` (raíz o estación) → **200 líneas** (la guía de Anthropic: "target under 200 lines per CLAUDE.md file", https://code.claude.com/docs/en/memory). Un archivo de dueño que se lee bajo demanda no tiene umbral de líneas: su tope es el de caracteres de la misma guía.
- `MEMORY.md` (raíz o estación) → **150 líneas**
- **Contá siempre con `wc -l`. Nunca estimes a ojo.**
- Si `MEMORY.md` raíz supera su umbral, el procedimiento definido en el raíz es: comprimir y mover lo viejo a `archivo-historico.md`. Proponé esa compresión (mostrando qué se movería) en vez de solo avisar.

---

## Cómo se escribe cada entrada (aplica a ambos modos)

**Filtro previo.** Una regla se propone solo si pasa las dos:

1. ¿Un asistente competente lo haría igual sin que se lo digan? → no se escribe.
2. ¿Ya lo dicen las instrucciones globales, las preferencias personales de Mati, o una regla que ya está en un nivel superior? → no se escribe.

3. ¿Es una corrección puntual que Mati hizo una sola vez, sin enunciarla como preferencia? → no va como regla en RECOMIENDO. Se extrae el criterio de fondo y va a TU DECISIÓN con la nota "corregido una vez". Convertir cada corrección en regla es lo que infla los archivos; la regla se justifica cuando corrige lo mismo por segunda vez o lo dice como norma ("siempre", "nunca", "de ahora en adelante").

Los hechos que van a `MEMORY.md` pasan un filtro propio: ¿cambia cómo se trabaja la próxima vez? Una decisión que condiciona trabajo futuro, un contacto, un rol o una spec de canon, sí. El estado de un proyecto, qué se hizo, dónde quedó un archivo o un dato suelto que se desactualiza, no: eso vive en el LEEME o el HANDOFF del proyecto y acá sería una anécdota.

**Forma de lo que pasa el filtro:**

- Cada regla dice tres cosas, en una línea o dos como mucho: cuándo aplica, qué hacer y el motivo en una frase. "Un anuncio armado por API se revisa en el Administrador antes de darlo por listo: el 7/11 la API devolvía Instagram cargado y el Administrador lo mostraba vacío."
- El motivo es lo que se rompe si la regla no se cumple, y si hubo un caso real es ese caso. No es el razonamiento que te llevó a proponerla. Sin motivo la regla se obedece literal y falla en el primer caso que no se parece al original. Si Mati no dio el porqué, el motivo es el caso concreto (qué pasó, en qué trabajo, cuándo); nunca se inventa una razón. Si ni el caso alcanza para entender cuándo aplica, la entrada va a TU DECISIÓN con la pregunta del porqué.
- Una negativa se escribe como explicación, no como orden seca: qué se hace en su lugar y qué pasa si se hace lo otro. "Las imágenes de un mail van adjuntas, nunca embebidas: Gmail y Outlook bloquean el base64 y queda un cuadrito roto." No "NUNCA embeber imágenes."
- Mínima expresión sin perder especificidad. Prueba: sacá una palabra; si la regla sigue diciendo cuándo, qué y por qué, sobraba. Si al acortar se pierde la condición o el dato concreto (la herramienta, el canal, el número, el nombre), te pasaste: una regla genérica no cambia ninguna conducta.
- Imperativo directo. "Los mails a clientes arrancan con Hola!". Nunca "Se detectó que Mati prefiere un saludo informal, por lo cual conviene que los mails arranquen con Hola!".
- Sin meta-comentarios: nada de "esta regla surge de", "conviene tener en cuenta que", "nota:", "por qué importa", "a partir de ahora". El archivo entero es "a partir de ahora".
- Sin fecha ni autoría en `CLAUDE.md`. Las entradas de `MEMORY.md` llevan `[YYYY-MM]` y un breve contexto para remitir al hecho.
- Si una regla nueva se parece a una existente, se reescribe la existente. No se apilan dos.

Antes de mostrar el PASO 4 o el SYS-PASO 3, releé cada entrada y borrá toda oración que no sea la instrucción o su motivo.

**No fabriques hallazgos.** Una auditoría que no encuentra nada es un resultado válido y frecuente. Inventar una regla para que el reporte se vea productivo ensucia los archivos con ruido que después hay que sacar, y es peor que no haber corrido la auditoría.

---

# SECCIÓN A: MODO SESIÓN

---

## PASO 0: Leer el sistema antes de escanear

1. **Descubrí las estaciones desde el filesystem**, para saber qué destinos existen:

```bash
find "$RAIZ" \( -name "CLAUDE.md" -o -name "MEMORY.md" \) \
  -not -path "*/node_modules/*" -not -path "$RAIZ/_archivo/*" -not -path "$RAIZ/_to_delete/*"
```

2. **Leé `CLAUDE.md` y `MEMORY.md` raíz.** Contá líneas con `wc -l` y compará contra los umbrales.
3. **Leé los archivos de referencia que se usaron en esta sesión.** Están en la tabla de Referencias del `CLAUDE.md` raíz: leé esa tabla y tomá de ahí los que apliquen a lo que se trabajó hoy. No los leas todos siempre: leé los que la sesión tocó, más el dueño de cada acción que aparezca en un hallazgo.
4. **Listá lo que la memoria automática guardó en esta sesión**, si la sesión la tiene: `find ~/.claude/projects/*/memory -name "*.md" -newermt "$(date +%Y-%m-%d)"`. Cada archivo nuevo o modificado hoy es una corrección que Claude Code guardó por su cuenta: entra al PASO 1 como hallazgo, aunque la conversación no la muestre. En Cowork esa carpeta no se ve: decilo en una línea y seguí.
5. Los `CLAUDE.md` de las estaciones no se leen todavía. Solo los que resulten destino de un hallazgo (ver filtro del PASO 1).

El estado del Mapa de Enrutamiento no se audita en este modo. Detectar carpetas sin mapear es trabajo del Modo Sistema: acá solo generaría una lista fija que se repite en cada sesión y no tiene dónde ir.

---

## PASO 1: Escanear la conversación

Recorrés la conversación completa buscando estas cinco señales:

### A. Correcciones
Mati corrigió algo que Claude produjo: cambió una palabra, reescribió una sección, ajustó un formato, o dijo "no, hacelo así". Una corrección muestra un criterio, no necesariamente una regla: pasa por el punto 3 del filtro previo.

**Qué buscar:** Momentos donde editó, rechazó o reescribió el output. Preguntate: ¿qué criterio de fondo motivó el cambio, y ya lo había corregido antes (en esta sesión, o figura en los archivos o en la memoria automática)?

**Ejemplo:** Cambió "Estimado cliente" por "Hola!" → Regla: los mails a clientes arrancan con "Hola!" o "Hola [nombre]!"

→ Generalmente va a **CLAUDE.md** (es una regla de comportamiento)

---

### B. Preferencias explícitas
Mati declaró una preferencia directamente. Palabras clave: "siempre", "nunca", "prefiero", "de ahora en adelante", "no hagas más", "me gusta cuando".

**Ejemplo:** "No uses negritas en el cuerpo del texto." / "Las fórmulas de Sheets siempre con punto y coma."

→ Va a **CLAUDE.md** (es prescriptivo)

---

### C. Decisiones
Mati tomó una decisión que afecta trabajo futuro: eligió una opción, fijó una fecha, estableció una regla para un proyecto, resolvió una ambigüedad.

**Ejemplo:** "Usemos el formato de propuesta corta para clientes nuevos." / "El contrato de Baguette arranca en julio."

→ Va a **MEMORY.md** (es factual, puede cambiar): con prefijo `[YYYY-MM]`

---

### D. Contexto nuevo
Mati compartió información sobre su negocio, equipo, clientes o proyectos. Datos de contacto, estados, cambios de estructura, novedades.

**Ejemplo:** "David es el dueño de Baguette, Maru lleva las redes." / "Luciano ahora está a cargo de los informes mensuales."

→ Va a **MEMORY.md** (es factual): con prefijo `[YYYY-MM]`

---

### E. Procesos repetibles
En la sesión se ejecutó un proceso que probablemente se repita: armar un tipo de informe, un flujo de onboarding de cliente, una secuencia de pasos que funcionó.

**Ejemplo:** Se armó por primera vez el flujo completo de análisis mensual de campañas para un cliente, con pasos claros que van a repetirse cada mes.

→ No va a CLAUDE.md ni MEMORY.md. Se marca como **candidato a SOP** y se sugiere documentarlo con la skill `muta-sop` (o como skill nueva si es un proceso de Claude). Solo marcarlo si el proceso tiene 3+ pasos y va a repetirse: un one-off no califica.

---

**Filtro de duplicados y contradicciones (antes de continuar):**

1. Para cada hallazgo, identificá su destino tentativo: primero la regla de dueño; la de nivel solo si la acción no tiene dueño.
2. **Leé el archivo destino real**, y si la acción la ejecuta una skill propia, también su `SKILL.md` (ver regla de dueño). Si el destino es una estación, leé su `CLAUDE.md`/`MEMORY.md` ahora. Sin leer el destino no podés deduplicar.
3. **Leé también el nivel de arriba.** Si el hallazgo va a una estación, chequeá que la regla no exista ya en raíz o en un archivo de referencia. Una regla que ya está arriba no se copia abajo.
4. **Chequeá la memoria automática de Code**, si la sesión la tiene (el índice `MEMORY.md` de `~/.claude/projects/<proyecto>/memory/`, que ya viene cargado en el contexto, más lo que listó el PASO 0). Toda regla que esté ahí, nueva de hoy o vieja que el hallazgo toca, va a RECOMIENDO como migración: el contenido completo (condiciones, números, casos y motivo, sin resumir de más) pasa al dueño; si el dueño ya tiene la regla pero con menos datos que la memoria, se completa el dueño con lo que falta. Después el archivo de memoria se mueve a `_to_delete/` de la raíz del OS y su línea sale del índice. Dejarla en los dos lados es lo que desincroniza: la memoria automática la vuelve a cargar en cada sesión de Code mientras Cowork no la ve. En Cowork esa carpeta no se ve: decilo en una línea y seguí.
5. **Duplicado:** si el hallazgo ya está escrito (igual o equivalente), descartalo. Solo surfaceás lo genuinamente nuevo.
6. **Contradicción:** si el hallazgo contradice una regla o dato existente (ej: el archivo dice "sin negritas" y Mati pidió negritas en títulos esta sesión), NO lo agregues como regla nueva. Marcalo como ⚔️ CONFLICTO y presentalo así en el PASO 4: qué dice el archivo, qué dice la sesión, y preguntá cuál gana. La resolución típica es reemplazar la regla vieja, no apilar las dos.

---

## PASO 2: Routing: ¿dónde va cada hallazgo?

Para cada hallazgo, aplicá este árbol de decisión **en orden**:

```
¿Aplica a TODO Claude, más allá de esta carpeta/OS?
  → SÍ: marcarlo 🌐 GLOBAL (solo avisar, no escribir en el OS)
  → NO: ¿Es sobre una acción o herramienta que tiene dueño (regla de dueño)?
      → SÍ: va a ese archivo, en la sección que corresponda, aunque la
            sesión haya sido en otra estación.
      → NO: ¿Es sobre una acción o herramienta que se usa desde dos o más
             estaciones (mandar mensajes, cargar tareas, subir archivos,
             una plataforma)?
          → SÍ: 📁 proponer su archivo dueño. No va a la estación de hoy
                ni a raíz.
          → NO: ¿Aplica solo cuando se trabaja en un dominio o proyecto específico?
              → SÍ: ¿Existe una estación para ese dominio?
                  → SÍ: va a [estación]/CLAUDE.md o [estación]/MEMORY.md.
                        Si la estación tiene sub-estaciones, bajá hasta la más
                        profunda donde la regla siga siendo verdadera.
                  → NO: anotarlo como "candidato a estación nueva" (ver PASO 3)
              → NO: ¿Aplica solo a un tipo de documento o solo al exportar
                     a cierto formato?
                  → SÍ: va al archivo que gobierna ese formato (el sistema de
                        diseño declarado en la tabla de Referencias del raíz),
                        en su sección de ese nivel, con la condición adelante.
                        Si esa sección no existe, marcalo 🧩 SIN DESTINO y
                        preguntá antes de escribir.
                  → NO: ¿Aplica realmente en cualquier tarea sin importar el contexto?
                      → SÍ: va a raíz
                      → NO: preguntá a Mati antes de decidir
```

Si un hallazgo entra por la rama de estación y además tiene condición de formato, no vuelvas a subirlo: queda en la estación con la condición escrita adelante.

**Señales de que algo pertenece al dueño de una acción, no a la estación de hoy ni a raíz:**
- Menciona una herramienta o canal concreto (una plataforma de anuncios, el gestor de tareas, una planilla, el mail, el chat del equipo)
- Describe cómo se hace una acción que también se hace desde otras estaciones (mandar un mail, cargar una tarea, subir un archivo, armar un anuncio)
- Es una trampa o un límite de un conector o una API

**Señales de que algo pertenece a una estación específica:**
- Refiere a un tipo de trabajo que solo se hace ahí (armar un guion, editar un video, costear un plan)
- Aplica solo dentro de ese dominio y no nombra una herramienta compartida
- La estación ya tiene secciones relacionadas en su CLAUDE.md

**Señales de que algo va genuinamente a raíz:**
- Aplica independientemente de la tarea (tono general de respuesta, idioma, formato de confirmaciones)
- Describe cómo tratar a Mati, cómo reportarle, o cómo manejar credenciales y accesos
- Empieza con "en toda herramienta", "en cualquier", "siempre que"
- Mati lo enunció como instrucción global del OS ("de ahora en adelante, en todo...")

**Antes de escribir en una estación:** verificá que la regla no sea verdadera fuera de esa carpeta. Si lo es, va al dueño de la acción; sube a raíz solo si no es de ninguna acción en particular (cómo tratar a Mati, cómo reportar, credenciales).

---

## PASO 3: Detección de escala

Antes de proponer dónde guardar, chequeá estas dos condiciones:

### 3A. ¿Raíz se está inflando?

- Si `CLAUDE.md` raíz supera **200 líneas**: emitilo como 📈 ESCALA en el PASO 4 y preguntá si Mati quiere que identifiques qué secciones podrían moverse a una estación antes de agregar más. No guardes nada en raíz hasta que confirme.
- Si `MEMORY.md` raíz supera **150 líneas**: emitilo como 📈 ESCALA y proponé comprimir y mover lo viejo a `archivo-historico.md`, mostrando qué entradas se moverían. Guardá lo nuevo recién después de resolver la compresión.

### 3B. ¿Hay candidatos para una estación nueva?

Si del PASO 2 quedaron **2 o más hallazgos** marcados como "candidato a estación nueva" en el mismo dominio:

→ Proponé crear la estación antes de guardar. Formato de propuesta:

```
📁 ESTACIÓN SUGERIDA: [Nombre-estacion]/
   ├── CLAUDE.md                → Identidad, Recursos, Flujo de trabajo, Reglas editoriales
   ├── MEMORY.md                → Contactos y Decisiones clave del dominio
   └── Recursos de [Nombre]/    → (vacía por ahora)

Hallazgos que irían acá:
→ [hallazgo 1]
→ [hallazgo 2]

¿Creamos la estación primero y después guardamos ahí?
```

La estructura exacta está en `00-recursos/plantilla-estacion.md`. Seguila al pie de la letra, incluida la convención de nombre de carpeta.

---

## PASO 4: Presentar el análisis antes de escribir

Mostrá todo lo que encontraste ANTES de modificar cualquier archivo.

Numerá los hallazgos y separalos en dos grupos. La distinción le ahorra a Mati leer con la misma atención lo que es obvio y lo que no: si todo viene mezclado, o revisa todo con lupa o aprueba todo en bloque, y las dos cosas son malas. Los números son para que pueda aprobar parcial ("va el 1 y el 4, el 2 no").

El destino incluye siempre la sección, no solo el archivo: es parte de lo que se está aprobando.

```
HALLAZGOS DE LA SESIÓN

RECOMIENDO: la regla es clara y el destino no admite discusión

1. [destino] → [archivo] → [sección]
   → [contenido exacto que se va a agregar]

2. [destino] → [archivo] → [sección]
   → [contenido exacto que se va a agregar]

TU DECISIÓN: hay un juicio de por medio, dos destinos posibles, falta el motivo,
o la redacción admite varias formas

3. [destino] → [archivo] → [sección]
   → [contenido exacto que se va a agregar]
   → [la duda concreta, en una línea]

🌐 GLOBAL: [si los hay: van al CLAUDE.md global privado, Mati los agrega a mano]
⚔️ CONFLICTOS: [si los hay: qué dice el archivo vs. qué dice la sesión, y cuál gana]
🧩 SIN DESTINO: [si los hay: reglas de pieza o de exportación sin sección donde vivir]
📋 CANDIDATOS A SOP: [si los hay: proceso detectado + sugerencia de documentarlo con muta-sop]
📈 ESCALA: [si las hay: archivos sobre el umbral, con el número de líneas]
📁 ESTACIONES O ARCHIVOS DUEÑOS NUEVOS SUGERIDOS: [si los hay, con la acción que cubren y qué reglas sueltas se mueven ahí]
```

Esperá confirmación. Mati puede aprobar todo, parte o nada, por número. Nada se escribe hasta que lo diga explícitamente, incluidos los del bloque RECOMIENDO.

---

## PASO 5: Escribir en los archivos correctos

Según la confirmación:

- **CLAUDE.md** (raíz o estación): agregarlo en la sección aprobada. No duplicar. Si se resolvió un ⚔️ CONFLICTO reemplazando una regla vieja, eliminá la regla vieja al agregar la nueva.
- **MEMORY.md** (raíz o estación): agregarlo en la sección aprobada con prefijo `[YYYY-MM]`. Verificar con `wc -l` que no supere 150 líneas. Si supera, proponer compresión: la de raíz va a `archivo-historico.md`; la de una estación va a un `archivo-historico.md` propio dentro de esa estación, para no mezclar dominios.
- **Regla de pieza o de medio de salida:** va al archivo que gobierna ese formato, en la sección de ese nivel, con la condición adelante. Si no existe la sección, no la improvises en otro lado: quedó marcada como 🧩 SIN DESTINO y se resuelve con Mati.
- **Si se crea una estación nueva:** crearla con la estructura de `00-recursos/plantilla-estacion.md` y **agregar la fila al Mapa de Enrutamiento del raíz en el mismo acto** (la fila ya fue mostrada y aprobada en PASO 4). No delegar esa actualización a Mati.

Al terminar, confirmar qué se escribió y en qué archivo.

---

## Reglas de comportamiento (Modo Sesión)

- **Nunca escribir sin mostrar primero** lo que se va a agregar y esperar confirmación
- **Nivel más bajo donde la regla sigue siendo verdadera.** Si es verdadera fuera de la carpeta, sube.
- **No duplicar**: si algo ya está en el archivo o en el nivel de arriba, no agregarlo de nuevo
- **No apilar contradicciones**: un conflicto se resuelve preguntando, no agregando la regla nueva al lado de la vieja
- **Sé quirúrgico**: solo lo genuinamente nuevo de esta sesión
- Si la sesión no generó nada nuevo: decirlo directo ("No encontré nada nuevo para guardar")
- Si se crea una estación nueva: actualizar el Mapa de Enrutamiento en raíz como parte del mismo cambio

**Al finalizar el modo sesión**, evaluá si se cumplen dos o más de estas condiciones:
- Raíz está cerca o sobre el umbral de líneas
- Varios hallazgos fueron a raíz porque no había estación disponible
- Se propuso crear una estación nueva
- Hubo dudas de nivel que se resolvieron yendo a raíz por defecto
- Algún hallazgo quedó marcado como 🧩 SIN DESTINO

Si se cumplen dos o más → agregar al final del output:
> "¿Querés que también haga una auditoría del sistema completo? Encontré señales de que puede haber contenido mal ubicado o que raíz se está inflando."

