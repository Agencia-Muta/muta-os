# muta-os

Marketplace de skills de Claude Code de Muta Digital, para equipo y amigos.

## Plugins

- **meta-ads-muta** — análisis técnico multinivel de campañas de Meta Ads (ROAS, CPL, CPM, fatiga, embudo).
- **creacion-contenido** — redacción de contenido: guiones de video corto, fórmulas de copy, carruseles y laboratorio de hooks.
- **metodologia-fv** — investigación de mercado (7 Maletas), psicología del consumidor y matriz de diversificación de ángulos. Basado en la metodología de Felipe Vergara.
- **reuniones-muta** — reuniones convertidas en entregables: `resumen-prospecto` destila una reunión estratégica en un HTML con diseño Muta ("En Limpio") para mandarle al prospecto o cliente como resumen post-reunión.

- **docs-y-resumenes** — material crudo convertido en documentos ejecutables.
  - `muta-sop` — toma audios, transcripciones, notas sueltas o una explicación hablada y devuelve un SOP, QA, guía interna, guía para cliente o plantilla, con el paso a paso redactado para alguien que nunca hizo la tarea. Si lo que le contaron son varios procesos pegados (cambia el responsable, se estira más de 90 minutos, hay paso de manos), lo detecta y propone separarlos antes de escribir.

- **claude-os** — las skills que sostienen un Claude OS, en orden de ciclo de vida:
  1. `cowork-builder` — **la primera vez.** Construye el sistema de trabajo desde cero, o le suma un dominio nuevo. Se corre una sola vez y por pedido explícito.
  2. `session-audit` — **el día a día.** Al cerrar una sesión, audita lo que pasó y guarda lo aprendido en el archivo que corresponde (CLAUDE.md, MEMORY.md o la estación).
  3. `setup-auditor` — **el mantenimiento.** Se programa para correr una vez cada 1 o 2 meses, o cuando sale un modelo nuevo: audita CLAUDE.md, skills, hooks y subagentes contra la guía vigente de Anthropic y devuelve un veredicto borrar / dejar / reescribir por instrucción.
  4. `skill-audit` — **manual, con `/skill-audit`.** Audita el registro de skills instaladas: presupuesto de descripciones, cuáles llegan truncadas, y un plan ordenado de bajas y recortes.
  5. `skill-a-prompt` — **cuando hay que sacar una skill del ecosistema.** Convierte una skill (su SKILL.md y sus archivos de referencia) en un prompt de texto pegable en ChatGPT, en un GPT personalizado o al arrancar cualquier chat, para quien necesite ese criterio en una herramienta que no tiene skills.

Los dos últimos se complementan: `metodologia-fv` define qué decir, `creacion-contenido` lo escribe. Cada skill funciona por separado.

## Instalar

**Claude Code (terminal):**
```
/plugin marketplace add Agencia-Muta/muta-os
/plugin install creacion-contenido@muta-os
/plugin install metodologia-fv@muta-os
/plugin install meta-ads-muta@muta-os
/plugin install reuniones-muta@muta-os
/plugin install claude-os@muta-os
/plugin install docs-y-resumenes@muta-os
```

**App Claude (web/desktop/Cowork):**
Customize → Plugins → Agregar → Agregar marketplace → Add from a repository → pegar la URL de este repo.

## Actualizaciones

El auto-update viene **desactivado por default**. Para activarlo: Customize → Plugins →
Marketplaces → elegir `muta-os` → "Enable auto-update".

- Con auto-update activado: se chequea al iniciar sesión (delay random de hasta 10 min),
  se actualiza en background y pide correr `/reload-plugins`.
- Sin auto-update: correr `/plugin marketplace update muta-os` a mano cuando se avise de un cambio.

## Atribución

`metodologia-fv` implementa la metodología de Felipe Vergara (7 Maletas de Cualquier Compra, matriz de diversificación creativa). Implementación propia de Muta Digital, sin afiliación oficial.
