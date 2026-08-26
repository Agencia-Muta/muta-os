---
name: resumen-prospecto
description: >
  Destilá una reunión estratégica con un prospecto o cliente en un documento HTML
  con diseño Muta, listo para enviarle: la versión en limpio de lo charlado, más
  clara y linda que el resumen automático de Fathom o Gemini. No es una propuesta
  ni una minuta interna: es el regalo post-reunión que genera efecto "me escuchó
  y me ordenó el negocio". Activá esta skill cuando el usuario diga "armá el
  resumen prospecto de X", "pasame en limpio la reunión con X", "armá el en
  limpio de X", "armá la hoja de ruta de la reunión con X", "destilá la reunión
  con X para mandársela", "hacele el resumen lindo a X", "analizá la reunión que
  tuve con X y armale un documento para ellos", o cualquier variante de convertir
  una reunión de diagnóstico/estrategia en un entregable para esa persona.
  También cuando el usuario pegue una transcripción y pida "el en limpio de esto".
  No usar para: extraer pendientes o tareas internas (reunion-pendientes),
  primera reunión de proyecto web (prd-web-primera-reunion), ni propuestas
  comerciales (estrategia comercial).
---

# Resumen Prospecto

El documento que produce se titula "En Limpio": ese es el nombre que ve el cliente. Resumen-prospecto es solo el nombre interno de la skill.

## Qué produce

Un solo archivo HTML (sin markdown intermedio) que pasa en limpio lo hablado en una reunión estratégica, para que el cliente se lleve algo claro y accionable aunque la reunión haya ido y venido mil veces. El lector es **el cliente**, no la agencia. El efecto buscado: "no solo estuvo buena la reunión, me fui con un mapa". Vale igual si la relación no avanza: el documento se sostiene solo.

## Flujo

1. **Conseguir la transcripción.** Si el usuario la pegó, usala directo. Si nombró la reunión o el cliente, buscala en Fathom: `search_meetings` con `recorded_by: "anyone"`, probando variantes del nombre (con y sin tilde, apellido solo). Si no aparece, `list_meetings` por fecha y confirmá con el summary que es la reunión correcta (los títulos de Fathom mienten seguido: "Impromptu Meeting" puede ser la reunión clave).

2. **Bajar el transcript completo y leerlo entero.** Si el output es muy grande, persistilo a archivo y leelo por tramos hasta cubrir el 100%. No uses el summary automático de Fathom como fuente: pierde el framing del que diagnosticó, mezcla idiomas y garblea frases. El valor del documento está en CÓMO lo dijo quien dio el diagnóstico en la reunión (sus metáforas, sus ejemplos, su orden), y eso solo está en el transcript.

3. **Chequeo de sustancia.** Si la reunión fue pura logística o no hubo diagnóstico real (nada de tesis, problemas, orden recomendado), avisale al usuario que no hay material para un En Limpio y frená. Mandar un "resumen estratégico" vacío quema más que no mandar nada.

4. **Extraer del transcript:**
   - La **tesis de fondo**: la idea única de la que cuelga todo (ej: "el producto es fácil de copiar, la marca no").
   - Las **lecturas madre** (4 a 6): las decisiones o hallazgos principales.
   - Los **hallazgos desarrollables** (dan 5 a 9 secciones): diagnósticos, el orden recomendado, qué no hacer.
   - Las **metáforas y ejemplos textuales de quien dio el diagnóstico** (el auto con la rueda pinchada, el lemon pie): conservalas, son el diferencial contra un resumen genérico. Si el transcript viene garbleado en un tramo, reconstruí el sentido; nunca cites garble literal.
   - Las **palabras del propio cliente**: usarlas en títulos y cuerpo hace que el documento se sienta hecho a medida.

5. **Rutear la carpeta.** Cliente activo (existe `Clientes/<X>/` o figura en `Clientes/clientes-activos.md`) → `Clientes/<X>/`. Si no → `Prospectos/<X>/` (creá la subcarpeta si hace falta). Nunca pises archivos previos.

6. **Redactar** aplicando las Reglas de copy y la Sanitización de abajo. Si los principios de voz no están en contexto, leé `00-recursos/principios-de-voz.md` (registro: voz de la agencia).

7. **Generar el HTML desde `assets/template.html`.** Copiá el template y completá los placeholders `{{...}}`. El CSS y la estructura fija no se tocan ni se regeneran: son la marca del documento. Repetí los bloques marcados con `<!-- REPETIR -->` según la cantidad real de lecturas y secciones.

8. **Guardar** como `en-limpio-<cliente>-v1.html` (si ya existe v1, versionar: v2, v3...).

   Si el usuario pide correcciones después de generado, editá ese mismo HTML directo (o versioná si el cambio es grande). Nunca crees un markdown paralelo: el HTML es el único archivo fuente.

9. **Verificar y entregar.** Antes que nada: `grep -c "{{" archivo.html` tiene que dar 0 (un placeholder sin reemplazar llegando al cliente es el peor bug posible de esta skill). Después, si hay browser disponible, chequeo rápido de render. Entregá la ruta y un resumen de qué secciones salieron. **La skill nunca envía el documento**: el usuario lo revisa y lo manda.

## Cuando el documento se manda por mail

- **Asunto: `En Limpio | <Nombre del negocio>`.** Siempre, sin variantes: el correo se rastrea por el nombre del negocio en el asunto, igual que con los presupuestos.
- **El PDF adjunto se llama `En Limpio - <Nombre del negocio>.pdf`.** El sufijo de versión (`-v1`) es del archivo de trabajo, nunca del que ve el cliente: si hay que versionar, se adjunta una copia con el nombre limpio.
- No enhebrar la respuesta en el hilo de mail por el que llegó la consulta si ese hilo arranca pidiendo tarifario o presupuesto. Mail nuevo, para que el documento se reenvíe internamente sin ese encabezado arriba.

## Estructura fija (los muebles)

Esto es idéntico en todos los documentos; es lo que lo hace reconocible como pieza de Muta:

- Kicker: `Diagnóstico estratégico`
- Título: `En Limpio`
- Subtítulo: `Lo que vemos en [Cliente] y en qué orden lo resolveríamos. El mapa del análisis.`
- Intro: 2 párrafos (qué es el doc + la tesis de fondo en negrita)
- Bloque `Las lecturas que ordenan todo` (sin número en el título: la cantidad varía)
- Cada sección cierra con el callout `La bajada` (1-2 oraciones accionables)
- Última sección: `La foto completa`, con caja negra de cierre y frase de fuerza
- Footer Muta

Variable: cantidad de lecturas (4-6), cantidad de secciones (5-9), títulos de secciones, todo el contenido.

Los ejemplos entre comillas de este documento ("la web está de adorno", "el lemon pie", "346 reseñas") salen de reuniones pasadas: ilustran el formato, no se reutilizan. Cada documento saca sus títulos, metáforas y frases de SU transcript. Si un En Limpio suena parecido a otro, falló.

## Fórmula de títulos de sección

Los títulos nombran el hallazgo del cliente, nunca la categoría. De ahí sale el efecto "esto es sobre MI negocio":

- Sentencia afirmativa corta, máximo 7 palabras.
- Nombra el hallazgo, no el tema: "La web está de adorno", no "Sitio web".
- Si hay número o imagen concreta de la reunión, usala: "346 reseñas, ninguna respondida".
- Prohibido el sustantivo abstracto solo: Estrategia, Posicionamiento, Contenido, Optimización, Branding.

## Reglas de copy

- **Abrir en afirmativo.** Decir qué es el documento, nunca qué no es. Prohibido "esto no es un informe/una propuesta/un resumen".
- **El documento no muestra la cocina.** Nada de proceso interno: "fuimos y vinimos", "lo que traían era", "nadie habló de esto todavía", "las métricas que estaban mirando", "se diluyó en la charla", "está en el informe de research". El cliente recibe el resultado limpio, no el backstage. Si un hallazgo nació de un error del cliente, se formula como principio general ("hay una tentación lógica: ..."), no como corrección con nombre.
- **Cero mención a propuesta, presupuesto o próxima venta.** El documento se sostiene solo y puede ir a gente que nunca va a recibir una propuesta. Prohibido "antes de la propuesta", "cuando veamos la propuesta", "en la cotización".
- **Solo material de la reunión.** Sin research web, sin datos externos, sin cifras que no se hayan dicho en la charla. Si falta un dato, no se inventa: se omite o se formula sin número.
- **Rastreabilidad total.** Todo lo afirmado tiene que ser rastreable a la reunión. Se puede desarrollar, dar contexto, completar el porqué de algo que quedó a medias. No se puede agregar consejo nuevo que no se dio: si al redactar aparece algo que "faltó decir", consultalo con el usuario antes de incluirlo, nunca lo metas en silencio. La autoridad del documento es de quien dio la reunión; la skill solo ordena.
- Español rioplatense, voseo, "ustedes" al cliente. Voz de la agencia: profesional, práctica, sin corporativismo.
- Nunca guión largo (—) como puntuación. Nunca guión corto como coma aclaratoria: coma o paréntesis.
- Argumentos con la mecánica del que diagnosticó: problema concreto → ejemplo → conclusión corta y rotunda. Pregunta retórica con respuesta inmediata ("¿Por qué? Porque...").
- Cierre del documento siempre en positivo: qué tienen a favor y qué sigue.

## Sanitización (siempre activa, sin preguntar)

Antes de emitir, pasá esta lista sí o sí. Va a haber automatización después, así que no hay humano de respaldo:

- **Otros clientes de Muta** nombrados al pasar → anonimizar ("un cliente nuestro", "una marca que trabajamos"). Nunca nombre ni rubro identificable junto a números. **Los datos del propio cliente sí entran**: son suyos.
- **Montos y fees de Muta** (precios de servicios, rebranding, rangos mensuales) → afuera siempre.
- **Tácticas grises o legalmente sensibles** dichas en confianza (chamuyos de stock, jugadas impositivas) → versión limpia y defendible, o no van.
- **Trapos sucios del propio cliente** (conflictos, episodios feos, quejas de terceros) → se mencionan solo si son parte del diagnóstico, con dignidad y framing constructivo.
- **Personas del equipo del cliente** criticadas en la reunión (la CM que "no hace nada") → el problema se atribuye al sistema ("sin estrategia definida"), nunca a la persona con nombre.
- Revisar tildes y ortografía completa antes de entregar.
