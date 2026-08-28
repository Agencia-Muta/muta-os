---
name: informe-de-mercado
description: >
  Genera un informe de investigación de mercado para un cliente o prospecto de Muta Digital, replicando el proceso del informe "Antes de Cebar" (Lift). Doble momento de uso en proyectos de identidad de marca: al inicio como base de entendimiento del mercado para el equipo de diseño, y al final como pieza que se entrega al cliente junto con el manual de marca (valor que el cliente no contrató). También sirve suelto, para un prospecto o como regalo de cierre de etapa. Activar cuando alguien de Muta pida "armá el informe de mercado de X", "el panorama de la categoría de X", "la investigación de mercado para el branding de X", "el informe estilo Antes de Cebar para X". Funciona con o sin reunión previa con el cliente. NO es para investigar los compradores de un negocio y armar anuncios (eso es 7-maletas), ni para bajar pendientes de una reunión (eso es reunion-pendientes), ni para documentos de requerimientos web (eso es prd-web-primera-reunion).
metadata:
  version: 1.0.0
---

# Informe de Mercado

## Qué es

Una investigación de la categoría en la que juega un cliente o prospecto de Muta, convertida en un informe HTML con estética Muta. Replica el proceso validado con el informe "Antes de Cebar" de Lift.

**Se produce una vez y se usa dos veces** dentro de un proyecto de identidad de marca:
1. **Al inicio**: el equipo de diseño lo usa como base de entendimiento del mercado antes de arrancar.
2. **Al final**: se entrega al cliente junto con el manual de marca. El cliente recibe algo que no contrató; eso construye experiencia y valor percibido.

También se ejecuta suelto: para un prospecto que se quiere ganar, o como regalo de cierre de una etapa de trabajo.

**Es un informe, no un plan.** Entrega datos, casos, gaps y observaciones; el cliente decide. Nunca prescribe, por dos razones de fondo: un plan queda fechado (si el cliente entra como cuenta meses después, el plan ya no vale y hay que desdecirse) y transfiere a Muta la responsabilidad de la ejecución (si lo ejecutan mal, "el plan era una porquería").

**El informe no opina de diseño.** De los competidores documenta tono, formas y estrategias de comunicación (ángulos textuales, tipo de contenido, jugadas como "legitimidad ANMAT"). Nada de paletas, packaging ni tipografías: las decisiones visuales son del equipo de diseño.

**Cuándo NO usar esta skill:**
- Investigar los compradores de un negocio para armar anuncios → `7-maletas`.
- Bajar pendientes o resumen de una reunión → `reunion-pendientes`.
- Documento de requerimientos de un sitio web → `prd-web-primera-reunion`.

## Niveles de input (nunca esperar una reunión)

El informe se produce completo en los tres escenarios. Antes de preguntar si hubo reunión, **buscarla en Fathom**. Nunca frenar el proceso por una reunión que no existe, ni mencionar su ausencia en el documento.

- **Nivel 0 — Sin reunión** (caso base, el más común): el cliente vino directo a pedir identidad de marca. Todo sale de desk research. El informe sale completo; las subsecciones de lectura del terreno que dependen de criterio estratégico de reunión se acortan y se sostienen solo con la evidencia de los casos.
- **Nivel 1 — Reunión creativa** (la tuvo Flor o el área de diseño): extraer referencias, gustos, sensación de marca buscada, a quién creen que le hablan, historia de la marca. Permite contrastar lo que el cliente cree de su categoría con lo que el mercado muestra.
- **Nivel 2 — Reunión estratégica** (la tuvo Mati): además de lo anterior, extraer las dinámicas de negocio y el criterio competitivo que se habló. Habilita la lectura del terreno completa.

Si hay reunión: es fuente de ideas y disparadores. **Jamás se cita ni se menciona en el documento** (regla de sanitización).

## Scoping (máximo 3 preguntas, solo lo que cambia el informe)

1. **Quién es el cliente y qué vende o lanza** (producto, categoría, país).
2. **¿Hay reunión grabada?** (buscar en Fathom primero; preguntar solo si la búsqueda es ambigua).
3. **Contexto del proyecto**: branding en curso, prospecto a ganar, o regalo suelto de cierre.

Con eso, avanzar. Decisiones menores se toman como supuesto y se marcan.

## Fase 1 — Investigación en enjambre (3 agentes Sonnet)

El research pesado lo hacen agentes Sonnet en paralelo; el modelo principal instruye, verifica en disco y redacta.

Reglas de orquestación (no negociables):
- **Prompts autocontenidos**: cada agente recibe todo el contexto que necesita.
- **Prohibido que los agentes creen sub-agentes.**
- **1 tema = 1 agente.**
- **Cada agente escribe su output a un `.md` en disco** (checkpoint para retomar tras cortes de sesión).
- **Verificar en disco antes de dar por bueno**: abrir el archivo, spot-check de links y cifras. Nunca confiar en el self-report del agente.
- Procesar los resultados a medida que llegan.

**Agente A — Mercado (macro).** Crecimiento del rubro en la región y el país en los últimos 3-5 años, con fuentes; la tendencia madre; señales de demanda (búsquedas, notas de medios masivos, foros). Toda cifra con URL; lo que no existe se declara. Las cifras de consultoras se tratan como rangos (las metodologías no son comparables entre sí).

**Agente B — Casos homólogos internacionales.** Marcas que resolvieron el mismo problema que el cliente (mismo patrón de negocio, no necesariamente el mismo rubro). Por caso: web linkeada, país y año, nicho inicial, táctica de crecimiento documentada por terceros (prensa de negocios, case studies), ángulos de venta textuales de su comunicación, modelo de recompra. Cerrar con los patrones comunes entre casos.

**Agente C — Competidores locales + objeciones.** Búsqueda abierta para descubrir jugadores (no limitarse a los que el cliente o Muta ya conocen). Pregunta clave: ¿existe ya el formato o propuesta exacta del cliente? Por competidor: web, qué vende, ángulos de venta textuales, tono y estrategia de comunicación, tipo de contenido que le funciona, redes. Objeciones reales de la categoría desde Mercado Libre (reviews negativas y preguntas al vendedor, citas textuales). Sin análisis de precios salvo pedido explícito. Si Mercado Libre o Instagram bloquean con muro de login, declararlo como limitación y seguir.

Salida de cada agente: un `.md` en `research/` con fuente linkeada por afirmación y sección final "Qué no encontré".

## Fase 2 — Construcción y auditoría

1. **Verificar los archivos en disco**: links reales (spot-check con curl), cifras con fuente, cobertura completa. Re-lanzar un agente puntual si algo quedó flojo.
2. **Construir el informe completo en Markdown** (documento de trabajo interno, NO el entregable).
3. **Auditar el Markdown** contra las reglas de contenido e iterar hasta que el contenido esté cerrado.
4. **Recién entonces, diseñar el HTML.** Nunca diseñar y corregir después: sobre el HTML solo chequeos de renderizado.

## Estructura del informe (de mayor a menor, la del informe Lift)

0. **Resumen ejecutivo** (1 página): las lecturas clave numeradas. Si solo leen esto, ya sirve.
1. **El mercado**: crecimiento con fuentes (presentado como rangos), la tendencia madre, señales de demanda. **Al menos un gráfico** (SVG embebido, estética Muta) si hay data de mercado.
2. **Casos de éxito internacionales**: con **criterio de selección explícito** (los tres filtros: misma tesis que el cliente, tracción verificable con cifras públicas de terceros, estrategia documentada) y **tabla de tracción verificada** por caso con fuente. Después, caso por caso: ritual/nicho, cómo crecieron, ángulos textuales. Cerrar con los patrones que se repiten.
3. **Competidores locales**: el hallazgo central (¿el espacio del cliente está vacío u ocupado?), mapa de jugadores con ángulos y estrategias de comunicación, objeciones reales de la categoría con citas. Cierra con **"¿Por qué a ellos?"**: los diferenciales del cliente contra el mapa completo, con la lectura honesta de que los diferenciales de producto se copian y el defendible es la marca.
4. **Banco de ángulos**: tabla de ángulos documentados por marca (con ejemplos textuales) + territorios de buyer para explorar, sin limitarse a los que mencionó el cliente. Las hipótesis propias se marcan como tales ("sin validar").
5. **Lectura del terreno**: las dinámicas competitivas como observaciones y preguntas abiertas: premisa central, mapa de entrantes potenciales (quién podría entrar y qué tan corto es su camino), dinámica de precios de la categoría, los números que definen el negocio (LTV y recompra, con destaque visual), expansión natural de la categoría, gaps que nadie ataca. Modular: con Nivel 2 va completa; con Nivel 0 se sostiene solo con la evidencia de los casos y se acorta.
6. **El motor de contenido**: cómo se gana en la categoría según los casos documentados (adquisición por video y por qué, volumen como variable competitiva, el circuito de UGC/creadores/embajadores, online construye marca / el físico hace caja). Todo anclado en evidencia de los casos, no en opinión suelta.
7. **Dónde no ponerse a gastar**: los patrones que preceden a la quema de presupuesto, cada uno con su mecanismo de pérdida desarrollado (adquirir con estáticos, publicar antes de poder vender, entrar a canales de volumen sin volumen, el lanzamiento diluido).
8. **Fuentes analizadas** + nota de transparencia metodológica (límites de acceso declarados, cifras como rangos, ninguna cifra estimada por Muta).

**Cada sección cierra con un bloque "Qué significa esto para [cliente]"** (2-3 líneas, observacional, sin imperativos).

## Reglas de contenido

- **Informe, no plan.** Prohibido: "recomendamos", "deberían", "tienen que", "hagan", "paso 1", "este plan". Sin fechas, cronogramas ni checklists de ejecución.
- **Evidencia > opinión.** Toda cifra con fuente linkeada. Citas textuales antes que paráfrasis. Patrones antes que casos únicos.
- **No inventar.** Lo que no se encontró se declara ("Información insuficiente" / "Qué no encontré"). Un dato inventado en un regalo quema más que su ausencia.
- **Tono de analista neutro**, semiobjetivo, estilo 7 maletas. Ni la voz personal de Mati ni la de Muta. Sin frases prohibidas del CLAUDE.md. Sin guión largo (—). Tildes y ortografía revisadas.
- **Sanitización estricta**: cero clientes de Muta con nombre, cero precios o data interna de Muta, cero nombres propios de reuniones. Las reuniones alimentan ideas, nunca se citan.

## Reglas del entregable

- **Una sola versión, HTML directo.** Sin versión interna/externa. Si el cliente necesita PDF: conversión única al cierre, con contenido validado (`chrome --headless --print-to-pdf --no-pdf-header-footer`).
- **Estética Muta**: Montserrat, negro `#232323`, amarillo `#FAD70B`; naranja/azul solo en detalles chicos. Leer `00-recursos/principios-de-marca.md` antes de diseñar. Componentes de la plantilla validada: header negro con título amarillo, resumen con métricas grandes, section-number chips, info-boxes amarillas para "Qué significa esto", quote-boxes para citas, tablas con header negro, stat-callout negro para el destaque de métricas, chart-box para gráficos SVG.
- **Ubicación**: `Clientes/<Cliente>/` con subcarpeta `research/` para el crudo. Prospectos en `Prospectos/<nombre>/`. Crear la carpeta si no existe, siguiendo la convención del directorio.
- **Versionar**: `informe-<slug>-vN.html` (+ su `.md` de trabajo). Nunca sobrescribir versiones.
- **El mensaje de entrega lo escribe una persona** (quien entrega), nunca la skill.

## Verificación antes de cerrar

- Grep anti-plan sobre el entregable: "recomendamos", "deben", "hagan", "paso", "plan de".
- Las métricas del resumen ejecutivo (cantidad de fuentes, casos, jugadores mapeados, gaps) se CUENTAN sobre el documento final (ej. `grep -o 'https://[^)"]*' | sort -u | wc -l` para fuentes), nunca se estiman ni se redondean para arriba.
- Grep sanitización: nombres de clientes de Muta, montos internos, nombres propios de reuniones.
- Toda cifra tiene fuente; spot-check de que las webs citadas abren.
- Sin guión largo; ortografía y tildes.
- El HTML renderiza (verificar en browser: header, tablas, gráficos, destacados).

## Contrato de entrada/salida

Interfaz pensada para que el proceso pueda integrarse a un pipeline mayor (n8n) cuando esté maduro. Hoy se ejecuta manual.

**Inputs:** nombre del cliente/prospecto · qué vende o lanza (producto, categoría, país) · nivel de input (0/1/2 + link de reunión si existe) · contexto (branding en curso / prospecto / regalo suelto) · carpeta destino.

**Outputs:** `informe-<slug>-vN.html` (entregable) · `informe-<slug>-vN.md` (trabajo) · `research/*.md` (crudo con fuentes) · resumen final para quien lo pidió: rutas, qué se hizo, nivel de input usado, data no encontrada y flags para revisión humana antes de entregar.

**El informe nunca se envía al cliente directamente desde la skill**: siempre pasa por revisión humana.
