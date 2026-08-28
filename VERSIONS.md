# Versiones de las skills

Este archivo registra la versión vigente de cada skill del marketplace. Cualquier cambio que se pushee a una skill DEBE subir su versión acá y en el frontmatter de su SKILL.md — si no, el equipo que ya la tiene instalada nunca se entera del cambio.

| Plugin | Skill | Versión | Último cambio |
|--------|-------|---------|---------------|
| branding-muta | informe-de-mercado | 1.0.0 | Versionado inicial |
| claude-os | cowork-builder | 1.0.0 | Versionado inicial |
| claude-os | session-audit | 1.0.0 | Versionado inicial |
| claude-os | setup-auditor | 1.0.0 | Versionado inicial |
| claude-os | skill-a-prompt | 1.0.0 | Versionado inicial |
| claude-os | skill-audit | 1.0.0 | Versionado inicial |
| creacion-contenido | carrusel | 1.1.0 | Fase 0 de contexto de marca; CTA por objetivo comercial; sin hashtags; cinco frameworks narrativos nuevos en references |
| creacion-contenido | estructuras-copy | 1.1.0 | Fase 0 de contexto de marca; expone las 10 estructuras narrativas y la guía rápida del manual |
| creacion-contenido | guion-video | 1.3.0 | Sección "El mundo del que mira": escenas con sustantivos del rubro, absolver antes de señalar, hablarle y no describirlo, economía sin compresión. Salió de la primera corrida de calidad: el guion pasaba las aserciones pero quedaba institucional. 1.1.2: los hechos sobre la marca se verifican contra el brief igual que los números; si falta un dato, se pregunta. 1.2.0: cadena de loops obligatoria en storytelling (loop madre + mini-loop en cada pasaje de escena) y la marca en primera persona activa. 1.2.1: los resultados entran por el beat que los hizo visibles. 1.3.0 (desde el cerebro creativo): open loops con "toda creatividad es una promesa", el payoff no es la oferta, texto en pantalla del hook como gancho contextual (la regla anterior estaba invertida), Primary Life Outcomes como filtro de la premisa. 1.3.1 (desde la transcripción de la capacitación 12/3): el encadenado de open loops baja hasta la oración (pistas de a poco, ciclo abrir-desarrollar-cerrar-abrir), el open loop puede ser puramente visual, y el gancho puede ser todo textual en algunos formatos. 1.4.0 (auditoría contra el canon): el guion funciona en mudo, la primera imagen la decide el guion, la objeción se contesta cuando nace, la última línea se escribe como el hook y se corta apenas pagó, y el guion se escribe para la boca del que graba. Se elimina un bullet redundante de loop abierto. 1.4.1: pasada final de limpieza — duplicados fusionados (tres capas, re-hook, payoff a las 3/4), contador de reglas corregido, payoff-no-es-la-oferta reescrito claro. 1.4.2: el gancho intermedio pasa de segundo fijo (12) a proporcional (mitad del recorrido). 1.4.3: y queda acotado a storytelling — en venta directa, educacional o yap retiene el argumento, no el suspenso. 1.4.4: el video directo definido en positivo — problema exacto, valor enseguida, orden problema/solución/aplicación/precio; retener reteniendo información es perder al comprador |
| creacion-contenido | hook-lab | 1.1.0 | Fase 0 de contexto y voz de marca |
| docs-y-resumenes | muta-sop | 1.0.0 | Versionado inicial |
| docs-y-resumenes | resumen-reunion | 1.0.0 | Versionado inicial |
| meta-ads-muta | meta-ads-analysismd | 1.0.0 | Versionado inicial |
| metodologia-fv | 7-maletas | 1.0.0 | Versionado inicial |
| metodologia-fv | detective-angulos | 1.0.0 | Versionado inicial |
| metodologia-fv | matriz-diversificacion | 1.0.0 | Versionado inicial |

## Cuándo se sube qué

- **Mayor (X.0.0):** cambio que rompe cómo se venía usando la skill (cambia el flujo, cambia qué pide, cambia el formato de entrega de forma incompatible).
- **Menor (x.Y.0):** capacidad nueva, o cambio en los disparadores del campo description.
- **Parche (x.y.Z):** correcciones y aclaraciones que no cambian el uso.
- **Regla dura:** todo cambio que se pushea sube la versión en el frontmatter Y en este archivo, en el mismo commit.
- **Regla de presupuesto:** una skill que ya funciona tiene techo de tamaño. Toda regla nueva entra sacando otra: antes de agregar, preguntarse qué regla existente no está trabajando y puede pagar el lugar. Una skill que solo crece vuelve al problema que la origino (se cumple lo visible y se afloja lo invisible).

## Corridas de eval

| Fecha | Skill | Versión | Casos corridos | Resultado |
|-------|-------|---------|----------------|-----------|
| 2026-08-28 | guion-video | 1.1.0 | Caso 1 (pedido sin contexto), en sesión limpia | 5 de 5 aserciones |
| 2026-08-28 | carrusel | 1.1.0 | Caso 1 (pedido sin contexto), en sesión limpia | 4 de 4 aserciones |
| 2026-08-28 | guion-video | 1.1.0 | Caso 2 (mano a mano con contexto cargado, anuncio de venta directa) | 8 de 8 aserciones; converge con la versión escrita a mano y suma premisa, hooks alternativos y notas de dirección |
| 2026-08-28 | guion-video | 1.1.0 | Corrida de calidad con modelo chico (ángulo "ya pasaste por varias agencias") | Pasó las aserciones pero quedó institucional: sin escenas del mundo del que mira, sin absolución, comprimido. Originó 1.1.1 |
| 2026-08-28 | guion-video | 1.1.1 | Misma corrida repetida con la skill parcheada y fixture con textura de audiencia | Brecha de tono cerrada (escenas, absolución, respiración). Encontró un hecho operativo inventado sobre la marca: originó 1.1.2 |

Los casos 3 y 4 de guion-video y 2 y 3 de carrusel quedan pendientes de correr con material real de cliente (necesitan un caso de éxito escrito como fixture).
