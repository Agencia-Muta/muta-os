---
name: cowork-builder
description: Construir o rediseniar desde cero el sistema de trabajo de Cowork, o sumarle un dominio nuevo. Uso unico y solo por pedido explicito: no activar en tareas del dia a dia ni en PRDs.
---

# cowork-builder — Configurador de Sistema de Trabajo en Claude Cowork

> **⚠️ Esta skill es de uso único.** Sirve para diseñar y construir tu sistema de trabajo
> en Cowork por primera vez, o para agregar un dominio nuevo a uno ya existente.
> Si tu sistema ya está construido y solo querés trabajar en él, no necesitás esto —
> usá directamente Cowork con tu proyecto configurado.

---

**Qué hacés:** Conducís una entrevista breve con el usuario, diseñan la arquitectura juntos,
y producís un archivo de configuración completo, build-ready, que el usuario lleva a Cowork
para ejecutar paso a paso. El documento debe funcionar como instrucción autónoma —
Cowork lo lee sin contexto previo.

**Regla de oro:** Proponé, no interrogués. El usuario debería dar un puñado de confirmaciones,
no escribir párrafos. Usá todo lo que ya sabés de él antes de preguntar algo.

---

## FASE 0 — Orientate primero (antes de decir una sola palabra al usuario)

Ejecutá esto en silencio antes de responder:

1. **Leé la memoria disponible.** Revisá los archivos de memoria del proyecto actual,
   conversaciones previas, y cualquier contexto que tengas sobre el usuario: trabajo,
   herramientas, proyectos activos, objetivos, restricciones, cómo trabaja, timezone.
2. **Pre-completá todo lo que podés inferir.** No preguntes lo que ya podés responder.
3. **Prepará el recap.** 5–7 bullets concretos de lo que ya sabés, listos para mostrar.

Luego abrí con: **"Acá lo que ya sé de vos — corregí lo que esté mal:"** + los bullets.
Si tenés poco contexto, decílo: la entrevista será más larga.

---

## FASE 1 — Proponer, no preguntar (4 pasos, uno a la vez)

### Paso 1 — Sugerí los dominios
Basándote en lo que sabés del usuario, proponé 4–6 dominios candidatos.
Cada uno: nombre + una línea de qué haría por él + por qué encaja.
Presentalos como pick-list numerada. El usuario elige y puede agregar uno.
**No preguntes "¿qué querés construir?" en abierto.**

### Paso 2 — Confirmá el entorno
En un bloque corto, decí tu mejor estimación de:
- Nivel técnico: no-code / light scripting / developer
- Conectores disponibles: Gmail, Calendar, Drive, Slack, Notion, etc.

Framealo como "esto es lo que asumo — corregí lo que esté mal."
La superficie de build es Claude Cowork (asumido siempre).

### Paso 3 — Duracion del build → scope
Preguntá **una sola cosa**: cuántas horas para el primer build.
Presentalo como tap único: `3 hs / 5 hs / 8 hs`.

Apenas tenga la respuesta, aplicá la **Regla de Scope** (ver Fixed Conventions)
y decí explícitamente qué dominios se construyen completos y cuáles quedan como
placeholder folders. Dejalo ajustar.

### Paso 4 — Cerrá los gaps reales
Solo lo que genuinamente no podés inferir (timezone, horarios de briefings,
qué NUNCA debe automatizarse). Proponé un default sensato y pedí confirmación.
**Una pregunta a la vez, solo si es necesario.**

En cuanto tengas suficiente, avanzá sin pedir permiso.

---

## FASE 2 — Sketch de arquitectura (aprobación antes del PRD)

Antes de escribir el PRD completo, mostrá un sketch de una página construido
estrictamente sobre los Fixed Conventions:
- Dominios elegidos
- Folder tree completo (con comentarios de propósito)
- Workflows por dominio mapeados a interaction patterns
- Build plan en tabla: Block 0 setup + los bloques de hora

Esperá feedback y ajustes. Luego arrancá con la Fase 3.

---

## FASE 3 — Escribir el PRD

Escribí el PRD completo como archivo markdown.
Dos audiencias: el usuario (para leer y aprobar) y Cowork (para ejecutar).
**Guardalo como `PRD-{nombre-del-sistema}.md` en la carpeta de outputs
y presentalo con present_files.**

---

## FIXED CONVENTIONS — Seguí estas exactamente, sin improvisar

> Todo lo de esta sección es fijo e idéntico en cada PRD. Las únicas variables
> son los dominios y la duración del build. No reinventes lo que el Productivity
> plugin ya provee.

### Foundation — Productivity plugin
Cada build corre sobre el Productivity plugin de Cowork.
Se instala en Cowork → Customize → Plugins → "Productivity" y se inicializa con `/start`.
`/start` crea en el root del proyecto: `CLAUDE.md`, `TASKS.md`, `memory/`, `dashboard.html`.
El plugin también provee `/update` (descubrimiento de action items) y un flujo create-skill.
El PRD construye sobre esto — **nunca crees un sistema separado de memoria, tasks o config.**

### Regla de Scope — duración determina dominios
| Duración | Qué cabe |
|---|---|
| ~3 hs | Foundation + 1 dominio + morning brief |
| ~5 hs | Foundation + 2 dominios (o 1 dominio rico + builder pattern) |
| ~8 hs | Foundation + 2–3 dominios |
| Más | Más dominios, nunca más de ~4 activos en una ventana |

Si el usuario elige más dominios de los que entran, construí los de mayor prioridad
completamente y dejá el resto como **placeholder folders** en el árbol + entradas en §10.
Un sistema sólido en dos dominios > uno roto en cinco.

### Setup sequence — Block 0 de todo build
Primera vez que corre Cowork; asumí que nada está configurado.
Cowork verifica y guía al usuario en orden:
1. El proyecto Cowork existe y apunta a una carpeta local (ej. `~/cowork/`)
2. El Productivity plugin está instalado
3. `/start` fue ejecutado → los archivos root del plugin existen
4. Los conectores necesarios están habilitados

Solo cuando los cuatro se confirman empieza el Block 1 (data layer).
Las custom skills en `toolbox/` se construyen durante los bloques — no son prerequisito.

### Root folder skeleton — siempre esta forma exacta
```
~/cowork/                    ← Cowork project root (LOCAL folder)
├── CLAUDE.md                ← plugin: cross-cutting working memory
├── TASKS.md                 ← plugin: task list
├── memory/                  ← plugin: deep memory, organized by domain
│   ├── people.md
│   ├── terminology.md
│   └── {domain}/            ← one subfolder per domain
├── dashboard.html           ← plugin dashboard
├── PRD-{system-name}.md     ← this PRD, dropped at root for reference
├── toolbox/                 ← installable custom skills (source of truth)
├── briefs/                  ← morning-brief output + archive/
└── {domain}/                ← one folder per domain (pattern below)
```
Agregá `builds/` solo si el autonomous-builder pattern está en scope.

### Per-domain folder pattern — idéntico para cada dominio
```
{domain}/
├── CLAUDE.md     ← folder-level voice/role for this domain
├── inputs/       ← human-maintained files (NEVER auto-overwritten)
├── data/         ← machine-refreshed derived files
└── outputs/      ← generated artifacts (briefs, dashboards, docs)
```

### Memory architecture — 3 tiers fijos
1. Root `CLAUDE.md` — working memory cross-cutting: personas, terminología, shorthand
2. `memory/{domain}/` — deep knowledge por dominio
3. `{domain}/CLAUDE.md` — rol y tono cuando se trabaja dentro de ese dominio

### Naming conventions — fijos
- Folders: `kebab-case`
- Memory files: `noun.md`
- Data files: `noun.json`
- Date-stamped files: `name-YYYY-MM-DD.md`

### Interaction patterns — siempre los cuatro
El sistema expone siempre: **dashboard** (visual always-on), **brief/digest** (scheduled push),
**skill** (on-demand command), y — solo si el build length lo permite — **autonomous builder**
(drop a brief, get a finished work product).

---

## ESTRUCTURA DEL PRD — §1 a §10, siempre en este orden

### Calibración de detalle
- Folder tree completo con comentario de propósito en cada carpeta
- Cada data file con schema real como code block (field names reales, valores de ejemplo). Nunca "schema TBD"
- Cada workflow y skill con prompt copy-paste-ready, nombrando exactamente los archivos que lee y escribe
- Build plan y decision log en tablas
- Decision log: 8–15 filas, solo decisiones no-obvias, sin historial inventado
- Un build de 3 hs es un PRD más corto que uno de 8 hs — escalá el detalle al scope

### §1 Executive summary
Qué es el sistema, los dominios, los interaction patterns, por qué encaja en la duración
del build y cómo escala después.

### §2 Quick start — moving this into Cowork
Esta sección es el handoff de Claude chat a Cowork. Debe contener:

**Getting into Cowork** (hace el usuario): abrir Claude Cowork, crear un proyecto apuntando
a una carpeta local (ej. `~/cowork/`), y cargar este PRD — dropearlo en la carpeta del proyecto
o pegar su contenido en el primer mensaje de Cowork. Primera vez que Cowork está involucrado;
nada está configurado todavía.

**Project instructions** — el texto exacto para pegar en el campo de custom-instructions
del proyecto Cowork, como bloque copy-paste (incluye: dominios, ubicación del data layer,
regla inputs-are-never-overwritten, regla Start Block N block-by-block, timezone).

**How to run the build** — instrucciones para Cowork: cuando el usuario diga arrancar,
asumir que nada está configurado. Ejecutar Block 0 primero. Construir bloque a bloque en orden;
tras cada bloque, reportar qué se hizo + done-check, luego esperar go-ahead.

**The first thing I say** — la frase literal que el usuario escribe en Cowork para empezar
(ej. "Start building — begin with Block 0").

### §3 Goals and non-goals
Explícito. Nombrá lo que deliberadamente NO está en este build y por qué.

### §4 Architecture overview
Las tres capas (local folders → Cowork project → workflows); los interaction patterns
y qué workflows los usan; la memoria en 3 tiers; decisiones arquitectónicas clave y la
tensión detrás de cada una. Todo según Fixed Conventions.

### §5 The data layer — the foundation
Construida sobre los archivos root del Productivity plugin, siguiendo el skeleton fijo.

Especificá:
- **Dónde vive** — local. Plain files en la carpeta local que el proyecto Cowork apunta.
  Los conectores (Drive, Gmail, Calendar, Notion) son fuentes de datos — **nunca almacenamiento**.
  La carpeta local puede estar dentro de un directorio sincronizado para backup, pero Cowork
  siempre lee/escribe archivos locales.
- **Folder tree** — árbol local completo con los dominios reales del usuario; comentario de propósito por carpeta
- **Inputs vs. data** — `inputs/` es mantenido por humanos; `data/` es machine-refreshed. Un refresh task NUNCA escribe en `inputs/`
- **Memory files** — qué va en `memory/people.md`, `memory/terminology.md`, y cada `memory/{domain}/`
- **Schemas** — cada data file con schema real: shape JSON/CSV, field names, valores de ejemplo
- **Refresh strategy** — para cada `data/` file: qué lo popula, con qué frecuencia, overwrite vs append-only, lógica de dedupe
- Naming sigue las Fixed Conventions (memory `.md`, data `.json`)

### §6 Component specifications
Para cada workflow (data pipeline, dashboard, brief, skill, builder):
propósito, qué lee, qué escribe, schedule, estructura del output.
Los interfaces leen solo desde el data layer de §5.

### §7 The build plan
Time-boxed, sized al build length del usuario, ejecutable.

**Block 0 — Setup** (abre siempre, ~15–30 min, mayormente acciones del usuario):
Cowork verifica en orden: (1) Productivity plugin instalado — si no, da pasos exactos y espera;
(2) `/start` ejecutado y archivos root existentes confirmados; (3) conectores necesarios habilitados,
nombrando cuáles activar. Solo cuando el setup está confirmado empieza Block 1.

**Block 1** — siempre es el data layer: crear el folder tree completo, los input files
(con seed data), y los data-refresh workflows.

**Luego:** primera interfaz → más interfaces → polish (en ese orden).

**Tabla:** `Block | What gets built | Who runs it | Output | Done when…`
"Who runs it" = Cowork o Me (cualquier cosa que requiera terminal/CLI/cron/git;
si el usuario es no-code, cada fila de build debe ser Cowork).
Blocks 1…N son aproximadamente de una hora; N = build length del usuario en horas.

**Cut order** — lista ordenada de qué cortar primero si van tarde.

**Never cut** — el core mínimo viable: siempre incluye Block 0 setup, data layer, y morning brief.

### §8 Setup details and copy-paste prompts
El paso exacto de creación de carpetas y un prompt copy-paste-ready para cada workflow
y skill del build plan, nombrando exactamente los data-layer files que lee y escribe.
Cada prompt con guardia: `CRITICAL: never write to inputs/` donde aplique.

### §9 Decision log
Tabla: cada decisión no-obvia y su razonamiento / trade-off.
~8–15 filas. Sin historial de changelog inventado.

### §10 Out of scope / future work
Qué viene después (incluyendo dominios diferidos por la Regla de Scope, como placeholder folders),
cómo escala la arquitectura sin reestructurar (nuevo dominio = nueva carpeta + workflows),
y qué forzaría una re-arquitectura.

---

## RECORDATORIO — Principios activos durante toda la conversación

- **Proponé, no interrogués.** El input del usuario debe ser un puñado de picks y confirmaciones.
- **Concreto sobre abstracto.** Folder names reales, schemas reales, prompts reales. Sin placeholders donde hay una respuesta posible.
- **Marcá los supuestos.** Para que el usuario pueda corregir.
- **Diseñá para más dominios de los que el usuario nombró hoy.** Escala sin reestructurar.
- **El data layer es local.** Los conectores son fuentes, nunca almacenamiento.
- **Usá el Productivity plugin.** Nunca reinventes memoria, tasks o config.
