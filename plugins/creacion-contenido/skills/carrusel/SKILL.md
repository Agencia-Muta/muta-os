---
name: carrusel
description: >
  Diseñador de carruseles para Instagram y LinkedIn: estructura slide por slide con títulos, textos y sugerencias visuales, listos para armar en Canva o Figma. Usá esta skill siempre que el usuario quiera crear, mejorar o estructurar un carrusel. Se activa con pedidos como "armá un carrusel", "carrusel de X slides", "pasá este contenido a carrusel", "carrusel para Instagram", "carrusel para LinkedIn", "qué pongo en cada slide", o cuando el usuario comparte un carrusel existente para revisión. Entrega estructura y textos, no genera las imágenes.
metadata:
  version: 1.1.0
---

# Carrusel — Estructura Slide por Slide

Convertís ideas, guiones o contenidos en carruseles con estructura probada. El carrusel es el formato que más guardados genera: se consume a ritmo propio y funciona como mini-landing. Entregás textos finales por slide + sugerencia visual, listos para que el equipo los arme en Canva.

## Fase 0 — Contexto de la marca

No escribas una línea sin esto. Si falta, pedilo antes de arrancar.

1. **De qué marca es.** Qué vende, a quién, qué la diferencia de sus competidores. Si existe un brief, pedilo: es la fuente principal y reemplaza a cualquier suposición tuya sobre el rubro.
2. **En qué tono habla.** Preguntá si hay un archivo de tono y voz de la marca, y pedilo. Si existe, escribís en esa voz y respetás sus prohibiciones **aunque contradigan algo de esta skill: la guía de voz de la marca gana siempre.**
3. **Qué tiene que pasar después de la pieza.** El objetivo comercial concreto (una consulta, una reunión, una venta, dar a conocer algo), no la métrica de plataforma.

Si el usuario ya dio todo esto, no preguntes: ejecutá.

Si no hay brief ni guía de voz y el usuario quiere avanzar igual, escribí, y avisale en una línea que el registro va a salir genérico y va a haber que ajustarlo a mano.

**Idioma y registro:** los define la guía de voz de la marca. Si no hay guía, seguí el registro en el que escribe el usuario.

## Principios del carrusel

- **La portada es un hook, no un título.** Compite en el feed igual que un reel: titular punzante ≤8 palabras + subtítulo de una línea que concreta la promesa. Usá el sistema de hooks de la skill `guion-video` (`references/sistema-de-hooks.md`): mismas macrocategorías, mismas palancas, mismo checklist. Si la portada no funcionaría como post independiente, reescribila.
- **Escribí la portada AL FINAL.** Recién cuando sabés qué entrega el carrusel podés escribir la portada que lo merece.
- **Una idea por slide.** Si un slide necesita dos párrafos, son dos slides.
- **Cada placa achica el embudo y profundiza la emoción.** El mejor carrusel funciona como un AIDA intra-contenido: la portada filtra a quién le habla, y cada slide conecta un poco más con la sensación mientras acerca a la acción.
- **Cada slide vende el swipe al siguiente.** Cortá las ideas en el punto de tensión: mini open-loops, un número que sigue ("...y eso que este era el 3"), un teaser ("el que sigue me sorprendió").
- **El slide 2 es el más abandonado.** Después de la portada, el slide 2 tiene que confirmar la promesa (contexto en 1-2 oraciones que tocan el dolor), no dar relleno.
- **La anteúltima slide entrega el payoff completo; la última pide UNA acción.**
- **Texto mínimo:** título ≤ 8 palabras, cuerpo ≤ 30 palabras por slide. Los títulos hacen el trabajo pesado: la gente escanea — si el título en negrita no comunica el punto solo, reescribilo. Son referencia, no límite duro: lo que manda es que cada slide tenga una sola idea y se sostenga solo.
- **Señales de formato:** `→` para contraste o énfasis, números para procesos, negrita para la frase clave.
- **Legible en móvil:** el diseño lo resuelve Canva, pero el texto tiene que funcionar sin imagen de apoyo.

## Patrones de slide del cuerpo

| Patrón | Estructura |
|---|---|
| Tip | Título en negrita + 1-2 líneas de contexto o ejemplo |
| Paso | "Paso N:" + qué hacer + por qué funciona (1 oración) |
| Contraste | Forma incorrecta → forma correcta, en dos líneas |
| Dato | Número sorprendente + insight de una oración |

## Flujo de trabajo

```
1. ¿Qué hay? (tema / contenido largo a adaptar / guion existente)
2. Objetivo y funnel (guardados / leads / venta → define fórmula y CTA)
3. Elegir fórmula y mapearla a slides (tabla de abajo)
4. Escribir slide por slide
5. Auditar con el checklist
```

Si el contenido viene de un guion de video, no lo pegues cortado: re-estructuralo — el carrusel no tiene audio ni ritmo de edición, cada slide debe sostenerse solo.

## Mapeo fórmula → slides

| Fórmula | Slides sugeridos | Distribución |
|---|---|---|
| **PAS** | 5-6 | S1 portada=Problema (hook) · S2-3 Agitación · S4-5 Solución · S6 CTA |
| **AIDA** | 6-8 | S1 Atención · S2-3 Interés · S4-6 Deseo · última Acción |
| **BAB** | 6-7 | S1-2 Antes · S3-4 Después · S5-6 Puente · S7 CTA |
| **Lista / Tips** | n+3 | S1 portada ("N errores/claves de X") · S2..n+1 un ítem por slide · S n+2 síntesis o giro · última CTA |
| **PASTOR** | 8-10 | S1 Problema · S2 Amplificar · S3-4 Historia · S5-6 Testimonio · S7-8 Oferta · última Respuesta/CTA |
| **Tutorial** | pasos+3 | S1 promesa del resultado · un paso por slide con resultado parcial · cierre con resultado final · CTA |
| **Narrativo (historia)** | 6-8 | S1 momento cumbre o conflicto · desarrollo con un beat por slide (therefore/but) · payoff · CTA |

Detalles, templates y errores de cada fórmula: `references/../../estructuras-copy/references/manual-estructuras.md` (manual completo de la skill estructuras-copy). Consultalo si la fórmula elegida no te resulta familiar.

Para carruseles narrativos (una historia, un caso, una transformación) no estás limitado a las fórmulas de copy: cualquiera de las 10 estructuras narrativas del manual (`references/../../estructuras-copy/references/manual-estructuras.md`, Sección 2) se puede mapear a slides con la misma lógica de un beat por slide. La guía rápida de uso al final de ese manual rutea qué estructura conviene según el objetivo (awareness, consideración, conversión). Antes de elegir estructura, leé también `references/frameworks-narrativos.md` de esta skill (cinco arquitecturas de carrusel según la razón para seguir swipeando).

## Especificaciones por plataforma

| Plataforma | Slides | Ratio | Densidad de texto | Caption |
|---|---|---|---|---|
| **Instagram** | 8-10 como referencia (la plataforma admite hasta 20) | 4:5 vertical (1080×1350), nunca horizontal | Baja: ~30 palabras máx por slide | Hook en la línea 1 (primeros 125 caracteres); el CTA sale del objetivo comercial |
| **LinkedIn** | 7-12 (ideal 9-10) | 1:1 o 4:5 (documento PDF) | Alta: el lector de LinkedIn tolera profundidad | Hook + teaser de 1-2 párrafos; link en el primer comentario, nunca en el cuerpo |
| **TikTok (photo carousel)** | 6-12 | 9:16 (1080×1920) | Mínima: **el texto en imagen ES el hook**, <6 palabras por slide | <150 caracteres; sumá audio en tendencia (TikTok premia carruseles con música) |

Si el contenido lo permite, la última slide puede cerrar el loop con la primera.

## CTA de última slide

El cierre de toda pieza sale del objetivo comercial declarado en la Fase 0, nunca de una métrica de plataforma. Seguidores, guardados, alcance y compartidos no son objetivos de una pieza: son efectos secundarios que a veces acompañan.

Una sola acción por pieza. Si pedís dos cosas, no conseguís ninguna. Y antes de escribir el CTA, preguntate si hace falta: si la acción es la consecuencia lógica del contenido, no se dice.

**CTAs prohibidos como cierre por defecto:**
- "Seguime para más [tema]"
- "Comentá SÍ si te pasó"
- "Mandáselo a alguien que lo necesita"
- "Guardá este reel / este carrusel"
- "Link en bio" como cierre suelto, sin acción comercial concreta detrás

Cualquiera de esos puede aparecer solo si el objetivo comercial declarado lo justifica de verdad (por ejemplo, un lead magnet que se entrega por comentario), nunca como relleno de cierre.

## Formato de entrega

```
🎠 CARRUSEL: [título interno]
📊 Objetivo: [guardados/leads/venta] · Fórmula: [X] · Slides: [N]

SLIDE 1 — PORTADA (hook)
Título: [...]
Visual: [sugerencia concreta: foto, ícono, contraste, estilo]

SLIDE 2 — [función en la fórmula]
Título: [...]
Texto: [...]
Visual: [...]

[... resto de slides ...]

SLIDE N — CTA
Texto: [...]
Visual: [...]

📝 CAPTION: [2-4 líneas que complementan sin repetir el carrusel; primera línea = hook propio]
```

No se piden ni se sugieren hashtags en ninguna entrega. Las palabras por las que la marca quiere aparecer van escritas dentro del copy, en una frase que se lee natural. Si una guía de voz de marca específica pide hashtags, gana la guía de esa marca.

## Checklist pre-entrega

- ¿La portada frena el scroll sin contexto? (leela sola: ¿la tocarías?)
- ¿El slide 2 confirma la promesa en vez de dar relleno?
- ¿Cada slide tiene una sola idea y ≤ 30 palabras de cuerpo?
- ¿El corte de cada slide deja tensión para el swipe?
- ¿El payoff está en la anteúltima slide?
- ¿La última slide pide una sola acción alineada al objetivo?
- ¿La caption suma (contexto, historia o CTA reforzado) en vez de repetir?
- ¿Cero frases prohibidas? ("el secreto que nadie te cuenta", "no te lo pierdas", superlativos vacíos)
