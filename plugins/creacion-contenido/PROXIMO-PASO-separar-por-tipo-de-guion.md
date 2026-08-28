# Próximo paso: separar `guion-video` por tipo de guion

Decidido el 28/08/2026. No empezado. Este archivo tiene todo lo necesario para
que otra sesión lo ejecute sin releer la conversación que lo originó.

## Por qué

`guion-video` llegó a 1.5.0 haciendo bien las piezas de argumento (venta directa,
criterio, educacional) y flojo el storytelling. La Fase 4 se bifurcó en 1.5.0 para
tapar eso, pero es un parche: una sola skill sostiene hoy tres esqueletos que se
estorban, y el que está escrito en el cuerpo principal (HOOK → DESARROLLO → GIRO →
CIERRE) le gana siempre a los que viven en las references.

La contraprueba del 28/08 (registrada en VERSIONS.md) mostró además que una parte
del problema no es de skill: sin material real que revivir, el formato historia
sale argumentado con skill y sin skill. Eso se resuelve en la Fase 0 de la skill
de storytelling, no en su esqueleto.

## Arquitectura

**Una puerta de entrada, tres hijas.** El equipo no elige skill: entra siempre por
`guion-video`.

- **`guion-video` (router).** Adelgaza a lo mínimo: Fase 0 (contexto de marca, voz,
  objetivo comercial), las tres decisiones de formato, y la derivación a la hija que
  corresponda. Sin esqueleto de guion propio.
- **`guion-storytelling`.** Arco de 5 partes, cadena de open loops, revivir en vez de
  reportar, obstáculo interno, punto de giro señalable.
- **`guion-venta-directa`.** Problema exacto, valor enseguida, orden problema /
  solución / aplicación / precio, filtro de audiencia, y todo lo de anuncios (regla
  del puente, tres hooks, tres caminos en pauta paga).
- **`guion-educacional`.** Una idea, ejemplo concreto, objeción contestada cuando nace.

`guion-serie` entra cuando Mati defina el framework. Yap queda afuera hasta que
exista uno (la búsqueda del 28/08 no encontró nada usable).

## Reglas de la separación

1. **Nada compartido se duplica.** Lo común vive en `CANON.md` y las hijas lo citan
   textual: Fase 0, objetivos y CTA, léxico prohibido, hashtags, mundo del que mira,
   hechos verificados. Se le suman al canon el bloque de hook y el checklist base.
   Si cada hija se redacta suelta, en tres meses hay tres dialectos de la misma
   regla — que es exactamente el problema que originó el canon.
2. **Las references se comparten, no se copian.** `sistema-de-hooks.md`,
   `proceso-creativo.md`, `formatos-de-contenido.md` y `formatos-ugc.md` quedan en un
   solo lugar y las hijas rutean ahí.
3. **La regla de presupuesto sigue vigente.** Separar no es excusa para que cada hija
   crezca: cada una tiene que quedar más chica que la `guion-video` actual.
4. **La Fase 0 de `guion-storytelling` pide los hechos del episodio** (qué pasó, quién,
   cuándo, qué se dijo) y, si el episodio es ilustrativo y no un caso puntual, obliga
   a escribirlo como situación recurrente ("cada tanto nos pasa esto") en vez de como
   un hecho que ocurrió. Resuelve el caso inventado sin prohibirlo.

## Riesgo principal

Que el ruteo falle y el equipo termine escribiendo con la hija equivocada, o que el
modelo invoque una hija salteando la Fase 0. Mitigación: `guion-video` es la única
con disparadores amplios en su `description`; las hijas se describen como destino de
derivación, no como puerta de entrada. Y se escriben evals de ruteo.

## Evals

- Portar los 5 casos actuales de `guion-video` a la hija que corresponda.
- Sumar 3 casos de ruteo contra `guion-video`: un pedido de historia, uno de anuncio
  y uno educacional, verificando que pide contexto primero y deriva a la hija correcta.
- Correr todo antes y después, y anotar el resultado en `VERSIONS.md`.

## Versionado

`guion-video` pasa a **2.0.0** (cambio mayor: cambia cómo se usa). Las hijas nacen en
**1.0.0**. Todo en el mismo commit, con su fila en `VERSIONS.md`.

## Verificación final

```bash
cd /home/user/muta-os
# Ninguna skill del plugin quedó sin Fase 0
grep -L "Contexto de la marca" plugins/creacion-contenido/skills/*/SKILL.md
# Las references no se duplicaron
find plugins/creacion-contenido -name "sistema-de-hooks.md" | wc -l   # esperado: 1
# El router no se quedó con esqueleto propio
grep -c "HOOK → DESARROLLO" plugins/creacion-contenido/skills/guion-video/SKILL.md  # esperado: 0
# Todas versionadas
grep -L "version:" $(find plugins -name SKILL.md)
```
