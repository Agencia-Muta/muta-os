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
| Las carpetas de `~/.claude/skills/` | no | sin ellas no se pueden comparar cuerpos y el análisis de superposición queda afuera; avisarlo |
| Clon local de los repos de plugins propios | no | sin él las skills de plugin se pueden diagnosticar pero no editar; avisarlo |

---

## Proceso

### 1. Medir el estado real

Contar, no estimar:

- Cuántas skills llegaron **con** descripción y cuántas **solo con el nombre**. Las que llegan sin descripción están truncadas: el ruteo automático no las puede elegir.
- Los caracteres de cada `description:` de las skills sueltas, sumados.
- Dónde cae el corte: cuál es la última skill que conservó su descripción.

Nunca reportar de memoria ni comparar contra una medición vieja de otra sesión: el orden de carga cambia entre sesiones.

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

Un dato que invierte el orden intuitivo: **una skill que ya llega truncada no está consumiendo presupuesto.** Recortarle la descripción no libera nada. Y las skills sueltas se cargan antes que las de plugin, así que una skill propia dentro de un plugin es siempre la primera en caer.

Consecuencia: lo único que libera presupuesto es **sacar o recortar skills sueltas que hoy sí muestran descripción**. Ordenar los hallazgos por eso, no por tamaño.

### 4. Analizar superposición antes de proponer bajas

Ninguna baja se propone leyendo la descripción. Abrir el cuerpo de la skill y el de las que se quedan, y decir qué capacidad concreta se pierde. Tres criterios:

- **Redundante**: lo que hace ya está en una que se queda. Se saca sin costo.
- **Off-brand**: funciona, pero contradice el sistema de Muta. Se saca, nombrando la excepción donde igual serviría.
- **Pérdida real**: es capacidad única. Se nombra la pérdida y decide Mati.

Contar también los archivos: una skill con `scripts/` o `references/` casi nunca es redundante con una de un solo `SKILL.md`.

### 5. Redactar las descripciones nuevas

Máximo 200 caracteres, según `Skills-Lab/plantilla-skill.md`. No es cortar: es reescribir.

- Si dos skills compiten por el mismo pedido, la frontera va escrita adentro de los 200 caracteres de las dos.
- Si la skill tiene modos o alcances que la descripción vieja no nombraba, la nueva los nombra: el largo no es la única falla posible.
- Nunca renombrar una skill para arreglar su ruteo: renombrar resetea su contador de uso.

### 6. Presentar y esperar

Mostrar el reporte completo antes de tocar nada. Nada se modifica sin confirmación por número.

### 7. Ejecutar lo que corresponda

- **Sueltas `custom` o `anthropic-example`**: armar el `.skill` con la descripción nueva y entregarlo con SendUserFile, que le da a Mati el botón de reemplazar habilidad. El cuerpo y las `references/` se copian intactos.
- **Skills de plugin propio**: editar el `SKILL.md` en el clon del repo, subir la versión del plugin, y dejar el commit y el push a Mati desde Claude Code local. `device_bash` no tiene red y el contenedor no tiene el repo.
- **Desinstalar: no se puede.** No existe esa operación. Se entrega la lista y la ejecuta Mati en Customize.

### 8. Cerrar diciendo cómo se verifica

El listado de skills llega al arrancar la sesión y no se refresca. **Esta auditoría no se puede verificar en su propia corrida.** Cerrar siempre con la prueba concreta: abrir una sesión nueva y preguntar por la descripción de una skill que estaba truncada.

---

## Formato de salida

```
AUDITORÍA DE SKILLS — [fecha]

ESTADO
[N] skills cargadas · [N] con descripción · [N] truncadas (solo nombre)
Descripciones sueltas: [N] caracteres
El corte cae después de: [nombre de la última que conservó descripción]

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
Sesión nueva → "¿qué descripción tenés de [skill que estaba truncada]?"
```

---

## Referencia

### Cómo funciona el presupuesto

El `description:` del frontmatter se carga en **todas** las sesiones, para todas las skills instaladas. El cuerpo, los `scripts/` y las `references/` **no se cargan hasta que la skill se invoca**, y ahí entran completos sin tope. Por eso recortar una descripción no saca capacidad: en una skill típica la descripción es entre el 1% y el 5% del archivo.

El listado tiene un tope de contexto del 1% de la ventana (~10k tokens sobre 1M). Al pasarlo, el sistema no saca skills: les corta la descripción y deja solo el nombre. Sin descripción el ruteo automático elige a ciegas, que es la causa de que agarre la skill equivocada o ninguna.

El corte es **posicional**, no por tamaño: se llena hasta agotar el presupuesto y lo que sigue queda sin descripción. Las sueltas van antes que las de plugin.

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
