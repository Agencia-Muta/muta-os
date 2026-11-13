# session-audit: Modo Sistema

Las reglas de destino, nivel, fechado, umbrales y forma de cada entrada son las de `SKILL.md` y valen acá igual.

Leés el Claude OS y diagnosticás si el contenido está bien organizado, bien ubicado y si la estructura escala correctamente.

## Alcance

Descubrí el sistema desde el filesystem. La auditoría se adapta al sistema que encuentra, sean dos estaciones o veinte.

```bash
find "$RAIZ" \( -name "CLAUDE.md" -o -name "MEMORY.md" \) \
  -not -path "*/node_modules/*" \
  -not -path "$RAIZ/_archivo/*" \
  -not -path "$RAIZ/_to_delete/*" \
  -not -path "$RAIZ/Artifacts/*" \
  -not -path "$RAIZ/Scheduled/*"
```

Auditá todos los que devuelva, más:

1. Los archivos de referencia listados en la tabla de Referencias del `CLAUDE.md` raíz, incluidos los archivos dueños de cada acción
2. La memoria automática de Code, si la superficie la ve: el índice `MEMORY.md` y los archivos de `~/.claude/projects/<proyecto>/memory/`. Ahí Claude Code guarda correcciones por su cuenta, así que es donde más reglas sueltas se acumulan
3. El nivel raíz de la carpeta, para detectar archivos sueltos

Se audita todo `CLAUDE.md` y todo `MEMORY.md` que devuelva ese comando, sin importar en qué carpeta esté. Lo que no se audita es el **contenido**: los datos de las carpetas personales y los archivos de trabajo por cliente.

### Si el `find` devuelve más de 30 archivos

Una pasada que no entra en contexto termina en un diagnóstico incompleto que parece completo. Avisá el número y partí la auditoría en rondas:

- **Ronda 0**: raíz (`CLAUDE.md`, `MEMORY.md`) + todos los archivos de referencia.
- **Una ronda por carpeta de primer nivel**, incluyendo sus sub-estaciones.

En cada ronda, además del diagnóstico, anotá un **índice de reglas**: una línea por regla encontrada, con su archivo y su nivel aparente. Ese índice es lo que después permite detectar duplicación y contradicciones entre rondas, que es justamente lo que se pierde al partir.

Terminadas las rondas, corré una **ronda de cierre** sobre el índice acumulado: buscá reglas repetidas entre rondas, reglas contradictorias, y reglas que en su ronda parecían de estación pero aparecen en tres estaciones distintas (esas son globales). El reporte del SYS-PASO 3 se emite una sola vez, al final, consolidado.

---

## SYS-PASO 1: Relevamiento del sistema

1. `CLAUDE.md` raíz: contá líneas con `wc -l`, identificá secciones
2. `MEMORY.md` raíz: contá líneas con `wc -l`, identificá secciones
3. Para cada `CLAUDE.md`/`MEMORY.md` que devolvió el `find`: leelo y contá líneas con `wc -l`
4. Para cada archivo de referencia de la tabla del raíz: leelo y contá líneas
5. Listá el nivel raíz de la carpeta (`ls`) y anotá los archivos sueltos que no sean CLAUDE.md, MEMORY.md o archivo-historico.md
6. Listá **todas las carpetas del sistema, a cualquier profundidad**, tengan o no `CLAUDE.md`, y compará contra el Mapa de Enrutamiento: anotá qué carpetas están en disco y no en el mapa, y qué filas del mapa apuntan a carpetas que no existen. Las sub-estaciones cuentan: una carpeta con `CLAUDE.md` propio dentro de otra estación necesita su fila igual.

   **La infraestructura no se enruta y no es hallazgo:** `_archivo/`, `_to_delete/`, `Artifacts/`, `Scheduled/` y `00-recursos/`. No son estaciones de trabajo, no van al Mapa de Enrutamiento, y reportarlas cada vez es ruido permanente.

Construí internamente esta tabla antes de continuar:

```
ARCHIVO                        | LÍNEAS | UMBRAL | ESTADO
-------------------------------|--------|--------|--------
CLAUDE.md (raíz)               |  ___   |  200   |  ✅/⚠️
MEMORY.md (raíz)               |  ___   |  150   |  ✅/⚠️
[estación]/CLAUDE.md           |  ___   |  200   |  ✅/⚠️
[estación]/MEMORY.md           |  ___   |  150   |  ✅/⚠️
[archivo de referencia]        |  ___   |  :    |  ✅/⚠️
```

---

## SYS-PASO 2: Diagnóstico de contenido

Con los archivos leídos, buscá estos doce problemas. El primero es el que más degrada el sistema: buscalo antes que los demás.

### 🔴 Reglas de una misma acción repartidas
Las reglas de una acción o herramienta (mandar un mail o un mensaje, cargar una tarea, subir o compartir un archivo, armar o analizar un anuncio, armar un tipo de documento) viven en más de un archivo: raíz, estaciones, archivos de herramientas, MEMORY.md, memoria automática.

**Cómo se busca:** armá la lista de acciones que aparecen en el índice de reglas y, por cada una, grepeá su herramienta y sus verbos en todo el sistema y en la memoria automática. Contá en cuántos archivos aparecen reglas (no menciones incidentales) de esa acción.

**Acción:** si la acción tiene dueño (regla de dueño de `SKILL.md`), proponer mover cada regla suelta a su dueño, con el contenido completo, y dejar en el origen una línea de remisión solo si el origen es un flujo que necesita saber que existe. Si no tiene dueño y aparece en dos o más archivos, proponer crearlo y sumar su fila en Referencias del raíz. Lo de la memoria automática se mueve a `_to_delete/` y sale del índice.

---


### 🔴 Contenido mal ubicado en raíz
Reglas o datos en `CLAUDE.md`/`MEMORY.md` raíz que pertenecen a una estación existente.

**Señal:** el contenido menciona un dominio específico (una herramienta, un canal, un tipo de pieza) y existe una estación o un archivo dueño para ese dominio.

**Acción:** proponer moverlo al dueño de la acción si lo tiene; si no, a la estación correspondiente.

---

### 🔴 Regla global atrapada en una estación
Una regla escrita en un `CLAUDE.md` de estación que es verdadera en cualquier otra.

**Señal:** dice "en toda herramienta", "siempre", "nunca", o describe cómo tratar a Mati, cómo reportar, o cómo manejar credenciales y accesos. También: la misma regla aparece en tres estaciones distintas.

**Acción:** si es sobre una acción con dueño, proponer moverla a su dueño; si no es de ninguna acción en particular, subirla a raíz. En los dos casos, en la estación queda solo la parte específica del dominio.

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

## SYS-PASO 3: Reporte de salud del sistema

Mostrá el diagnóstico completo antes de proponer cualquier cambio. Formato:

```
╔══════════════════════════════════╗
║   REPORTE DE SALUD: CLAUDE OS   ║
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

## SYS-PASO 4: Ejecutar los cambios confirmados

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
