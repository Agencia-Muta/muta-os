---
name: hook-lab
description: >
  Laboratorio de hooks: genera 10 hooks alternativos para un guion, idea de video o contenido existente, cada uno desde un ángulo estratégico distinto, para testear cuál detiene mejor el scroll. Usá esta skill cuando el usuario diga "dame hooks alternativos", "más opciones de hook", "el hook no funciona", "variantes de este hook", "hooks para testear", "qué hook uso", "10 hooks", o comparta un guion/idea pidiendo específicamente trabajar el gancho inicial. No escribe el guion completo (eso es guion-video): solo produce y evalúa hooks.
metadata:
  version: 1.2.0
---

# Hook Lab — 10 Hooks Alternativos para Testear

Generás variantes de hook desde ángulos estratégicos distintos. El hook decide en 1.5 segundos si el video vive o muere; testear variantes con el mismo body es la forma más barata de mejorar el rendimiento (la "prueba de Vergara": un solo contenido, distintos inicios, ver cuál conecta).

## Fase 0 — Contexto de la marca

No escribas una línea sin esto. Si falta, pedilo antes de arrancar.

1. **De qué marca es.** Qué vende, a quién, qué la diferencia de sus competidores. Si existe un brief, pedilo: es la fuente principal y reemplaza a cualquier suposición tuya sobre el rubro.
2. **En qué tono habla.** Preguntá si hay un archivo de tono y voz de la marca, y pedilo. Si existe, escribís en esa voz y respetás sus prohibiciones **aunque contradigan algo de esta skill: la guía de voz de la marca gana siempre.**
3. **Qué tiene que pasar después de la pieza.** El objetivo comercial concreto (una consulta, una reunión, una venta, dar a conocer algo), no la métrica de plataforma.

Si el usuario ya dio todo esto, no preguntes: ejecutá.

Si no hay brief ni guía de voz y el usuario quiere avanzar igual, escribí, y avisale en una línea que el registro va a salir genérico y va a haber que ajustarlo a mano.

**Idioma y registro:** los define la guía de voz de la marca. Si no hay guía, seguí el registro en el que escribe el usuario.

Un hook se escribe en la voz de la marca. Un hook buenísimo en el registro equivocado es un hook descartado.

**Antes de generar, leé el sistema completo:** `../guion-video/references/sistema-de-hooks.md` — ahí están las 5 macrocategorías, los 3 métodos para frenar el scroll, las 5 palancas psicológicas, el checklist de 10 preguntas y la lista de quemados. Todo hook que entregues tiene que tirar de al menos una palanca (los mejores tiran de dos) y pasar el checklist.

## Paso 1 — Analizá antes de generar

Del guion o idea que trajo el usuario, identificá (y comunicá en 3 líneas):
1. **Hook actual** (si existe): por qué funciona o falla
2. **Promesa/payoff del contenido:** qué recibe quien se queda — el hook nuevo tiene que poder cumplirse
3. **Audiencia y objetivo:** a quién le habla y qué acción busca. Si hay más de un perfil o dolor distinto (el del margen, el del stock, el que quiere marca propia), nombralos y repartí los diez hooks entre ellos, diciendo cuál apunta a cuál

Si falta el objetivo o la audiencia y no se deduce del contenido, preguntá una sola vez. Si se deduce, ejecutá.

## Paso 2 — Generá los 10 hooks

Uno por ángulo. Reglas para todos:
- Máximo 12 palabras habladas (3 segundos)
- Conversacional: algo que una persona diría de verdad en voz alta
- Habla del espectador, no de quien graba
- Debe poder cumplirse con el contenido real (nada de clickbait que defrauda)
- Prohibido: "tips y trucos", "el secreto que nadie te cuenta", "no te imaginás lo que pasó"

| # | Ángulo | Mecánica |
|---|---|---|
| 1 | **Mandato directo** | Orden sin rodeos: "Dejá de [error]." |
| 2 | **Curiosidad / loop** | Promesa que solo se cierra al final |
| 3 | **Error** | "Esto no te funciona porque lo hacés mal" |
| 4 | **Contrarian / mito** | Afirmación que contradice la creencia común |
| 5 | **Beneficio / resultado** | El resultado concreto primero |
| 6 | **Identificación / espejo** | La situación exacta del espectador ideal |
| 7 | **Dato / prueba** | Número o hecho que sorprende |
| 8 | **Historia** | "Te tengo que contar algo que me pasó…" |
| 9 | **Pregunta incómoda** | Interpela un punto ciego real |
| 10 | **Advertencia / urgencia** | "No hagas [X] si no querés [consecuencia]" |

## Paso 3 — Formato de entrega

```
🎯 Contenido: [1 línea] · Audiencia: [1 línea] · Objetivo: [1 línea]

 1. [MANDATO] "..."
 2. [CURIOSIDAD] "..."
 3. [ERROR] "..."
 ... (los 10)

⭐ TOP 3 PARA TESTEAR
1. #[n] — [por qué: 1 línea estratégica]
2. #[n] — [por qué]
3. #[n] — [por qué]

📱 Texto en pantalla: para el hook elegido, el gancho contextual (da contexto o curiosidad, no repite lo hablado; va arriba)
```

## Reglas de evaluación del Top 3

- Puntuá cada hook contra el checklist de 10 preguntas de `sistema-de-hooks.md`; el top 3 sale de ahí, no de tu gusto
- Priorizá especificidad sobre impacto genérico: "¿Llegás a fin de mes sin saber en qué se te fue la plata?" gana a "¿Querés ahorrar?"
- El mandato directo suele ganar cuando el tema lo permite: más corto, más fuerte
- Diversificá el top 3 por PALANCA, no solo por ángulo: uno intelectual (vacío/romper predicción), uno sensorial (escena/espejo), uno directo (payoff/problema). Tres sabores de contrarian no son tres opciones
- Si el video es un ad pago, incluí al menos un hook de identificación/espejo (para prospección fría) y uno de resultado (para retargeting)
- Al entregar el top 3, decí qué compromete cada elección: un hook contrarian compromete el desarrollo a defender la afirmación; una escena compromete a resolverla; un salto temporal compromete a mostrar el camino entre los dos momentos

## Qué es un concepto (para evitar malentendidos)

Un concepto publicitario es la intersección de una oferta, un ángulo y una audiencia, en un formato concreto (video, estática, carrusel). Un hook es la puerta de entrada de un ángulo, no un concepto entero. Cuando el usuario pida "un concepto nuevo", aclará qué cambia: ¿la oferta, el ángulo, la audiencia o el formato? Cuando pida "otro ángulo", generá hooks desde otra palanca o mecánica distinta.

Si el usuario después quiere el guion completo con el hook elegido, derivá a la skill `guion-video`. Si necesita diversificar por nivel de conciencia para campañas, derivá a `matriz-diversificacion`.
