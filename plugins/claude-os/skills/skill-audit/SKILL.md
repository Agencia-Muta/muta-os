---
name: skill-audit
description: Audita el registro de skills: mide el presupuesto de descripciones, detecta cuales llegan truncadas y devuelve un plan ordenado de bajas y recortes. Manual, con /skill-audit.
disable-model-invocation: true
---

# skill-audit — Auditoría del registro de skills

## Cuándo se usa

Cuando Mati la invoca a mano con `/skill-audit`, o al cerrar el Modo Sistema de `session-audit` si él lo pide. Nunca por iniciativa propia: no tiene disparadores automáticos.

Audita el **registro** de skills: cuáles están instaladas, cuánto pesa cada descripción y cuáles se están perdiendo el ruteo. No audita el contenido ni la calidad de una skill (para mejorar una skill puntual, ese trabajo vive en `Skills-Lab`), ni la seguridad de un artefacto de terceros (eso es `evaluar-herramienta`), ni los archivos del Claude OS (eso es `session-audit` en Modo Sistema).

---

## Qué necesito antes de empezar

| Insumo | Obligatorio | Si falta |
|---|---|---|
| El listado de skills de esta sesión | sí | está siempre en el prompt del sistema; si no aparece, frenar y decirlo |
| `~/.claude/skills/manifest.json` | sí | sin él no hay campo `source` ni se puede separar lo editable de lo intocable; frenar |
| `skillUsage` en `~/.claude.json` | sí | sin él no se puede explicar por qué una skill llega truncada; frenar |
| Las carpetas de `~/.claude/skills/` | no | sin ellas no se pueden comparar cuerpos y el análisis de superposición queda afuera; avisarlo |
| Clon local de los repos de plugins propios | no | sin él las skills de plugin se pueden diagnosticar pero no editar; avisarlo |

---

## Proceso

### 1. Medir el estado real

Contar, no estimar:

- Cuántas skills llegaron **con** descripción y cuántas **solo con el nombre**. Las que llegan sin descripción están truncadas: el ruteo automático no las puede elegir.
- Los caracteres de cada `description:` que **sí** llegó, separando propias de terceros. Esas son las que llenan el tope.
- El uso de cada skill: leer `skillUsage` en `~/.claude.json`, que guarda `usageCount` y `lastUsedAt` (en milisegundos) por skill, las de plugin como `plugin:skill`. Algunas quedaron registradas sin prefijo: si no aparece con él, buscarla por el nombre solo. Cruzarlo con el listado y separar tres grupos: con descripción, con uso pero sin descripción, y nunca usadas.

Nunca reportar de memoria ni comparar contra una medición vieja de otra sesión: el uso cambia todos los días, y una skill que se usó hoy puede llegar con descripción en la sesión siguiente.

### 2. Clasificar por origen

Leer el campo `source` de `manifest.json`. Define qué se puede hacer con cada una:

| `source` | Se puede desinstalar | Se puede recortar la descripción |
|---|---|---|
| `custom` | sí, Mati desde Customize | sí, entregando el `.skill` con SendUserFile |
| `anthropic-example` | sí, Mati desde otra pestaña del Directorio | sí |
| `anthropic` | no | no, son gestionadas |
| skill de plugin | desinstalando el plugin entero | sí, editando el repo fuente y pusheando |

Lo que está en disco y no figura en el manifest es del sistema, no de Mati: no se toca ni se reporta.

### 3. Separar lo que cuesta de lo que no

Quién conserva la descripción se decide por uso, no por posición ni por origen (ver Referencia). De ahí salen tres datos que invierten el orden intuitivo:

- **Una skill sin usar cuesta solo su nombre.** Ya llega truncada: sacarla libera ese nombre y nada más, y recortarle la descripción no libera nada.
- **Lo que consume el tope son las descripciones largas de las skills de terceros que sí se usan.** Lo único que libera presupuesto es sacar o recortar skills que hoy llegan con descripción, sueltas o de plugin, según lo que permita su origen (paso 2).
- **Las propias de uso raro no se rescatan recortando.** Lo que se libera lo ocupa otra skill con uso, no la que uno elige. Esas dependen del ruteo de los CLAUDE.md de cada estación (mapa en `~/Claude/00-recursos/mapa-enrutamiento.md`): en TUYAS QUE NO ESTÁN LLEGANDO, decir si el CLAUDE.md de su estación la nombra. Si no la nombra ninguno, ese es el hallazgo.

Ordenar los hallazgos por eso, no por tamaño.

### 4. Analizar superposición antes de proponer bajas

Ninguna baja se propone leyendo la descripción. Abrir el cuerpo de la skill y el de las que se quedan, y decir qué capacidad concreta se pierde. Tres criterios:

- **Redundante**: lo que hace ya está en una que se queda. Se saca sin costo.
- **Off-brand**: funciona, pero contradice el sistema de Muta. Se saca, nombrando la excepción donde igual serviría.
- **Pérdida real**: es capacidad única. Se nombra la pérdida y decide Mati.

Contar también los archivos: una skill con `scripts/` o `references/` casi nunca es redundante con una de un solo `SKILL.md`.

### 5. Redactar las descripciones nuevas

Máximo 200 caracteres, según `Skills-Lab/plantilla-skill.md`. No es cortar: es reescribir.

- Si dos skills compiten por el mismo pedido, la frontera va escrita adentro de los 200 caracteres de las dos.
- Un "no" sin destino deja el pedido sin skill: la frontera dice qué hace la vecina.
- Si la skill tiene modos o alcances que la descripción vieja no nombraba, la nueva los nombra: el largo no es la única falla posible.
- Nunca renombrar una skill para arreglar su ruteo: renombrar resetea su contador de uso.

### 6. Presentar y esperar

Mostrar el reporte completo antes de tocar nada. Nada se modifica sin confirmación por número.

### 7. Ejecutar lo que corresponda

- **Sueltas `custom` o `anthropic-example`**: armar el `.skill` con la descripción nueva y entregarlo con SendUserFile, que le da a Mati el botón de reemplazar habilidad. El cuerpo y las `references/` se copian intactos.
- **Skills de plugin propio**: editar el `SKILL.md` en el clon del repo, subir la versión del plugin, y dejar el commit y el push a Mati desde Claude Code local. `device_bash` no tiene red y el contenedor no tiene el repo.
- **Desinstalar: no se puede.** No existe esa operación. Se entrega la lista y la ejecuta Mati en Customize.

### 8. Cerrar diciendo cómo se verifica

El listado de skills llega al arrancar la sesión y no se refresca. **Esta auditoría no se puede verificar en su propia corrida.** Cerrar siempre con la prueba concreta: abrir una sesión nueva y preguntar por la descripción de una skill con uso registrado que estaba truncada. Una nunca usada va a seguir sin descripción aunque haya lugar.

---

## Formato de salida

```
AUDITORÍA DE SKILLS — [fecha]

ESTADO
[N] skills cargadas · [N] con descripción · [N] truncadas (solo nombre)
Descripciones que llegan: [N] caracteres ([N] propias · [N] de terceros)
Con uso y sin descripción: [N] · Nunca usadas: [N]

TUYAS QUE NO ESTÁN LLEGANDO
→ [skill] — [qué se pierde por no tener descripción]

1. BAJAS — libera [N] caracteres
| Skill | Chars | Qué se pierde | Veredicto |
|---|---|---|---|
| [nombre] | [N] | [capacidad concreta] | redundante / off-brand / pérdida real |

2. RECORTES — [N] → [N] caracteres
| Skill | De | A | Texto nuevo |
|---|---|---|---|

3. DECISIONES TUYAS
→ [las que son pérdida real de capacidad, con la pregunta concreta]

ORDEN DE EJECUCIÓN
1. [lo que libera presupuesto de verdad, primero]
2. [medir en sesión nueva antes de seguir]
3. [el resto]

VERIFICACIÓN
Sesión nueva → "¿qué descripción tenés de [skill con uso que estaba truncada]?"
```

---

## Referencia

### Cómo funciona el presupuesto

El nombre de cada skill instalada llega en **todas** las sesiones. El `description:` del frontmatter llega solo si entra en el tope. El cuerpo, los `scripts/` y las `references/` **no se cargan hasta que la skill se invoca**, y ahí entran completos sin tope. Por eso recortar una descripción no saca capacidad: en una skill típica la descripción es entre el 1% y el 5% del archivo.

El listado tiene un tope que ronda los 29.000 caracteres en total: medido el 5/11/2026, dio 28.914 antes de sacar 6 skills y 28.921 después. Sacar skills no achica el listado: el espacio libre lo vuelve a ocupar otra. Al pasar el tope el sistema no saca skills: les corta la descripción y deja solo el nombre. Sin descripción el ruteo automático elige a ciegas, que es la causa de que agarre la skill equivocada o ninguna.

El reparto es **por uso**, no posicional: `skillUsage`, en `~/.claude.json`, guarda cuántas veces se usó cada skill y cuándo fue la última. Lo medido el 5/11/2026:

- Todas las skills instaladas que llegaron con descripción tenían uso registrado. Las nunca usadas llegaron solo con el nombre, sueltas o de plugin. Las integradas de Claude Code (`loop`, `run`, `simplify` y similares) llegan con descripción aunque no se hayan usado nunca.
- Ser de plugin no manda al final: `muta-ventas:pipeline-venta` y `marketing-skills:ads` llegan con descripción, mientras que skills sueltas sin usar llegan sin ella.
- Tres skills de `muta-ventas` ganaron la descripción en el día, después de usarse.
- Tener uso no alcanza: `meta-ads-muta:meta-ads-analysismd` (5 usos, el último hace 27 días) y `muta-ventas:mail-presupuesto` (3 usos, hace 29 días) llegaron sin descripción.

La fórmula exacta no se conoce, pero no es solo recencia: `ponytail` conserva la suya con más de 90 días sin uso. Nunca prometer que una skill puntual va a llegar con descripción después de una baja o un recorte: se mide en sesión nueva.

### Síntomas que disparan la auditoría

- Una o más skills llegan solo con el nombre.
- Una descripción propia pasa los 250 caracteres.
- Claude agarró la skill equivocada, o ninguna, en un pedido que claramente correspondía a una.
- Se instaló algo nuevo desde la última medición.

Si no se cumple ninguno, decirlo y no fabricar hallazgos.

### Trampas verificadas

- **El manifest en disco es una foto del arranque de la sesión.** No refleja lo que Mati acaba de desinstalar. Para lo recién cambiado, la única fuente es el listado de una sesión nueva.
- **Apagar una skill equivale a desinstalarla** a efectos de carga: una deshabilitada no aparece en el listado.
- **La carpeta puede no llamarse como la skill.** En Customize se muestra el campo `name:` del frontmatter, no el nombre de carpeta. Si Mati no encuentra una para desinstalarla, ese suele ser el motivo; las `anthropic-example` además viven en otra pestaña del Directorio.
- **Antes de instalar algo nuevo**, verificar que el nombre no choque con una skill ya disponible, propia o de plugin.
- **No usar la fase "Description Optimization" de `skill-creator`**: agranda la descripción hasta que esa skill dispare bien, sin saber que compite con todas las demás por el mismo presupuesto.
