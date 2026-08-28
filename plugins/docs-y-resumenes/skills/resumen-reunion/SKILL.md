---
name: resumen-reunion
description: Convierte una reunion en un resumen con contexto, lo que se definio con su razonamiento y los pendientes por responsable. Sirve para mandar por mail al cliente y para actualizar el estado del proyecto.
metadata:
  version: 1.0.0
---

# resumen-reunion

Tomás una reunión y devolvés un resumen que le sirve a alguien que no estuvo ahí. Ese es el estándar: si el lector no puede reconstruir qué pasó y qué sigue sin preguntar nada, no está terminado.

El mismo texto sirve para el mail al cliente y para la actualización de estado del proyecto: es la misma información. No se generan dos versiones, porque se desincronizan.

## Cuándo se usa

Cuando alguien pide resumir una reunión, una llamada o una transcripción para mandarla o registrarla. Se activa con "resumí la reunión con X", "armá la actualización de estado de X", "pasame el resumen de esta llamada para mandarle al cliente".

No se usa para destilar una reunión de diagnóstico en una pieza diseñada para un prospecto: eso es otro documento y otro lector.

## Qué necesito antes de empezar

| Insumo | Obligatorio | Si falta |
|---|---|---|
| La reunión: transcripción completa, audio o relato en primera persona | sí | buscarla en el grabador si hay uno conectado; si no, pedirla. Nunca trabajar sobre el resumen automático del grabador: se pierde el razonamiento y queda el titular |
| Cliente o proyecto | no | inferirlo del contenido |

## Proceso

1. Conseguí la reunión completa. Si hay un grabador conectado, traé la transcripción entera, no su resumen automático.
2. Leela toda antes de escribir. Identificá quién habla y qué rol tiene: cliente, equipo, proveedor externo.
3. Si es un relato en primera persona ("le dije que..."), reconstruí la conversación desde ahí. Si es una transcripción literal, seguí el ida y vuelta real.
4. Listá los temas que se tocaron y, para cada uno, en qué quedó: definido, abierto, o pendiente de un dato.
5. Escribí el contexto como un párrafo corrido de dos a cuatro oraciones: por qué se hizo la reunión, cómo salió en general, y qué queda en el aire. Es lo primero que lee alguien que no estuvo.
6. Escribí cada tema en un solo lugar, con el qué y el porqué juntos. No separes "lo que se discutió" de "lo que se definió": en casi todos los temas se discutió y se resolvió, y separarlos hace que el resumen diga todo dos veces y salga al triple de largo.
7. Máximo dos subpuntos por tema. El tema que necesita más se gana su propia sección con título y párrafo, como cualquier asunto que tenga peso propio (una decisión técnica con varias implicancias, un tema comercial que se abrió al final).
8. Los eventos futuros acordados (una filmación, un viaje, una presentación, una visita) no se comprimen: llevan cuándo son, qué formato tienen y qué hay que preparar, y su preparación aparece en pendientes. Son lo más caro de perder en un resumen, porque tienen fecha de vencimiento: un color mal puesto se corrige cuando se ve, un viaje sin preparar se pierde.
9. Marcá qué no bloquea. Si algo puede resolverse después sin frenar lo demás, decilo: evita que alguien detenga el proyecto esperando algo opcional.
10. Cruzá definiciones contra pendientes: toda definición que implique trabajo (instalar algo, producir algo, cambiar algo) tiene que aparecer en pendientes con su responsable. Una definición sin pendiente es una tarea que nadie va a hacer.
11. Agrupá los pendientes por responsable, con el nombre de cada uno. No los separes en "nuestro lado" y "el de ellos": hay freelances y terceros que no entran en ninguno, y adentro de cada lado hay gente distinta que necesita saber qué le toca.
12. Las tensiones van como pendientes de definición, nunca como reproches. Si dijeron dos cosas incompatibles, el texto dice "queda por definir si X o Y".
13. Revisá que todo sea mostrable al cliente tal cual está. Nada de comentarios internos sobre el trato, la cuenta o las personas. Si al registrar el estado hace falta algo interno, se suma a mano en ese momento.
14. Antes de entregar, releelo preguntándote si alguien que faltó entiende qué pasó y qué sigue. Lo que no se entienda sin haber estado, explicalo.

## Formato de salida

Markdown para pegar sin limpiar.

```
### <Qué pasó> | <fecha>

<Párrafo de contexto: por qué se hizo la reunión, cómo salió y qué queda en el aire. Dos a cuatro oraciones, corrido, sin subtítulo.>

### Definiciones

* <Qué quedó definido, con la razón que lo sostiene, en la misma línea>
  - <Como máximo dos subpuntos, solo si hacen falta para entenderlo>

### <Tema con peso propio>

<Párrafo. Solo para lo que no entra en un bullet: una decisión técnica con implicancias, un asunto que se abrió aparte. Acá va también qué no bloquea al resto.>

### Pendientes

**<Nombre o área>**
* <Lo que tiene que resolver o mandar>

**<Otro nombre o área>**
* <Lo suyo>
```

El título dice qué pasó, no que hubo una reunión: "Diseño aprobado + pendientes de contenido | 21 ago" sirve, "Reunión con el cliente" no.

Si una sección no tiene contenido, va igual con una línea que lo aclare. Sacarla hace parecer que el tema no existió.

Si la transcripción tiene huecos o algo quedó ambiguo, decilo donde corresponde en vez de completarlo con lo más probable. Los nombres propios de herramientas, montos y fechas se transcriben exactos: si no se entendió cuál se dijo, marcalo en vez de poner el que suena parecido.

## Referencia

**Voz.** Español rioplatense con voseo, profesional y relajado, sin jerga interna ni corporativismo. El registro es el de contarlo en voz alta, escrito prolijo.

**El equilibrio.** Un resumen que es solo punteo no sirve: quien no estuvo lee titulares y no entiende nada. Uno que es solo narrativa tampoco: nadie encuentra qué le toca. El contexto y los temas de peso van en párrafo, el resto en bullets que llevan su porqué adentro.

**Largo.** Si no entra en una pantalla, sobra algo, y casi siempre es un tema contado dos veces o un subpunto que no cambia ninguna decisión.
