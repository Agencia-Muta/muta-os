---
name: resumen-reunion
description: Convierte la transcripcion de una reunion en un resumen con contexto, lo que se discutio, lo que se definio y los pendientes de cada lado. Sirve para mandar por mail al cliente y para actualizar el estado del proyecto.
---

# resumen-reunion

Tomás la transcripción de una reunión (grabada, dictada en primera persona, o pegada como texto) y devolvés un resumen que le sirve a alguien que no estuvo ahí. Ese es el estándar: si el lector no puede reconstruir qué pasó y qué sigue sin preguntarte nada, el resumen no está terminado.

El mismo texto sirve para dos destinos, porque son el mismo contenido: el mail que le mandás al cliente después de la reunión, y la actualización de estado del proyecto en el gestor de tareas. No se generan dos versiones.

## Cuándo se usa

Cuando alguien pide resumir una reunión, una llamada o una transcripción para mandarla o registrarla. Se activa con "resumí la reunión con X", "pasame el resumen de esta llamada", "armá la actualización de estado de X", "resumí esto para mandarle al cliente".

No se usa para destilar una reunión de diagnóstico en una pieza diseñada para un prospecto: eso es otro trabajo, con otro documento y otro lector. Tampoco para extraer solo la lista de tareas sin el contexto alrededor.

## Qué necesito antes de empezar

| Insumo | Obligatorio | Si falta |
|---|---|---|
| La transcripción o el relato de la reunión | sí | pedirla. Sin material no hay resumen, y no se completa con lo que se supone que pasó |
| Quién es el cliente o el proyecto | no | si no está claro por el contenido, preguntarlo para el título |

## Proceso

1. Leé la transcripción completa antes de escribir nada. Identificá quién habla y qué rol tiene cada uno: cliente, equipo, proveedor.
2. Distinguí el tipo de material. Si es un relato en primera persona ("le dije que..."), reconstruí la conversación desde ahí. Si es una transcripción literal de una llamada, seguí el ida y vuelta real entre las partes.
3. Separá lo que pasó en cuatro cosas: el contexto de por qué se hizo la reunión, los temas que se discutieron, lo que quedó definido, y lo que quedó pendiente de cada lado.
4. Escribí el contexto como un párrafo de verdad, no como un bullet. Ese párrafo tiene que responder por qué se hizo la reunión, de qué se habló en general y hacia dónde quedó apuntando. Es lo primero que lee alguien que no estuvo.
5. En los temas discutidos, cada punto lleva el razonamiento, no solo el titular. "Se evaluó cambiar el formato del video" no dice nada; "se evaluó cambiar el formato del video porque el actual no funciona en el evento, donde la gente lo ve de pie y sin audio" sí. Si el punto no explica por qué se llegó ahí, todavía no está escrito.
6. Marcá las tensiones como pendientes de definición, no como reproches. Si el cliente dijo dos cosas incompatibles, el resumen dice "queda por definir si X o Y", no "el cliente se contradijo". El texto lo va a leer el cliente.
7. Separá los pendientes por lado: los de ellos y los nuestros. Sin esa separación, nadie sabe a quién le toca mover la próxima ficha, que es para lo que se manda el resumen.
8. Revisá que todo el texto sea mostrable al cliente tal cual está. Nada de comentarios internos sobre el trato, la cuenta o las personas. Si algo interno hace falta para el registro del proyecto, se agrega a mano después, no lo genera la skill.
9. Antes de entregar, releé el resumen preguntándote si alguien que faltó a la reunión entiende qué pasó y qué sigue. Lo que no se entienda sin haber estado, se explica.

## Formato de salida

Markdown, para pegar sin limpiar. Títulos en negrita, bullets con `*` y subpuntos con `-`.

```
### Reunión <Cliente o proyecto>

<Párrafo de contexto: por qué se hizo la reunión, de qué se habló y hacia dónde quedó apuntando. Dos a cuatro oraciones, corrido.>

### Qué se discutió

* <Tema, con el razonamiento de por qué se llegó ahí y qué posturas hubo>
  - <Detalle que hace falta para entenderlo>
* <Otro tema>

### Qué se definió

* <Definición concreta, con la razón que la sostiene>

### Pendientes de su lado

* <Lo que tiene que resolver o mandar el cliente>

### Pendientes de nuestro lado

* <Lo que tenemos que hacer nosotros>
```

Si alguna sección no tiene contenido, va igual con una línea que lo diga: "No se definió nada todavía, quedó todo abierto para la próxima". Sacar la sección hace parecer que no existía el tema.

Si la transcripción tiene huecos o algo quedó ambiguo, decilo dentro del resumen en el lugar donde corresponde, en vez de completarlo con lo más probable.

## Referencia

**Español rioplatense, con voseo.** Tono profesional y relajado, sin jerga interna y sin corporativismo. El registro es el mismo que usarías contándolo en voz alta, escrito prolijo.

**El equilibrio entre bullet y párrafo.** Un resumen que es solo punteo no sirve: quien no estuvo lee titulares y no entiende nada. Un resumen que es solo narrativa tampoco: nadie encuentra qué le toca hacer. El contexto va en párrafo, los temas y pendientes en bullets que llevan su porqué adentro.

**Por qué un solo texto para el mail y para el estado del proyecto.** Son la misma información leída por gente distinta en momentos distintos. Generar dos versiones abre la puerta a que digan cosas diferentes, y a que la del gestor de tareas quede desactualizada. Si al registrar el estado hace falta sumar algo interno, se agrega en ese momento sobre el texto ya escrito.
