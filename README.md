# muta-os

Marketplace de skills de Claude Code de Muta Digital, para equipo y amigos.

## Plugins

- **meta-ads-muta** — análisis técnico multinivel de campañas de Meta Ads (ROAS, CPL, CPM, fatiga, embudo).
- **creacion-contenido** — redacción de contenido: guiones de video corto, fórmulas de copy, carruseles y laboratorio de hooks.
- **metodologia-fv** — investigación de mercado (7 Maletas), psicología del consumidor y matriz de diversificación de ángulos. Basado en la metodología de Felipe Vergara.
- **reuniones-muta** — reuniones convertidas en entregables: `resumen-prospecto` destila una reunión estratégica en un HTML con diseño Muta ("En Limpio") para mandarle al prospecto o cliente como resumen post-reunión.

- **claude-os** — las tres skills que sostienen un Claude OS, en orden de ciclo de vida:
  1. `cowork-builder` — **la primera vez.** Construye el sistema de trabajo desde cero, o le suma un dominio nuevo. Se corre una sola vez y por pedido explícito.
  2. `session-audit` — **el día a día.** Al cerrar una sesión, audita lo que pasó y guarda lo aprendido en el archivo que corresponde (CLAUDE.md, MEMORY.md o la estación).
  3. `setup-auditor` — **el mantenimiento.** Se programa para correr una vez cada 1 o 2 meses, o cuando sale un modelo nuevo: audita CLAUDE.md, skills, hooks y subagentes contra la guía vigente de Anthropic y devuelve un veredicto borrar / dejar / reescribir por instrucción.

Los dos últimos se complementan: `metodologia-fv` define qué decir, `creacion-contenido` lo escribe. Cada skill funciona por separado.

## Instalar

**Claude Code (terminal):**
```
/plugin marketplace add matiascardoselli-spec/muta-os
/plugin install creacion-contenido@muta-os
/plugin install metodologia-fv@muta-os
/plugin install meta-ads-muta@muta-os
/plugin install reuniones-muta@muta-os
/plugin install claude-os@muta-os
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
