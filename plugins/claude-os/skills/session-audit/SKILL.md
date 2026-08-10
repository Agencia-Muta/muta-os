---
name: session-audit
description: Audita la sesion o el sistema de archivos del Claude OS y guarda lo aprendido en el archivo correcto. Usar ante 'audita la sesion', 'guarda lo que aprendimos' o 'antes de cerrar guarda'.
---

# session-audit — Auditoría del Claude OS
*v3 — 7 de agosto de 2026*

## PASO PREVIO — Fijar la raíz del sistema (obligatorio, ambos modos)

Ubicá la carpeta del Claude OS: la que contiene `CLAUDE.md`, `MEMORY.md` y las estaciones. Guardá su ruta absoluta y usala en todos los comandos de acá en adelante.

Esto no es burocracia: los `find` de esta skill usan rutas relativas, y el directorio de trabajo por defecto de la shell no es la carpeta del OS. Corridos desde otro lado devuelven archivos de más, sin error y sin aviso.

```bash
RAIZ="<ruta absoluta de la carpeta del OS>"
cd "$RAIZ" && ls CLAUDE.md MEMORY.md
```

Si no hay carpeta conectada o no encontrás esos archivos, pedí acceso. **No inventes rutas ni simules el contenido de los archivos.**

Con la raíz fijada, elegí el modo:

```
1. ¿Mati pidió auditar el SISTEMA (los archivos, la estructura, el orden)?
   → SECCIÓN B (Modo Sistema).

2. ¿Pidió auditar la SESIÓN (lo que pasó en esta conversación)?
   → SECCIÓN A (Modo Sesión).

3. ¿La frase admite las dos lecturas ("auditá esto", "revisá cómo venimos")?
   → Preguntá cuál de los dos. El Modo Sistema lee decenas de archivos:
     equivocarse de modo cuesta caro en las dos direcciones.
```

**Regla de destino (aplica a ambos modos):**
La define el `CLAUDE.md` raíz (sección "Memoria"). En resumen: prescriptivo (reglas de comportamiento) → `CLAUDE.md`; factual (estado, decisiones, contactos, datos que cambian) → `MEMORY.md`. Ante un cambio de criterio, gana lo que diga el raíz.

**Regla de nivel (aplica a ambos modos):**

Una regla vive en el nivel más bajo donde sigue siendo verdadera.

1. **Global**: verdadero siempre, en cualquier estación. Comportamiento e identidad de marca.
2. **Estación**: verdadero solo para ese tipo de trabajo. Aplica a cualquier profundidad: si una estación tiene sub-estaciones o proyectos adentro (`Herramientas-web/muta-sessions/`, `Clientes/Raices-Ypora/`), la misma prueba corre entre padre e hijo. Una regla que vale para un solo proyecto no vive en el `CLAUDE.md` del padre.
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
- `CLAUDE.md` (raíz o estación) → **250 líneas**
- `MEMORY.md` (raíz o estación) → **150 líneas**
- **Contá siempre con `wc -l`. Nunca estimes a ojo.**
- Si `MEMORY.md` raíz supera su umbral, el procedimiento definido en el raíz es: comprimir y mover lo viejo a `archivo-historico.md`. Proponé esa compresión (mostrando qué se movería) en vez de solo avisar.

---

## Cómo se escribe cada entrada (aplica a ambos modos)

**Filtro previo.** Una regla se propone solo si pasa las dos:

1. ¿Un asistente competente lo haría igual sin que se lo digan? → no se escribe.
2. ¿Ya lo dicen las instrucciones globales, las preferencias personales de Mati, o una regla que ya está en un nivel superior? → no se escribe.

Los hechos que van a `MEMORY.md` no pasan por este filtro: se escriben la primera vez.

**Forma de lo que pasa el filtro:**

- Una regla, una línea. Dos solo si la excepción es parte de la regla.
- Imperativo directo. "Los mails a clientes arrancan con Hola!". Nunca "Se detectó que Mati prefiere un saludo informal, por lo cual conviene que los mails arranquen con Hola!".
- Sin el porqué. El razonamiento que te llevó a la regla se descarta, no se escribe.
- Sin meta-comentarios: nada de "esta regla surge de", "conviene tener en cuenta que", "nota:", "por qué importa", "a partir de ahora". El archivo entero es "a partir de ahora".
- Sin fecha ni autoría en `CLAUDE.md`. Las entradas de `MEMORY.md` sí llevan `[YYYY-MM]`, y ahí el contexto sí va, porque son hechos y no órdenes.
- Si una regla nueva se parece a una existente, se reescribe la existente. No se apilan dos.

Antes de mostrar el PASO 4 o el SYS-PASO 3, releé cada entrada y borrá toda oración que no sea la instrucción en sí.

**No fabriques hallazgos.** Una auditoría que no encuentra nada es un resultado válido y frecuente. Inventar una regla para que el reporte se vea productivo ensucia los archivos con ruido que después hay que sacar, y es peor que no haber corrido la auditoría.

---

# SECCIÓN A — MODO SESIÓN

---

## PASO 0 — Leer el sistema antes de escanear

1. **Descubrí las estaciones desde el filesystem**, para saber qué destinos existen:

```bash
find . \( -name "CLAUDE.md" -o -name "MEMORY.md" \) \
  -not -path "*/node_modules/*" -not -path "./_archivo/*" -not -path "./_to_delete/*"
```

2. **Leé `CLAUDE.md` y `MEMORY.md` raíz.** Contá líneas con `wc -l` y compará contra los umbrales.
3. **Leé los archivos de referencia que se usaron en esta sesión.** Están en la tabla de Referencias del `CLAUDE.md` raíz: leé esa tabla y tomá de ahí los que apliquen a lo que se trabajó hoy. No leas los seis siempre: leé los que la sesión tocó, más el que sea destino tentativo de algún hallazgo.
4. Los `CLAUDE.md` de las estaciones no se leen todavía. Solo los que resulten destino de un hallazgo (ver filtro del PASO 1).

El estado del Mapa de Enrutamiento no se audita en este modo. Detectar carpetas sin mapear es trabajo del Modo Sistema: acá solo generaría una lista fija que se repite en cada sesión y no tiene dónde ir.

---

## PASO 1 — Escanear la conversación

Recorrés la conversación completa buscando estas cinco señales:

### A. Correcciones
Mati corrigió algo que Claude produjo: cambió una palabra, reescribió una sección, ajustó un formato, o dijo "no, hacelo así". Cada corrección revela una regla implícita.

**Qué buscar:** Momentos donde editó, rechazó o reescribió el output. Preguntate: ¿qué preferencia subyacente motivó el cambio?

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

→ Va a **MEMORY.md** (es factual, puede cambiar) — con prefijo `[YYYY-MM]`

---

### D. Contexto nuevo
Mati compartió información sobre su negocio, equipo, clientes o proyectos. Datos de contacto, estados, cambios de estructura, novedades.

**Ejemplo:** "David es el dueño de Baguette, Maru lleva las redes." / "Luciano ahora está a cargo de los informes mensuales."

→ Va a **MEMORY.md** (es factual) — con prefijo `[YYYY-MM]`

---

### E. Procesos repetibles
En la sesión se ejecutó un proceso que probablemente se repita: armar un tipo de informe, un flujo de onboarding de cliente, una secuencia de pasos que funcionó.

**Ejemplo:** Se armó por primera vez el flujo completo de análisis mensual de campañas para un cliente, con pasos claros que van a repetirse cada mes.

→ No va a CLAUDE.md ni MEMORY.md. Se marca como **candidato a SOP** y se sugiere documentarlo con la skill `muta-sop` (o como skill nueva si es un proceso de Claude). Solo marcarlo si el proceso tiene 3+ pasos y va a repetirse: un one-off no califica.

---

**Filtro de duplicados y contradicciones (antes de continuar):**

1. Para cada hallazgo, identificá su destino tentativo según la regla de nivel.
2. **Leé el archivo destino real.** Si el destino es una estación, leé su `CLAUDE.md`/`MEMORY.md` ahora. Sin leer el destino no podés deduplicar.
3. **Leé también el nivel de arriba.** Si el hallazgo va a una estación, chequeá que la regla no exista ya en raíz o en un archivo de referencia. Una regla que ya está arriba no se copia abajo.
4. **Duplicado:** si el hallazgo ya está escrito (igual o equivalente), descartalo. Solo surfaceás lo genuinamente nuevo.
5. **Contradicción:** si el hallazgo contradice una regla o dato existente (ej: el archivo dice "sin negritas" y Mati pidió negritas en títulos esta sesión), NO lo agregues como regla nueva. Marcalo como ⚔️ CONFLICTO y presentalo así en el PASO 4: qué dice el archivo, qué dice la sesión, y preguntá cuál gana. La resolución típica es reemplazar la regla vieja, no apilar las dos.

---

## PASO 2 — Routing: ¿dónde va cada hallazgo?

Para cada hallazgo, aplicá este árbol de decisión **en orden**:

```
¿Aplica a TODO Claude, más allá de esta carpeta/OS?
  → SÍ: marcarlo 🌐 GLOBAL (solo avisar, no escribir en el OS)
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

**Señales de que algo pertenece a una estación específica:**
- Menciona un canal concreto (Meta Ads, Asana, Sheets, Gmail, etc.)
- Refiere a un tipo de tarea específica (redactar mails, analizar campañas, armar scripts)
- Aplica solo dentro de ese dominio
- La estación ya tiene secciones relacionadas en su CLAUDE.md

**Señales de que algo va genuinamente a raíz:**
- Aplica independientemente de la tarea (tono general de respuesta, idioma, formato de confirmaciones)
- Describe cómo tratar a Mati, cómo reportarle, o cómo manejar credenciales y accesos
- Empieza con "en toda herramienta", "en cualquier", "siempre que"
- Mati lo enunció como instrucción global del OS ("de ahora en adelante, en todo...")

**Antes de escribir en una estación:** verificá que la regla no sea verdadera fuera de esa carpeta. Si lo es, sube.

---

## PASO 3 — Detección de escala

Antes de proponer dónde guardar, chequeá estas dos condiciones:

### 3A. ¿Raíz se está inflando?

- Si `CLAUDE.md` raíz supera **250 líneas**: emitilo como 📈 ESCALA en el PASO 4 y preguntá si Mati quiere que identifiques qué secciones podrían moverse a una estación antes de agregar más. No guardes nada en raíz hasta que confirme.
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

## PASO 4 — Presentar el análisis antes de escribir

Mostrá todo lo que encontraste ANTES de modificar cualquier archivo.

Numerá los hallazgos y separalos en dos grupos. La distinción le ahorra a Mati leer con la misma atención lo que es obvio y lo que no: si todo viene mezclado, o revisa todo con lupa o aprueba todo en bloque, y las dos cosas son malas. Los números son para que pueda aprobar parcial ("va el 1 y el 4, el 2 no").

El destino incluye siempre la sección, no solo el archivo: es parte de lo que se está aprobando.

```
HALLAZGOS DE LA SESIÓN

RECOMIENDO — la regla es clara y el destino no admite discusión

1. [destino] → [archivo] → [sección]
   → [contenido exacto que se va a agregar]

2. [destino] → [archivo] → [sección]
   → [contenido exacto que se va a agregar]

TU DECISIÓN — hay un juicio de por medio, dos destinos posibles,
o la redacción admite varias formas

3. [destino] → [archivo] → [sección]
   → [contenido exacto que se va a agregar]
   → [la duda concreta, en una línea]

🌐 GLOBAL: [si los hay — van al CLAUDE.md global privado, Mati los agrega a mano]
⚔️ CONFLICTOS: [si los hay — qué dice el archivo vs. qué dice la sesión, y cuál gana]
🧩 SIN DESTINO: [si los hay — reglas de pieza o de exportación sin sección donde vivir]
📋 CANDIDATOS A SOP: [si los hay — proceso detectado + sugerencia de documentarlo con muta-sop]
📈 ESCALA: [si las hay — archivos sobre el umbral, con el número de líneas]
📁 ESTACIONES NUEVAS SUGERIDAS: [si las hay]
```

Esperá confirmación. Mati puede aprobar todo, parte o nada, por número. Nada se escribe hasta que lo diga explícitamente, incluidos los del bloque RECOMIENDO.

---

## PASO 5 — Escribir en los archivos correctos

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
- **No duplicar** — si algo ya está en el archivo o en el nivel de arriba, no agregarlo de nuevo
- **No apilar contradicciones** — un conflicto se resuelve preguntando, no agregando la regla nueva al lado de la vieja
- **Sé quirúrgico** — solo lo genuinamente nuevo de esta sesión
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

---

# SECCIÓN B — MODO SISTEMA

Leés el Claude OS y diagnosticás si el contenido está bien organizado, bien ubicado y si la estructura escala correctamente.

## Alcance

Descubrí el sistema desde el filesystem. La auditoría se adapta al sistema que encuentra, sean dos estaciones o veinte.

```bash
cd "$RAIZ"
find . \( -name "CLAUDE.md" -o -name "MEMORY.md" \) \
  -not -path "*/node_modules/*" \
  -not -path "./_archivo/*" \
  -not -path "./_to_delete/*" \
  -not -path "./Artifacts/*" \
  -not -path "./Scheduled/*"
```

Auditá todos los que devuelva, más:

1. Los archivos de referencia listados en la tabla de Referencias del `CLAUDE.md` raíz
2. El nivel raíz de la carpeta, para detectar archivos sueltos

Se audita todo `CLAUDE.md` y todo `MEMORY.md` que devuelva ese comando, sin importar en qué carpeta esté. Lo que no se audita es el **contenido**: los datos de las carpetas personales y los archivos de trabajo por cliente.

### Si el `find` devuelve más de 30 archivos

Una pasada que no entra en contexto termina en un diagnóstico incompleto que parece completo. Avisá el número y partí la auditoría en rondas:

- **Ronda 0**: raíz (`CLAUDE.md`, `MEMORY.md`) + todos los archivos de referencia.
- **Una ronda por carpeta de primer nivel**, incluyendo sus sub-estaciones.

En cada ronda, además del diagnóstico, anotá un **índice de reglas**: una línea por regla encontrada, con su archivo y su nivel aparente. Ese índice es lo que después permite detectar duplicación y contradicciones entre rondas, que es justamente lo que se pierde al partir.

Terminadas las rondas, corré una **ronda de cierre** sobre el índice acumulado: buscá reglas repetidas entre rondas, reglas contradictorias, y reglas que en su ronda parecían de estación pero aparecen en tres estaciones distintas (esas son globales). El reporte del SYS-PASO 3 se emite una sola vez, al final, consolidado.

---

## SYS-PASO 1 — Relevamiento del sistema

1. `CLAUDE.md` raíz — contá líneas con `wc -l`, identificá secciones
2. `MEMORY.md` raíz — contá líneas con `wc -l`, identificá secciones
3. Para cada `CLAUDE.md`/`MEMORY.md` que devolvió el `find`: leelo y contá líneas con `wc -l`
4. Para cada archivo de referencia de la tabla del raíz: leelo y contá líneas
5. Listá el nivel raíz de la carpeta (`ls`) y anotá los archivos sueltos que no sean CLAUDE.md, MEMORY.md o archivo-historico.md
6. Listá **todas las carpetas del sistema, a cualquier profundidad**, tengan o no `CLAUDE.md`, y compará contra el Mapa de Enrutamiento: anotá qué carpetas están en disco y no en el mapa, y qué filas del mapa apuntan a carpetas que no existen. Las sub-estaciones cuentan: una carpeta con `CLAUDE.md` propio dentro de otra estación necesita su fila igual.

   **La infraestructura no se enruta y no es hallazgo:** `_archivo/`, `_to_delete/`, `Artifacts/`, `Scheduled/` y `00-recursos/`. No son estaciones de trabajo, no van al Mapa de Enrutamiento, y reportarlas cada vez es ruido permanente.

Construí internamente esta tabla antes de continuar:

```
ARCHIVO                        | LÍNEAS | UMBRAL | ESTADO
-------------------------------|--------|--------|--------
CLAUDE.md (raíz)               |  ___   |  250   |  ✅/⚠️
MEMORY.md (raíz)               |  ___   |  150   |  ✅/⚠️
[estación]/CLAUDE.md           |  ___   |  250   |  ✅/⚠️
[estación]/MEMORY.md           |  ___   |  150   |  ✅/⚠️
[archivo de referencia]        |  ___   |   —    |  ✅/⚠️
```

---

## SYS-PASO 2 — Diagnóstico de contenido

Con los archivos leídos, buscá estos once problemas:

### 🔴 Contenido mal ubicado en raíz
Reglas o datos en `CLAUDE.md`/`MEMORY.md` raíz que pertenecen a una estación existente.

**Señal:** el contenido menciona un dominio específico (Meta Ads, Asana, mails, etc.) y existe una estación para ese dominio.

**Acción:** proponer moverlo a la estación correspondiente.

---

### 🔴 Regla global atrapada en una estación
Una regla escrita en un `CLAUDE.md` de estación que es verdadera en cualquier otra.

**Señal:** dice "en toda herramienta", "siempre", "nunca", o describe cómo tratar a Mati, cómo reportar, o cómo manejar credenciales y accesos. También: la misma regla aparece en tres estaciones distintas.

**Acción:** proponer subirla, dejando en la estación solo la parte específica del dominio.

---

### 🔴 Regla de un solo proyecto en el CLAUDE.md del padre
Una regla en una estación que solo es verdadera para uno de sus sub-proyectos.

**Señal:** nombra un stack, una herramienta o un caso de uso que los otros sub-proyectos de esa carpeta no comparten, o los contradice.

**Acción:** proponer bajarla al sub-proyecto.

---

### 🔴 Mezcla de dominios
Un archivo cuyo tema es X y que además legisla sobre Y.

**Señal:** un documento de diseño que define tono de voz, un archivo de voz que define formato, un CLAUDE.md de cliente que define pipeline de skills.

**Acción:** proponer mover la parte ajena a su dueño declarado, dejando una línea de remisión.

---

### 🔴 Restricción condicional escrita como prohibición absoluta
Una regla de nivel 3 o 4 (pieza o medio de salida) redactada sin su condición, que por eso aplica siempre.

**Señal:** una prohibición técnica cuya causa es un formato de salida, un visor o un tipo de documento, pero el texto no lo dice.

**Acción:** proponer reescribirla con la condición adelante, y moverla al archivo o sección que gobierna ese nivel. Si esa sección no existe, decirlo: el arreglo incluye crearla.

---

### 🔴 Contenido huérfano (debería tener estación propia)
Bloques en raíz que pertenecen a un dominio sin estación todavía, pero el dominio es suficientemente grande o recurrente como para merecer una.

**Señal:** hay 3+ líneas sobre el mismo tema específico en raíz, sin estación que lo contenga.

**Acción:** proponer crear la estación y mover el contenido.

---

### 🔴 Carpeta desordenada (archivos sueltos y carpetas sin mapear)
Entregables, informes, borradores o carpetas que no pertenecen donde están.

**Señal:** archivos sueltos en raíz que no son de sistema, o carpetas sin fila en el Mapa de Enrutamiento, tengan o no `CLAUDE.md`, a cualquier profundidad.

**Acción:** por cada archivo suelto, proponer destino (estación, `Clientes/[cliente]/` o `_archivo/`). Por cada carpeta sin mapear, preguntar si se agrega al mapa, se mueve o se archiva. **Nunca mover ni borrar sin confirmación.**

---

### 🟡 Duplicación entre archivos
El mismo concepto, regla o dato aparece en más de un archivo.

**Señal:** frases similares o idénticas en dos o más archivos.

**Acción:** gana la copia que esté en el nivel correcto. Si la regla es verdadera fuera de esa carpeta, gana la de arriba y se borra la local. Antes de borrar, compará las dos palabra por palabra: si una perdió parte de la regla, sobrevive la completa. Si la copia local agrega una condición propia del dominio, esa condición queda como línea aparte en la estación, remitiendo a la regla de arriba.

---

### 🟡 Contradicciones entre archivos
Dos reglas o datos que se contradicen entre sí (ej: raíz dice una cosa y la estación dice lo contrario, o dos entradas de MEMORY.md son incompatibles).

**Señal:** instrucciones opuestas sobre el mismo tema, o datos factuales que no pueden ser ciertos a la vez.

**Acción:** presentar ambas versiones y preguntar cuál gana. Eliminar la perdedora.

---

### 🟡 Estaciones que se solapan
Dos estaciones que cubren territorio parecido o que podrían fusionarse.

**Señal:** sus `CLAUDE.md` tienen secciones similares o abordan los mismos tipos de tarea.

**Acción:** sugerir fusión o redefinición de scope.

---

### 🟢 Contenido viejo, referencias rotas y Mapa de Enrutamiento

**Contenido viejo:** entradas de `MEMORY.md` fechadas `[YYYY-MM]` con más de 6 meses que describen estados o decisiones que probablemente cambiaron, y entradas sin fecha que describen algo temporal ("está en curso", "arranca en", "por ahora", "activos al..."). Listarlas y preguntar cuáles siguen vigentes: las obsoletas se eliminan o se actualizan, las vigentes sin fecha se prefijan.

**Referencias rotas:** rutas de archivo citadas en los archivos auditados que no existen en disco. Extraé las rutas de los archivos de la ronda y verificalas en batch (un solo `ls` con todas las rutas, o un `test -e` en loop), no una por una. Priorizá las que aparecen en el raíz, en los archivos de referencia y en el Mapa de Enrutamiento: son las que rompen trabajo real. Una ruta muerta citada en varios archivos se corrige en todos, no solo en el primero.

**Mapa de Enrutamiento:** carpetas en disco sin fila, filas apuntando a carpetas borradas, y filas markdown rotas o fuera de la tabla.

---

## SYS-PASO 3 — Reporte de salud del sistema

Mostrá el diagnóstico completo antes de proponer cualquier cambio. Formato:

```
╔══════════════════════════════════╗
║   REPORTE DE SALUD — CLAUDE OS   ║
╚══════════════════════════════════╝

ESTADO DE ARCHIVOS
[tabla de líneas vs umbrales]

PROBLEMAS ENCONTRADOS

🔴 CRÍTICOS (mal ubicado, regla atrapada, regla de un solo proyecto, mezcla de
   dominios, restricción condicional, huérfano, carpeta desordenada)
→ [problema + archivo + cita textual + acción propuesta]

🟡 MEJORAS (duplicación, contradicciones, estaciones solapadas)
→ [problema + archivos involucrados + cita textual + acción propuesta]

🟢 MANTENIMIENTO (contenido viejo, referencias rotas, mapa)
→ [problema + acción propuesta]

RESUMEN
✅ [N] archivos dentro de los umbrales
⚠️ [N] archivos sobre el límite
🔴 [N] problemas críticos
🟡 [N] mejoras sugeridas
🟢 [N] tareas de mantenimiento
```

Cada hallazgo va con la cita textual del fragmento problemático, no con una paráfrasis. Sin la cita, Mati tiene que ir a buscar el fragmento para poder decidir, y una paráfrasis casi siempre suaviza el problema.

Esperá confirmación de Mati antes de modificar cualquier archivo.

---

## SYS-PASO 4 — Ejecutar los cambios confirmados

Por cada cambio aprobado:

- **Mover contenido de raíz a estación:** copiarlo al archivo destino, eliminarlo del origen. Verificar que no queden duplicados.
- **Subir una regla atrapada:** copiarla al nivel correcto, eliminarla de la estación. Si la estación tenía una condición propia, dejarla como línea aparte remitiendo a la regla de arriba.
- **Bajar una regla de un solo proyecto:** moverla al `CLAUDE.md` del sub-proyecto y eliminarla del padre.
- **Resolver una mezcla de dominios:** mover el bloque ajeno al archivo que es dueño de ese tema. En el archivo de origen queda una línea de remisión, no una copia.
- **Corregir una restricción condicional:** reescribirla con la condición adelante y moverla a la sección de su nivel. Si esa sección no existe, crearla antes de mover, con el alcance declarado en la primera línea.
- **Crear estación nueva:** seguir la estructura de `00-recursos/plantilla-estacion.md` y actualizar el Mapa de Enrutamiento en el mismo acto.
- **Mover archivos sueltos:** al destino confirmado. Lo que se archiva va a `_archivo/`. Lo que Mati marque para borrar va a `_to_delete/`, donde queda esperando que él decida: nunca se borra directo.
- **Eliminar duplicados:** conservar la copia del nivel correcto y la redacción completa.
- **Resolver contradicciones:** conservar la versión que Mati confirmó, eliminar la otra.
- **Corregir referencias rotas:** actualizar la ruta en todos los archivos que la citan.
- **Actualizar Mapa de Enrutamiento:** agregar, quitar o reparar filas según corresponda.
- **Comprimir MEMORY.md sobre umbral:** las entradas viejas del raíz van a `archivo-historico.md`; las de una estación, a un `archivo-historico.md` dentro de esa estación.

Confirmar cada cambio ejecutado con el archivo afectado y las líneas modificadas.
