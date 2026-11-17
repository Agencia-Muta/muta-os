---
name: animar-monigote
description: >
  Anima al monigote de palitos de Muta (la persona de las historias que presenta Pupilo) con el motor de personajes del repo muta-recursos: planea los momentos, arma la escena, la renderiza a video o a imágenes fijas y la revisa cuadro por cuadro. Usala cuando alguien pida "animá un monigote", "un monigote que dude y después señale", "una escena con monigotes", "un video de palitos", o quiera copiar cómo se mueve un video de referencia en un monigote. No es para el monigote del estilo Explicador (HyperFrames, cuerpo de óvalo): ese tiene su propio LEEME en monigote-explicador/.
---

# Animar un monigote

El monigote es la persona de palitos de las historias de Muta: cabeza redonda, ojos de punto, cuerpo de un palo, brazos y piernas de dos tramos. Vive en el motor de personajes del repo `Agencia-Muta/muta-recursos`, carpeta `personajes-animados/`. Esta skill no trae código: todo sale de ese repo, así cada mejora del motor le llega a quien haga `git pull`.

Lo que hace que un monigote se vea vivo y no un robot, y que el motor ya resuelve si la escena lo pide (paso 4):
1. **Momentos encadenados.** Si dos momentos van seguidos, el cuerpo pasa de uno al otro sin volver a la pose de parado entre medio. Si una mano tendría que cruzar el pecho, atravesar la cara o pasar por el hombro, esa mano sola baja un instante al costado y después sigue, como lo haría una persona. Un momento sin pose propia (espera, asiente, niega) sigue en la pose del anterior.
2. **Nunca congelado.** En las pausas respira (el torso y la cabeza se mueven unos píxeles) y parpadea. Una mano que toca la cara o se rasca la cabeza se mueve con ella; con la cabeza apoyada en una pared o un brazo pegado a la cabeza, casi no se mece; las manos que sostienen algo arriba (cajas, cartel) o dan un apretón no respiran por su cuenta. Si dibujás un objeto que la mano sostiene, ubicalo desde las manos que devuelve `monigote(...)` (`g.manos`), no en un lugar fijo, o se despega.
3. **Poses que fluyen** (`fluye`): solo cambia algo cuando escribís claves a mano que siguen en el mismo sentido sin pausa. Con los momentos de la biblioteca casi no se nota, y está bien: donde la mano cambia de sentido, frenar es lo correcto.

## 1. Tener el repo al día

- Fijate si el repo ya está clonado (buscá una carpeta `muta-recursos` con `personajes-animados/motor/`). Si está, `git pull` antes de arrancar.
- Si no está: `git clone https://github.com/Agencia-Muta/muta-recursos.git`. Es privado: hace falta estar en la organización Agencia-Muta de GitHub. Si el clone da 404 o pide permisos, frená y decile a la persona que le pida acceso a Mati.
- Requisitos: Google Chrome, Node.js 20 o más y ffmpeg. Si falta alguno, decí cuál y cómo se instala, y esperá el OK antes de instalar nada. La primera vez, en `personajes-animados/motor/`: `npm install`.

No hagas commit ni push al repo: las escenas nuevas quedan en la compu de quien las hace, salvo que pida subirlas.

## 2. Leer antes de tocar

En `personajes-animados/`, en este orden:
- `CLAUDE.md`: las reglas de la estación (las rutas a carpetas fuera del repo que nombra son de otra compu; acá los videos salen a `motor/out/`).
- `monigote/LEEME.md`: qué poses, caras y momentos tiene, y cómo se encadenan.
- `Recursos de Personajes-animados/actuacion.md`: qué hace que una pose cuente (las manos dicen la emoción, preparar, actuar y asentar, rápido y sostenido).
- `motor/LEEME.md`: cómo se renderiza.

## 3. Planear los momentos con la persona

Antes de escribir código, proponé la escena como una lista de momentos con su segundo: "0,6 s duda (sostiene 0,6 s) → presenta → se queda señalando". Pedí el OK de la lista y el formato (9:16 para reel, 4:5 para feed).

Los momentos que existen (`MG_MOMENTOS` en `monigote/codigo/monigote.js`, y en video con `--escena=monigote-momentos`): espera, presenta, duda, no-entiende, se-entera, se-desinfla, angustia, no-puede-mas, desconfia, lee, muestra-cartel, encoge-hombros, festeja, carga, trato, asiente, niega, saluda, camina, corre. Lo que la persona pide en palabras se traduce a esos nombres: **señalar o mostrar algo es `presenta`**, pensar es `duda`, rascarse la cabeza es `no-entiende`. Un nombre que no existe corta con un error que lista los que hay.

Lo que conviene saber al encadenar: `festeja` termina parado (cae del salto); arrancar a correr (`corre`) justo después de otro momento es brusco; y al salir de `duda` el brazo pasa uno o dos cuadros por delante de la cara (lo hace la pose, también sin encadenar).

Si la escena pide algo que el monigote no sabe hacer, el momento nuevo se suma a `MG_MOMENTOS` (con su preparación, su acción y su asentado, desde una pose de `MG_POSES`), nunca dentro de la escena. Una pose nueva se muestra antes de usarla.

## 4. Armar la escena

Una escena es un archivo `monigote/escenas/monigote-<id>.js` más su fila en `motor/src/config.js` (`'monigote-<id>': { de: 'monigote', dur: <segundos>, w: 1080, h: 1920 }`). El molde, que es la escena de prueba `monigote-encadena.js`:

```js
(() => {
  const H = 430, GY = 1300;   // altura del monigote y línea del piso, en px
  const juana = MG_pista(
    [{ t: 0, x: 540, pose: 'parado' }, ...MG_encadena(0.6, [['duda', { sostiene: 0.6 }], ['presenta', { sostiene: 1.2 }]])],
    { pelo: 2 },                     // cómo es: pelo 0 a 3, canas, bigote
    { fluye: true, vive: true });    // siempre las dos prendidas en una escena nueva
  shots([[0, (t) => { monigote({ ...juana(t), gy: GY, H }); }]]);
})();
```

- `MG_encadena(t, pasos)`: los momentos seguidos, cada uno arranca cuando termina el anterior más su `sostiene`. El último vuelve a parado salvo `{ queda: true }`.
- Para caminar: el paso `['camina', { x: <destino>, dur: 1.6 }]`.
- Varios monigotes: una `MG_pista` por cada uno, con su `x`.
- No uses `MG_pista` sin `{ fluye: true, vive: true }` en una escena nueva: sin opciones queda como antes (para no cambiar piezas viejas) y se congela en las pausas.

## 5. Medir una referencia (si hay un video para copiar el movimiento)

Desde `motor/`, con el video bajado:
- `bash tools/sheet.sh ref.mp4 <inicio> <duración> <ancho:alto:x:y>`: hoja de todos los cuadros del tramo, para ver el timing. Ojo: este recorte va **ancho:alto:x:y** (el orden de ffmpeg).
- `python3 -I tools/track_edges.py ref.mp4 --start S --dur D --fps <fps del video> --crop x:y:ancho:alto --colors RRGGBB --cols C1,C2 --row R`: dónde están los bordes del sujeto en cada cuadro. Ahí se ve lo que el ojo no ve: cuánto se balancea, respira o se aplasta, y cada cuánto. Ojo: acá el recorte va **x:y:ancho:alto**, al revés que `sheet.sh`. Si todas las columnas salen "-", el recorte o el color están mal.
- `python3 -I tools/sample_colors.py ref.mp4 --at <segundo> X,Y`: el color exacto de un punto.

Pasá lo medido a la escena como números (cuánto dura cada momento, cuánto sostiene) y explicale a la persona qué tomaste de la referencia. Se copia el movimiento, nunca el dibujo ni el personaje de otro.

## 6. Renderizar y revisar antes de mostrar

Desde `motor/`:
- Hoja de cuadros sueltos: `node render.mjs --escena=monigote-<id> --sheet=0.5,1.2,2.4,3.3 --out=out/check/<id>.jpg`. Mirala vos antes de seguir.
- Si la salida del render muestra `[page error]`, frená: la escena no cargó y lo que salió es otra cosa (la demo del kit), aunque el comando termine bien. Leé el error, arreglá la escena y volvé a renderizar.
- Tramo cuadro por cuadro (contactos, cambios de pose): `node render.mjs --escena=monigote-<id> --strip=2.0:3.0 --out=out/check/tramo.jpg`.
- Video: `node render.mjs --escena=monigote-<id> --clip --out=out/<id>.mp4`.
- Imágenes fijas para un carrusel: `node render.mjs --escena=monigote-<id> --stills=1.2,3.3 --transparente --out=out/fijas`.

Antes de mostrar, chequeá en la hoja:
- Cada pose se lee sin cara (tapá la cabeza: si no se entiende, la pose no está).
- Nunca vuelve a parado entre dos momentos seguidos (salvo después de `festeja`).
- En las pausas se lo ve respirar: compará dos cuadros de la misma pausa separados un segundo y medio, el torso y la cabeza tienen que estar en otro lugar.
- Los brazos no se cruzan por delante del pecho ni atraviesan la cara al pasar de un momento a otro (`--strip` del cambio).
- Las manos tocan de verdad lo que agarran.

Entregá el video de `motor/out/` y una línea con qué hace el monigote y qué quedó sin mirar.
