---
name: 7-maletas
description: "Investiga automáticamente a tus clientes y mercado con las 7 Maletas de Cualquier Compra de Felipe Vergara. Analiza tu negocio, competidores, reviews de Google/Amazon, redes sociales y anuncios para generar un reporte completo de investigación de mercado listo para crear campañas publicitarias. SIEMPRE usa este skill cuando el usuario mencione: investigación de mercado, 7 maletas, analizar mi negocio, entender clientes, por qué me compran, buyer persona, investigar competidores, qué dicen mis clientes, análisis de mercado, crear buyer personas, o cuando esté a punto de crear anuncios/campañas y necesite entender primero a su audiencia. También úsala cuando el usuario quiera saber qué problemas resuelve su producto, qué diferenciales tiene, o cómo mejorar su comunicación en web o anuncios."
metadata:
  version: 1.0.0
---

# Las 7 Maletas de Cualquier Compra - Investigación Automatizada

Eres un investigador de mercado experto especializado en la metodología de las 7 Maletas.
Tu trabajo es analizar automáticamente negocios, competidores y mercados usando herramientas de navegación
y búsqueda para descubrir los 7 elementos críticos que determinan si las personas compran o no.

---

## Flujo de trabajo

```
Datos del cliente → PASO 0: Verificar herramientas → FASE 1: Tu negocio → FASE 2: Competidores → FASE 3: Mercado → FASE 4: Reporte 7 Maletas
```

---

## INICIO: Recopilar datos del cliente

Pide al usuario estos datos en un solo mensaje:

1. **Nombre de tu empresa/negocio**
2. **URL de tu tienda o sitio web** (ej: https://tuempresa.com)
3. **Ficha de Google Business** (nombre completo como aparece en Google Maps)
   - Si no la tiene: pide ciudad + giro del negocio
4. **¿Es un cliente actual de tu agencia o una consulta nueva?**
   - Si es **cliente actual**: ¿Tenés activos alguno de estos conectores para él?
     - Metricool (métricas de redes)
     - Meta Ads (acceso a su cuenta publicitaria)
     - Conector de su web o tienda
   - Guardá esta información como `CLIENTE_TIPO` (existente / nuevo) y `CONECTORES_ACTIVOS` (lista de los disponibles)
5. **¿Tenés competidores en mente?**
   - **A) No, buscalos vos** → la skill busca competidores de forma autónoma
   - **B) Sí, los tengo** → el usuario provee URLs (web y/o Instagram de cada uno)
   - **C) Ambas** → la skill busca competidores de forma independiente primero, y además analiza los que el usuario indica
   - Guardá esta elección como `COMPETIDORES_MODO` (A / B / C) y las URLs como `COMPETIDORES_USUARIO`
6. **¿Querés que use un scraper (Apify) para Instagram y Google Maps?**
   - Instagram sin login y Google Maps sin login bloquean posts, comentarios y texto de reviews (muro de login / "vista limitada"). Un actor de Apify (Instagram Scraper, Google Maps Reviews Scraper, etc.) evita esos bloqueos sin necesitar credenciales.
   - **A) Sí, usá Apify donde haga falta** → en los Pasos 1.2, 1.3, 2.3 y 3.4, preferí el actor de Apify correspondiente (buscalo con `search-actors` o `mcp__Apify__search-actors`) por sobre Claude Chrome/agent-browser para IG y Maps
   - **B) No, con Claude Chrome/agent-browser alcanza** → seguí el flujo normal de la skill; si el muro de login bloquea contenido, anotalo como limitación y continuá
   - Guardá esta elección como `USA_APIFY` (sí / no)

**Ejemplo de presentación:**
```
Para empezar la investigación de las 7 Maletas, necesito algunos datos:

1. **Nombre de tu empresa/negocio**
2. **URL de tu sitio web** (ej: https://tuempresa.com)
3. **Ficha de Google Business** (o dime tu ciudad + giro si no tenés ficha)
4. **¿Es un cliente actual tuyo o una consulta nueva?**
   - Si es cliente actual: ¿tenés Metricool, Meta Ads o conector de su tienda activos para él?
5. **¿Tenés competidores en mente?**
   - A) No, buscalos vos
   - B) Sí, dame sus URLs (web o Instagram)
   - C) Ambas (buscá y también analizo los que te doy)
6. **¿Uso Apify para Instagram y Google Maps?** (evita el muro de login que bloquea posts, comentarios y reviews)
   - A) Sí
   - B) No, seguí con Claude Chrome

Ejemplo:
Empresa: Panadería El Buen Pan
URL: https://panaderiaelbuenpan.com
Ficha Google: Panadería El Buen Pan, Bogotá
Tipo: Cliente actual — Metricool activo, Meta Ads activo
Competidores: C — https://panaderiariviera.com, @panaderialabaguette
```

Una vez tengas los datos, continúa con el PASO 0.

---

## PASO 0: Verificar fuentes de datos disponibles (OBLIGATORIO)

### 0.1 Si es cliente existente: verificar conectores activos

Si `CLIENTE_TIPO = existente`, verificá qué conectores están realmente disponibles probando llamarlos:

- **Metricool**: intenta `mcp__90faed5c-23bb-41d4-bc7d-690dd29a5318__getBrandSettings` — si responde, tenés acceso a métricas de redes
- **Meta Ads**: intenta `mcp__2c393d2e-0c08-4e10-84e8-60d23c8a3e0a__ads_get_ad_accounts` — si responde, tenés acceso a su cuenta publicitaria
- **Conector web/tienda**: nota si el usuario lo mencionó; en ese caso ya tenés acceso directo a datos de venta

Guardá como `CONECTORES_CONFIRMADOS` solo los que realmente respondieron.

**Cómo usan los conectores durante la investigación:**
- **Metricool activo** → en el Paso 1.3 (redes sociales), en lugar de navegar con Chrome, usá `getAnalyticsDataByMetrics` para extraer métricas reales de engagement y alcance
- **Meta Ads activo** → en el Paso 2.4 (anuncios de competidores), también podés revisar si el cliente ya tiene campañas activas para entender qué mensajes usa; usá `ads_get_ad_entities` con el ad account del cliente
- **Conector tienda activo** → mencionalo en el reporte como fuente adicional para datos de conversión si el usuario los comparte

### 0.2 Verificar Claude Chrome

Intenta llamar a `mcp__Claude_in_Chrome__tabs_context_mcp` para obtener información del tab activo.

**Si Chrome responde con éxito:**

1. Guarda el tab ID para usarlo durante la investigación
2. Infórmale al usuario:

```
✅ Claude Chrome conectado — Iniciando investigación completa

📋 Datos recibidos. Comenzando investigación automática de las 7 Maletas...

📊 Con Claude Chrome obtendré:
   • TODAS las reviews de Google Business (scroll automático)
   • Navegación completa de Facebook Ads Library
   • Análisis de Instagram con contenido dinámico
   • Resultados 10× más completos que búsquedas estáticas
[Si CONECTORES_CONFIRMADOS no está vacío]: ✨ Además, usaré los conectores activos para datos más precisos del cliente.

⏱️  Tiempo estimado: 25-30 minutos. Te mantendré informado del progreso.
```

3. Continúa con FASE 1

---

**Si Claude Chrome NO está disponible:**

Detén la ejecución e informa al usuario:

```
❌ Claude Chrome no detectado

Este skill requiere Claude Chrome para funcionar correctamente.

Para instalarlo:
1. Instala la extensión Claude in Chrome desde Chrome Web Store
2. Reinicia Claude Code
3. Vuelve a ejecutar /7-maletas

Sin Claude Chrome, los resultados serían incompletos (solo 5-10 reviews en lugar de todas, sin acceso a Instagram, sin navegación interactiva de Facebook Ads).
```

**NO continúes con la investigación sin Claude Chrome.**

---

## FASE 1: Análisis de Tu Negocio (~5 min)

Infórmale al usuario:
```
🔍 FASE 1/4: Analizando tu negocio...
```

### Paso 1.1: Analizar la página web del cliente

Ejecuta WebFetch con la URL proporcionada.

**Del contenido extraído, documenta:**
- **Productos/servicios**: Lista completa con nombres y descripciones breves
- **Beneficios mencionados**: Citas textuales de beneficios que destacan (ej: "envío gratis", "atención 24/7")
- **Problemas que dice resolver**: Frases donde mencionan problemas (ej: "¿Cansado de X?", "Dile adiós a Y")
- **Diferenciales destacados**: Qué los hace únicos vs competencia (ej: "100% natural", "hecho a mano", "20 años de experiencia")
- **Garantías ofrecidas**: Si hay devoluciones, garantía de satisfacción, etc. (cita textual)
- **Testimonios**: Si hay reviews/testimonios en la web, guarda 2-3 completos con nombre del cliente si está disponible
- **Secciones importantes**: Identifica si hay páginas como /productos, /about, /nosotros — si existen y parecen relevantes, fetchéalas también

**Si la web tiene múltiples páginas relevantes:**
- Ejecuta WebFetch adicional en páginas de productos, servicios, about, testimonios
- Prioriza las que tengan información sobre beneficios y diferenciales

**Qué guardar para el reporte:**
Crea un documento mental/temporal con:
```
NEGOCIO DEL CLIENTE:
- Nombre: [nombre]
- Industria/giro: [inferido de la web]
- Productos/servicios principales: [lista]
- Beneficios que comunican: [bullets con citas textuales]
- Problemas que mencionan: [bullets con citas textuales]
- Diferenciales: [bullets]
- Garantías: [citas textuales o "No mencionan garantías"]
- Testimonios en web: [cantidad + 2-3 ejemplos textuales]
```

### Paso 1.2: Buscar y analizar su ficha de Google Business

#### Encontrar la ficha de Google Business:

**Buscar y analizar con Claude Chrome:**

1. Usa Claude Chrome para navegar a Google Maps
2. En la barra de búsqueda de Google Maps:
   - **Si el usuario proporcionó nombre de ficha:** Busca `"{nombre exacto de la ficha}" {ciudad}`
   - **Si NO proporcionó ficha:** Busca `"{nombre empresa}" {ciudad}`

3. **Detectar si hay múltiples sucursales:**
   - Si los resultados muestran varias fichas con el mismo nombre de empresa (ej: "Panadería El Buen Pan - Palermo", "Panadería El Buen Pan - Belgrano"), NO elijas una sola.
   - Reconocé que el negocio tiene **múltiples sucursales** y anotá la lista completa.
   - Informale al usuario: "Encontré X sucursales. Voy a analizar todas y combinar los hallazgos."
   - Guardá como `SUCURSALES = [lista de nombres/URLs de cada ficha]`

4. **Para cada ficha** (o la única si solo hay una):
   - Haz click en la ficha
   - Haz click en la pestaña "Reseñas" o "Reviews"
   - **PASO CRÍTICO - Expandir y extraer reviews completas:**

     a. **Scrollea progresivamente** (5-7 scrolls) para cargar más reviews en el DOM

     b. **Expande reviews truncadas:**
        - Busca botones "Ver más" / "More" / "Más información" en las reviews
        - Haz click en al menos 10-15 de estos botones para expandir el texto completo
        - Las reviews truncadas solo muestran las primeras 2 líneas — necesitas expandirlas

     c. **Extrae el contenido completo:**
        - Después de expandir, usa `get_page_content` para obtener el HTML
        - Parsea las reviews completas (nombre del reviewer, fecha, calificación, texto completo)
        - NO te conformes con solo nombres — necesitas el texto completo de cada review

     d. **Objetivo por sucursal:** 30-50 reviews con contenido textual completo
        - Si solo ves nombres sin texto, es que NO expandiste las reviews
        - Ejemplo de review completa: "María González — 5★ — Hace 2 meses — 'Excelente servicio, los postres son deliciosos y la atención es muy buena. Lo recomiendo 100%.'"

5. Si NO encuentras la ficha: anota "Sin ficha de Google Business encontrada" y continúa al Paso 1.3

**Del contenido de Google Business, extrae:**
- **Calificación promedio**: Ej: 4.5 estrellas
- **Cantidad total de reviews**: Número exacto
- **Reviews textuales COMPLETAS**: 30-50 reviews con texto completo (NO solo nombres)
  - Cada review debe incluir: nombre, calificación (estrellas), fecha, y texto completo
  - Identifica qué elogian (frases textuales exactas de las reviews)
  - Identifica qué critican/quejas (frases textuales exactas de las reviews)
  - Busca patrones: si 5+ personas mencionan lo mismo, es un patrón importante

**⚠️ VALIDACIÓN:** Si tu output solo tiene nombres de reviewers (ej: "Carola Toledo, Adan Tamlk") sin texto, significa que NO expandiste las reviews correctamente. Regresa y haz click en "Ver más" para obtener el contenido completo.

**Qué guardar:**

Si hay una sola sucursal:
```
GOOGLE BUSINESS DEL CLIENTE:
- Calificación: [X.X estrellas]
- Total reviews: [número]
- Reviews analizadas: [número — indica si fueron todas o solo las primeras]
- Elogios más mencionados:
  * "[Frase textual]" (mencionado X veces)
  * "[Frase textual]" (mencionado X veces)
- Quejas/problemas mencionados:
  * "[Frase textual]" (mencionado X veces)
  * "[Frase textual]" (mencionado X veces)
```

Si hay múltiples sucursales, guardá por separado y luego combiná:
```
GOOGLE BUSINESS - SUCURSAL [Nombre/Zona]:
- Calificación: [X.X]  |  Reviews analizadas: [N]
- Elogios: [patrones]
- Quejas: [patrones]

[Repetir por cada sucursal]

GOOGLE BUSINESS - CONSOLIDADO:
- Calificación promedio ponderada: [X.X estrellas]
- Total reviews analizadas: [suma]
- Elogios más mencionados en todas las sucursales:
  * "[Frase]" (mencionado X veces en Y sucursales)
- Quejas más mencionadas en todas las sucursales:
  * "[Frase]" (mencionado X veces en Y sucursales)
- Diferencias entre sucursales (si las hay):
  * "Sucursal [A] tiene más quejas sobre [X] que [B]"
```

### Paso 1.2b: Buscar y analizar reviews en marketplaces (cliente + competidores)

**⚠️ SOLO PARA ECOMMERCE / PRODUCTOS FÍSICOS**

**Primero, detecta el tipo de negocio:**
- ✅ **ES ECOMMERCE/PRODUCTO:** Si vende productos físicos (postres, ropa, suplementos, electrónicos, etc.) → EJECUTA este paso
- ❌ **ES SERVICIO:** Si es servicio presencial o remoto (consultoría, clínica, abogado, plomero, etc.) → SALTA este paso, continúa al 1.3
- ❌ **ES CURSO/INFOPRODUCTO:** Si vende cursos online, ebooks, membresías → SALTA este paso, continúa al 1.3

**Si detectaste que ES SERVICIO O CURSO:** Anota "Marketplace N/A - Negocio de servicios/infoproductos" y salta directamente al Paso 1.3.

**Si ES ECOMMERCE/PRODUCTO FÍSICO, continúa:**

#### Detectar el marketplace principal según el mercado

**Primero, determiná el país/mercado del negocio** (inferido de la URL, la ficha de Google o la ciudad):

- 🇦🇷 **Argentina / LatAm (Argentina, México, Colombia, Chile, Perú, Uruguay, etc.):**
  → Marketplace principal: **Mercado Libre** (mercadolibre.com.ar, .com.mx, .com.co, etc.)
  → Amazon: úsalo solo si el negocio tiene presencia explícita en Amazon (lo mencionan en su web o redes)

- 🌎 **España o mercado global:**
  → Marketplace principal: **Amazon** (amazon.es, amazon.com)
  → Mercado Libre: no aplica

- 🔍 **País ambiguo:** ejecutá una búsqueda rápida en ambos y quedate con el que tenga más presencia del producto

#### Analizar en Mercado Libre (mercado principal para LatAm)

1. Usa Claude Chrome para navegar al marketplace correcto (ej: mercadolibre.com.ar)
2. Busca el nombre del negocio o el producto principal
3. **Para el CLIENTE** — si tiene productos listados:
   - Navegá a sus publicaciones principales
   - Extraé: precio, cantidad de ventas, calificación, preguntas frecuentes y reviews (opiniones)
   - Filtrá por "Peor calificadas" para ver objeciones reales

4. **Para COMPETIDORES** — buscá el mismo producto/categoría:
   - Identificá 3-5 competidores mejor posicionados en el marketplace
   - Para cada uno: precio, ventas totales, calificación, reviews más destacadas
   - Esto te da el **mapa de precios del mercado** (importante para Maleta 4 — Diferenciales)

5. Anotá el **rango de precios** del mercado:
   - Precio mínimo, precio máximo, precio promedio del segmento
   - Dónde está posicionado el cliente (bajo, medio, alto)

#### Analizar en Amazon (si aplica)

Si el negocio tiene presencia en Amazon o el mercado lo requiere:

1. Navegá a amazon.com (o .com.mx, .es, etc. según el país)
2. Buscá el producto del cliente y de competidores directos
3. Extraé reviews positivas y negativas con el mismo criterio que Mercado Libre

#### Qué extraer de las reviews (aplica a ambos marketplaces):

**REVIEWS POSITIVAS (4-5 estrellas):**
- ¿Qué beneficios específicos mencionan?
- ¿Qué dolor/problema resolvió el producto?
- ¿Qué palabras usan para describir el resultado? (citas textuales)
- ¿Comparan con otras marcas? ¿Por qué eligieron esta?

**REVIEWS NEGATIVAS (1-3 estrellas):**
- ¿Qué objeciones mencionan? (precio, sabor, tamaño, entrega, calidad)
- ¿Qué expectativas NO cumplió el producto?
- ¿Qué críticas constructivas dan?
- ¿Mencionan alternativas mejores?

**PREGUNTAS FRECUENTES (sección Q&A o "Preguntas al vendedor"):**
- Identificá dudas recurrentes (ingredientes, alérgenos, envío, garantía, etc.)
- Estas son objeciones en forma de pregunta

**Qué guardar:**
```
MARKETPLACE REVIEWS:
- Marketplace principal usado: [Mercado Libre / Amazon / Ambos]
- URL del marketplace: [URL]

MAPA DE PRECIOS (ecommerce):
- Precio mínimo del segmento: $[X]
- Precio máximo del segmento: $[X]
- Precio promedio: $[X]
- Precio del cliente: $[X] → Posicionamiento: [Bajo / Medio / Alto]

ANÁLISIS DE PRODUCTOS:
- Cliente en marketplace: [✅ SÍ / ❌ NO]
- Productos analizados:
  * [Cliente o Competidor 1] — $[precio] — [X.X estrellas] — [Y] ventas/reviews
  * [Competidor 2] — $[precio] — [X.X estrellas] — [Y] ventas/reviews
  * [Competidor 3] — $[precio] — [X.X estrellas] — [Y] ventas/reviews

- Elogios más mencionados:
  * "[Frase textual]" — [Producto]
  * "[Frase textual]" — [Producto]

- Quejas/objeciones detectadas:
  * "[Frase textual]" — [Producto]
  * "[Frase textual]" — [Producto]

- Preguntas frecuentes:
  * "[Pregunta recurrente 1]"
  * "[Pregunta recurrente 2]"
```

**Si NO encontrás el cliente NI competidores en el marketplace:**
Anotá "Sin presencia en marketplace detectada para este nicho" y continuá al Paso 1.3.

**💡 VALOR DE LOS MARKETPLACES:** Las reviews son especialmente útiles porque los compradores son brutalmente honestos (dinero real gastado). El mapa de precios además es insumo directo para la Maleta 4 (Diferenciales) y la Maleta 6 (Objeciones de precio).

---

### Paso 1.3: Buscar y analizar redes sociales del cliente

**Usar Claude Chrome para buscar y navegar:**

1. Primero revisa la página web del cliente (del paso 1.1) - busca íconos/enlaces de redes en footer o header
2. Si encuentras enlaces directos a Instagram/Facebook, guárdalos
3. Si NO encuentras enlaces:
   - **Para Instagram:** Abre instagram.com en Claude Chrome y busca `"{nombre empresa}"` en la barra de búsqueda
   - **Para Facebook:** Abre facebook.com y busca `"{nombre empresa}"`

**Para Instagram:**
1. Navega a la cuenta con Claude Chrome
2. Haz scroll en el perfil para ver los últimos 5-10 posts
3. Haz click en 2-3 posts recientes (últimas 2 semanas)
4. Lee los comentarios de cada post (scrollea para ver más comentarios)
5. Identifica patrones:
   - Comentarios positivos (qué elogian)
   - Preguntas frecuentes (qué dudas tienen)
   - Quejas o sugerencias

**Para Facebook:**
1. Navega a la página con Claude Chrome
2. Haz scroll para ver posts recientes
3. Haz click en 2-3 posts con más interacción
4. Lee los comentarios
5. Identifica patrones similares a Instagram

**Qué guardar:**
```
REDES SOCIALES DEL CLIENTE:
- Instagram: [URL o "No encontrado"]
  * Comentarios positivos: [patrones detectados]
  * Preguntas frecuentes: [patrones]
  * Quejas/sugerencias: [patrones]
- Facebook: [URL o "No encontrado"]
  * Comentarios positivos: [patrones]
  * Preguntas frecuentes: [patrones]
```

**Si no encuentras redes o no hay contenido útil:**
Anótalo y continúa. No es crítico para el análisis — puedes compensar con más énfasis en Google Business reviews y análisis de competidores.

---

Infórmale al usuario:
```
✅ FASE 1 completada: Tu negocio analizado
📊 Datos recopilados: [X] reviews de Google, sitio web completo, redes sociales
```

---

## FASE 2: Análisis de Competidores (~10 min)

Infórmale al usuario:
```
🔍 FASE 2/4: Investigando competidores...
```

### Paso 2.1: Identificar y organizar competidores

El flujo depende de `COMPETIDORES_MODO` que definiste en el INICIO:

---

**MODO A — "Buscalos vos":**

Ejecuta estas búsquedas en paralelo (múltiples WebSearch):

1. `"{industria/giro del cliente}" "{ciudad}" -"{nombre del cliente}"`
   - Ejemplo: "panadería artesanal Bogotá -El Buen Pan"

2. `"mejores {producto/servicio}" "{ciudad}"`
   - Ejemplo: "mejores panaderías Bogotá"

3. `"{producto/servicio} en {ciudad} recomendados"`
   - Ejemplo: "panaderías en Bogotá recomendadas"

De los resultados, identificá 3-5 competidores con sitio web activo, Google Business Profile y que sean competencia directa. Priorizá los que aparezcan en múltiples búsquedas y tengan reviews activas.

---

**MODO B — "Yo los tengo":**

Usá directamente las URLs que el usuario proporcionó en `COMPETIDORES_USUARIO`. No hagas búsqueda autónoma.

---

**MODO C — "Ambas":**

Primero ejecutá el flujo del Modo A (búsqueda autónoma) excluyendo explícitamente los competidores que el usuario ya mencionó de los queries (agregá `-"{nombre}"` para cada uno). Completá ese análisis antes de pasar a los del usuario.

Luego, una vez terminado el análisis autónomo, analizá los competidores de `COMPETIDORES_USUARIO` por separado. Al final, uní todo en una lista consolidada sin duplicados.

---

**Guarda la lista final:**
```
COMPETIDORES IDENTIFICADOS:
Origen: [Búsqueda autónoma / Proporcionados por usuario / Ambos]

1. [Nombre] - [URL web] - [URL Google Maps si encontraste] - [Origen: autónomo/usuario]
2. [Nombre] - [URL web] - [URL Google Maps] - [Origen]
3. [Nombre] - [URL web] - [URL Google Maps] - [Origen]
4. [Nombre] - [URL web] - [URL Google Maps] - [Origen]
5. [Nombre] - [URL web] - [URL Google Maps] - [Origen]
```

### Paso 2.2: Analizar páginas web de competidores

**Para CADA competidor (top 3-5):**

1. Ejecuta WebFetch en su página web principal

**Extrae:**
- **Problemas que comunican**: ¿Qué pain points mencionan?
- **Diferenciales que usan**: ¿Qué dicen que los hace únicos?
- **Cómo describen productos**: Frases, vocabulario, beneficios
- **Garantías que ofrecen**: Devoluciones, garantías de satisfacción, políticas
- **Estructura de la web**: ¿Qué secciones tienen? (productos, servicios, testimonios, etc.)
- **Precios visibles**: Si muestran precios en la web, anotálos

**Si el negocio es ECOMMERCE**, también extrae:
- Rango de precios de sus productos principales
- Si tienen envío gratis, cuotas sin interés u otras condiciones de compra
- Posicionamiento de precio (premium, económico, masivo)

**Guarda por cada competidor:**
```
COMPETIDOR [N] - [Nombre]:
- Problemas que comunican: [bullets con citas]
- Diferenciales: [bullets con citas]
- Garantías: [citas textuales]
- Lenguaje/vocabulario destacado: [palabras clave que repiten]
- Precios (si ecommerce): [rango] — Posicionamiento: [Bajo/Medio/Alto]
```

### Paso 2.3: Analizar reviews de competidores

**Para CADA competidor con Google Business:**

1. Usa Claude Chrome para navegar a la URL de Google Maps del competidor
2. Haz click en la pestaña "Reseñas"
3. Scrollea para cargar 15-20 reviews
4. Lee y analiza las reviews mientras scrolleas

**Extrae:**
- **Qué valoran los clientes**: Patrones en reviews positivas
- **Quejas comunes**: Patrones en reviews negativas
- **Comparaciones**: ¿Mencionan a otros competidores? ¿Por qué eligieron este?

**Busca específicamente:**
- Frases como "mejor que X porque...", "a diferencia de Y..."
- Problemas mencionados: "El único problema es...", "Ojalá tuvieran..."
- Elogios específicos: "Me encantó que...", "Lo mejor es..."

**Guarda:**
```
REVIEWS COMPETIDOR [N]:
- Total reviews: [número]
- Calificación: [X.X]
- Valoran principalmente:
  * "[Cita textual]" (frecuencia: [X] veces)
  * "[Cita textual]" (frecuencia: [X] veces)
- Quejas principales:
  * "[Cita textual]" (frecuencia: [X] veces)
  * "[Cita textual]" (frecuencia: [X] veces)
```

### Paso 2.4: Analizar anuncios de competidores (Biblioteca de Facebook)

**Para los competidores con más presencia digital (top 2-3):**

**Usar Claude Chrome para navegar Facebook Ads Library:**

1. Navega a `https://www.facebook.com/ads/library/` con Claude Chrome
2. En la Biblioteca de Anuncios:
   - Selecciona el país correcto (ej: Colombia, México, etc.)
   - En el buscador, escribe el nombre del competidor
   - Filtra por "Todos los anuncios" o "Anuncios activos"
3. Explora los anuncios encontrados:
   - Haz click en 3-5 anuncios para ver detalles completos
   - Lee los textos completos de los anuncios
   - Ve las imágenes/videos si están disponibles
   - Identifica patrones en los mensajes
4. Si NO encuentras anuncios: anota "Sin anuncios activos en Facebook" y continúa con siguiente competidor

**Si encuentras anuncios activos, extrae:**
- **Textos de los anuncios**: Headlines y descripciones completas
- **Problemas que abordan**: Qué pain points mencionan
- **Ofertas/promociones**: Descuentos, bonos, promociones especiales
- **Llamados a la acción (CTA)**: "Compra ahora", "Agenda cita", "Más información", etc.
- **Diferenciales destacados**: Qué usan para diferenciarse

**Guarda:**
```
ANUNCIOS COMPETIDORES:
Competidor [Nombre]:
- Anuncios activos encontrados: [número o "Sin anuncios activos"]
- Problema que abordan en ads: [citas textuales]
- Oferta principal: [descripción]
- Diferenciales destacados: [bullets]
- CTA más usado: [ej: "Pide ahora", "Agenda cita", etc.]
```

**Si NO encuentras anuncios:**
Anótalo y continúa. Significa que ese competidor no está usando publicidad pagada activamente (o no es detectable sin Claude Chrome).

---

Infórmale al usuario:
```
✅ FASE 2 completada: Competidores analizados
📊 Datos recopilados: [X] competidores, [Y] reviews analizadas, anuncios revisados
```

---

## FASE 3: Investigación de Mercado General (~5 min)

Infórmale al usuario:
```
🔍 FASE 3/4: Investigando el mercado general...
```

### Paso 3.1: Búsquedas de Google con trucos

**Truco del ESPACIO (sufijos - lo que va DESPUÉS):**

1. Ejecuta WebSearch: `"{producto/servicio} comprar"`
   - Ejemplo: "bicicleta comprar"
   - Google automáticamente sugiere búsquedas relacionadas

2. Analiza las sugerencias que aparecen:
   - Busca patrones sobre PARA QUÉ lo compran (ej: "para empezar", "para ciudad", "para montaña")
   - Busca segmentos (ej: "para niños", "para adultos mayores")
   - Busca especificaciones (ej: "según estatura", "económica", "de calidad")

**Truco del ASTERISCO (prefijos - lo que va ANTES):**

1. Ejecuta WebSearch: `"* {producto/servicio} comprar"`
   - Ejemplo: "* bicicleta comprar"

2. Analiza las sugerencias:
   - Descubre productos/servicios complementarios
   - Identifica necesidades relacionadas
   - Encuentra variantes del producto

**Qué guardar:**
```
BÚSQUEDAS DE GOOGLE:
Sufijos (ESPACIO):
- "{producto} comprar para [X]" → Segmento: [X]
- "{producto} comprar según [Y]" → Objeción/necesidad: [Y]
- Patrón detectado: [insight]

Prefijos (ASTERISCO):
- "[Z] {producto} comprar" → Producto complementario: [Z]
- Patrón detectado: [insight]
```

### Paso 3.2: Analizar contenido de artículos/guías de compra

Si en las búsquedas anteriores encuentras artículos tipo:
- "Cómo elegir..."
- "Guía de compra de..."
- "Mejores [producto] 2024"

**Ejecuta WebFetch en 1-2 de estos artículos y extrae:**
- Problemas que mencionan
- Criterios de selección que destacan
- Características que comparan
- Objeciones que abordan

**Guarda:**
```
ARTÍCULOS/GUÍAS:
- Problemas mencionados: [bullets]
- Criterios de compra: [bullets]
- Características importantes: [bullets]
```

### Paso 3.3: Buscar influencers del nicho (SI APLICA)

**Solo si el nicho tiene influencers activos** (fitness, belleza, comida, viajes, moda, tech):

1. Ejecuta WebSearch: `"influencer {nicho}" Instagram`
   - Ejemplo: "influencer fitness Bogotá Instagram"

2. Si encuentras cuentas relevantes:
   - Identifica 1-2 influencers con buen engagement
   - Busca posts recientes sobre productos similares

3. Si es posible acceder al contenido:
   - Lee qué dicen sobre el producto/servicio
   - Lee comentarios de seguidores
   - Identifica qué valoran, qué preguntan

**Guarda:**
```
INFLUENCERS (si aplica):
- Influencer: [nombre/cuenta]
- Qué destaca del producto: [citas]
- Comentarios de seguidores: [patrones]
```

**Si el nicho NO tiene influencers relevantes o no encuentras contenido útil:**
Anótalo y salta este paso.

### Paso 3.4: Analizar marketplaces (SI APLICA)

**Solo si el producto se vende en marketplaces** (Amazon, MercadoLibre, etc.):

1. Ejecuta WebSearch: `site:mercadolibre.com.co "{producto}"`
   - O `site:amazon.com "{producto}"`

2. Si encuentras productos similares con reviews:
   - Ejecuta WebFetch en 1-2 productos con más reviews
   - Lee reviews positivas y negativas

**Extrae de las reviews:**
- Problemas/quejas comunes
- Soluciones/beneficios valorados
- Qué hace que compren o devuelvan el producto

**Guarda:**
```
MARKETPLACES (si aplica):
- Plataforma: [MercadoLibre/Amazon]
- Reviews analizadas: [número]
- Problemas mencionados: [bullets con citas]
- Beneficios valorados: [bullets con citas]
```

**Si el producto NO se vende en marketplaces o no hay reviews útiles:**
Salta este paso.

---

Infórmale al usuario:
```
✅ FASE 3 completada: Mercado general investigado
📊 Datos recopilados: búsquedas analizadas, [contenido adicional según nicho]
```

---

## FASE 4: Llenar la Plantilla de las 7 Maletas (~5 min)

Infórmale al usuario:
```
🔍 FASE 4/4: Llenando la plantilla de las 7 Maletas...
```

Con TODA la información recopilada en las 3 fases anteriores, llena la plantilla oficial respondiendo TODAS las preguntas con evidencia de la investigación.

**IMPORTANTE:** Al finalizar la investigación, genera un archivo HTML con diseño minimalista profesional.

**Nombre del archivo**: `7-maletas-[slug-empresa]-[YYYY-MM-DD].html`
(Ejemplo: `7-maletas-MutaDigital-2026-03-26.html`)

**Características del diseño:**
- Paleta sobria: Negro (#1d1d1f), Gris claro (#f5f5f7), Azul acento (#0071e3)
- Tipografía: Inter, SF Pro, -apple-system
- Mucho espacio en blanco (padding generoso)
- Sin gradientes ni animaciones llamativas
- Tablas limpias con bordes sutiles (1px, #d2d2d7)
- Estilo Apple/Minimalista

**Ubicación**: Guardar en el directorio actual del usuario

---

**Estructura del contenido a incluir en el HTML:**

```markdown
# LAS 7 MALETAS DE CUALQUIER COMPRA
## HOJA DE INVESTIGACIÓN: [Nombre de la Empresa]

*Fecha: [Fecha de hoy en formato: DD de Mes de YYYY]*
*Investigación automatizada por Claude Code*

---

## 📊 RESUMEN DE INVESTIGACIÓN

**Fuentes analizadas:**
- ✅ Sitio web: [URL del cliente]
- ✅ Google Business: [X] reviews analizadas
- ✅ Amazon: [X] reviews analizadas (cliente + competidores) / ❌ N/A (servicio/infoproducto) / ❌ Sin presencia detectada
- ✅ Competidores: [X] negocios investigados
- ✅ Reviews de competidores: [Y] reviews analizadas
- ✅ Redes sociales: [Instagram/Facebook del cliente]
- ✅ Anuncios: [X] anuncios de competidores revisados
- ✅ Búsquedas de Google: [Z] queries procesadas

---

## 1️⃣ PÚBLICO

### a. ¿Cuál es el rango de edades de tus clientes?

**Respuesta basada en la investigación:**
[Rango de edad detectado en reviews y redes sociales — ej: "25-45 años principalmente"]

**Evidencia:**
- "[Cita textual de review que menciona edad/generación]" — Google Review
- Comentarios en Instagram mayormente de [grupo etario]
- Comparación con competidores: [insight]

---

### b. ¿Cuál es su género dominante?

**Respuesta basada en la investigación:**
[Género dominante — ej: "60% mujeres, 40% hombres según nombres en reviews"]

**Evidencia:**
- De [X] reviews analizadas con nombres identificables: [Y]% mujeres, [Z]% hombres
- Comentarios en redes sociales: [patrón observado]
- Competidores atienden a: [comparación]

---

### c. ¿Dónde viven?

**Respuesta basada en la investigación:**
[Ubicación geográfica — ej: "Principalmente zona norte de Bogotá" o "Todo Colombia, concentración en Bogotá, Medellín, Cali"]

**Evidencia:**
- Reviews de Google mencionan: [barrios/zonas específicas]
- [X]% de reviews son de [ciudad/zona principal]
- Competidores operan en: [áreas]

---

### d. ¿Situación sentimental?

**Respuesta basada en la investigación:**
[Si se detectó — ej: "Principalmente familias con hijos" o "Solteros/as jóvenes profesionales" o "No determinable con la información disponible"]

**Evidencia (si aplica):**
- Reviews mencionan: "[citas donde hablan de familia/pareja/hijo]"
- Patrones en comentarios: [observaciones]
- Si NO hay evidencia: "No se pudo determinar con certeza — se recomienda preguntar directamente en entrevistas"

---

### e. ¿Poder adquisitivo?

**Respuesta basada en la investigación:**
[Nivel socioeconómico inferido — ej: "Medio-alto, dispuestos a pagar premium por calidad"]

**Evidencia:**
- Precio del producto/servicio: [rango de precios]
- Comparación con competidores: [si es más caro/barato/igual]
- Reviews mencionan precio como: [factor positivo/negativo/neutral]
- Búsquedas incluyen términos: [ej: "económico", "premium", "barato"]

---

## 2️⃣ PROBLEMA

### a. Haz una lista de todos los DOLORES/PAIN POINTS del cliente que este producto resuelve

**IMPORTANTE:** Estamos buscando los PROBLEMAS QUE TU CLIENTE TIENE (dolores, frustraciones, necesidades insatisfechas) que este producto/servicio resuelve. NO confundir con objeciones de compra (eso va en Maleta 6).

**Ejemplos de dolores correctos:**
- ❌ INCORRECTO: "No confían en comprar online" (esto es objeción)
- ✅ CORRECTO: "Pierden dinero en anuncios que no convierten" (esto es dolor del cliente)

**Dónde buscar estos dolores:**
- Reviews positivas donde dicen "antes tenía X problema, ahora lo resolví con este producto"
- Búsquedas de Google relacionadas al problema (ej: "cómo reducir costo por lead")
- Comentarios en redes donde piden soluciones
- Reviews de competidores mencionando qué problema les resolvió

---

**Lista completa de problemas detectados:**

1. **[Problema 1]**
   - Mencionado [X] veces en reviews/comentarios
   - Evidencia: "[Cita textual]" — [Fuente]

2. **[Problema 2]**
   - Mencionado [X] veces
   - Evidencia: "[Cita textual]" — [Fuente]

3. **[Problema 3]**
   - Mencionado [X] veces
   - Evidencia: "[Cita textual]" — [Fuente]

4. **[Problema 4]**
   - Mencionado [X] veces
   - Evidencia: "[Cita textual]" — [Fuente]

5. **[Problema 5]**
   - Mencionado [X] veces
   - Evidencia: "[Cita textual]" — [Fuente]

[Continúa si hay más problemas detectados...]

---

### b. Reduce esa lista a tres

**Los 3 problemas principales (ordenados por frecuencia):**

**1. [Problema #1 más mencionado]**
- Frecuencia: [X] menciones
- Fuentes: Reviews del cliente, competidores, redes sociales
- Por qué es importante: [explicación breve]

**2. [Problema #2]**
- Frecuencia: [X] menciones
- Fuentes: [dónde se detectó]
- Por qué es importante: [explicación breve]

**3. [Problema #3]**
- Frecuencia: [X] menciones
- Fuentes: [dónde se detectó]
- Por qué es importante: [explicación breve]

---

### c. Selecciona el problema más importante

**El problema MÁS IMPORTANTE es:**

**"[Frase exacta del problema principal]"**

**Por qué este es el problema #1:**
- Mencionado [X] veces (más que cualquier otro)
- Aparece en [Y] fuentes diferentes (reviews propias, competidores, redes, búsquedas)
- [Razón adicional — ej: "Afecta directamente la decisión de compra según reviews"]

**Evidencia contundente:**
- "[Cita textual 1]" — Cliente en Google Reviews
- "[Cita textual 2]" — Comentario en Instagram
- "[Cita textual 3]" — Review de competidor
- Búsqueda popular de Google: "[query relacionada]"

---

## 3️⃣ SOLUCIÓN

### a. ¿Cómo tu producto o servicio le resuelve este problema a tus clientes?

**Tu solución actual (según tu web):**

"[Cita textual de cómo describes tu producto/servicio en la web]"

**Análisis de cómo resuelve el problema #1:**

[Explicación de cómo el producto/servicio conecta con el problema principal]

**Lo que dicen tus clientes sobre la solución:**
- "[Cita de review positivo que confirma que resuelve el problema]" — Google Review
- "[Otra cita]" — [Fuente]

**Cómo competidores comunican su solución:**
- **Competidor 1:** "[Su mensaje]"
- **Competidor 2:** "[Su mensaje]"
- **Competidor 3:** "[Su mensaje]"

**💡 Oportunidad de mejora:**
[Si hay una forma MEJOR de comunicar la solución basado en lo que funciona para competidores o lo que piden los clientes, menciónalo aquí. Si tu solución ya es clara, escribe: "Tu solución está bien comunicada y conecta con el problema principal"]

---

## 4️⃣ DIFERENCIALES

### a. Haz una lista de las cosas que haces mejor que tus competidores

**Diferenciales encontrados:**

1. **[Diferencial 1]**
   - Dónde lo mencionas: [En tu web/reviews/redes]
   - Competidores que también lo tienen: [Competidor X, Y] o "Ninguno"
   - Evidencia: "[Cita de tu web o review de cliente]"

2. **[Diferencial 2]**
   - Dónde lo mencionas: [fuente]
   - Competidores que también lo tienen: [lista o "Ninguno"]
   - Evidencia: "[Cita]"

3. **[Diferencial 3]**
   - Dónde lo mencionas: [fuente]
   - Competidores que también lo tienen: [lista o "Ninguno"]
   - Evidencia: "[Cita]"

4. **[Diferencial 4]**
   - Dónde lo mencionas: [fuente]
   - Competidores que también lo tienen: [lista o "Ninguno"]
   - Evidencia: "[Cita]"

5. **[Diferencial 5]**
   - Dónde lo mencionas: [fuente]
   - Competidores que también lo tienen: [lista o "Ninguno"]
   - Evidencia: "[Cita]"

**Comparación con competencia:**

| Diferencial | Tú | Comp 1 | Comp 2 | Comp 3 | Saturación |
|------------|-----|--------|--------|--------|------------|
| [Dif 1] | ✅ | ✅ | ✅ | ❌ | Alta |
| [Dif 2] | ✅ | ❌ | ❌ | ❌ | 🔥 Único |
| [Dif 3] | ✅ | ✅ | ❌ | ❌ | Media |

---

### b. ¿Cuál es el diferencial que MENOS se está mencionando en tu mercado?

**El diferencial MENOS saturado (tu oportunidad):**

**"[Nombre del diferencial único o menos usado]"**

**Análisis:**
- Tú LO TIENES: [Sí/No — si no lo tienes pero podrías tenerlo, menciónalo]
- De [X] competidores analizados: [Y] lo mencionan ([Z]%)
- Por qué es valioso: [Razón basada en reviews — qué problema resuelve]

**💡 Recomendación:**

[Si LO TIENES: "DESTACA este diferencial en todos tus anuncios y web — es tu ventaja competitiva más fuerte porque casi nadie más lo tiene"]

[Si NO LO TIENES: "Considera implementar [diferencial] porque los clientes lo valoran (evidencia: [cita]) y casi ningún competidor lo ofrece"]

---

## 5️⃣ TESTIMONIOS

### a. ¿Qué clientes pasados / casos de éxito puedes mostrar para generar confianza?

**Testimonios actuales disponibles:**

**Calificación general:**
- Google Business: [X.X] ⭐ ([Y] reviews totales)
- Otras plataformas: [si aplica]

**Los 5 mejores testimonios (textuales):**

**1.**
> "[Testimonio completo — debe mencionar resultado/beneficio específico]"
> — [Nombre del cliente si está disponible], [Fuente y fecha si está]

**2.**
> "[Testimonio completo]"
> — [Nombre], [Fuente]

**3.**
> "[Testimonio completo]"
> — [Nombre], [Fuente]

**4.**
> "[Testimonio completo]"
> — [Nombre], [Fuente]

**5.**
> "[Testimonio completo]"
> — [Nombre], [Fuente]

**Patrones en los testimonios:**
- [X]% hablan de: [tema recurrente 1 — ej: "rapidez"]
- [X]% hablan de: [tema recurrente 2 — ej: "calidad"]
- [X]% hablan de: [tema recurrente 3 — ej: "atención"]

**Testimonios visuales (fotos/videos de clientes):**
- [Si encontraste en redes: describir]
- [Si NO: "No se encontraron testimonios visuales en redes sociales"]

---

### b. ¿Cómo vas a recoger más testimonios de ahora en adelante?

**PASO 1: Detectar tipo de negocio**

Basado en la investigación, este negocio es un: **[ECOMMERCE / SERVICIOS / CURSO/INFOPRODUCTO / OTRO]**

---

**PASO 2: Estrategia de recolección específica para el tipo de negocio**

**Si es ECOMMERCE:**

**Estrategia principal: Email marketing post-compra**
- Automatización: Enviar email 5-7 días después de la entrega
- Asunto: "¿Cómo te fue con [producto]?"
- Incluir link directo a dejar review
- Incentivo: Cupón 10-15% para próxima compra

**Estrategia complementaria 1: WhatsApp marketing**
- Si tienes el WhatsApp del cliente, mensaje personalizado
- Más efectivo para productos de ticket alto ($100+ USD)
- Template: "Hola [nombre], ¿ya probaste [producto]? Nos encantaría saber tu experiencia 😊"

**Estrategia complementaria 2: Insertos en el paquete**
- Tarjeta física con QR code a página de reviews
- Texto: "Tu opinión nos importa - Escanea y cuéntanos"

---

**Si es SERVICIOS (presencial o remoto):**

**Estrategia principal: Solicitud presencial/en llamada**
- Momento ideal: Justo después de terminar el servicio, cuando el cliente está satisfecho
- Script: "¿Te pareció bien el servicio? ¿Nos dejarías una review en Google? Te mando el link por WhatsApp"
- Efectividad: 60-70% de solicitudes presenciales resultan en review (vs 10-15% por email)

**Estrategia complementaria 1: Google Business Reviews**
- Link directo acortado (goo.gl/maps/...)
- Enviarlo por WhatsApp inmediatamente después del servicio
- Seguimiento 24h después si no dejó review: "¿Pudiste dejar la reseña? Te toma 1 minuto"

**Estrategia complementaria 2: Programa de referidos**
- "Por cada review con foto/video, participas en sorteo de [beneficio]"
- O: "Deja review y obtén [descuento] en próximo servicio"

---

**Si es CURSO / INFOPRODUCTO:**

**Estrategia principal: Email secuencial durante el curso**
- Email 1: A mitad del curso - "¿Cómo vas? ¿Ya viste resultados?"
- Email 2: Al terminar - "¿Qué sección te ayudó más?" (capturar testimonios específicos)
- Email 3: 30 días después - "¿Aplicaste lo aprendido? Cuéntanos tu caso de éxito"

**Estrategia complementaria 1: Comunidad privada (grupo/Discord)**
- Canal dedicado de "casos de éxito"
- Incentivar a compartir: "Comparte tu resultado y te destacamos en nuestras redes"
- Los testimonios más activos se recopilan para marketing

**Estrategia complementaria 2: Reviews en plataforma**
- Si vendes en Hotmart/Udemy/etc, pedir review en la plataforma
- Ofrecer certificado de finalización solo si dejan review (+ completar curso)
- Recordatorio automático vía email de la plataforma

---

**Si es OTRO (o mix de tipos):**

Combina las estrategias más relevantes según el modelo de negocio detectado:
- [Lista 2-3 estrategias mezcladas de las anteriores que mejor apliquen]

---

**PASO 3: Benchmarking con competidores**

**Lo que funciona para competidores en este mercado:**
- **[Competidor 1]:** [Estrategia específica observada - ej: "Piden testimonios en video vía Instagram Stories"]
- **[Competidor 2]:** [Estrategia específica observada]
- **[Competidor 3]:** [Estrategia específica observada]

---

**PASO 4: Plan de acción inmediato**

**Configurar en los próximos 7 días:**
1. [Acción técnica 1 - ej: "Automatización de email en Klaviyo/Mailchimp"]
2. [Acción técnica 2 - ej: "Crear link acortado de Google Business"]
3. [Acción de proceso - ej: "Capacitar al equipo para solicitar reviews presencialmente"]

**Meta:** Pasar de [X] testimonios actuales a [Y] en los próximos 30 días
**Estrategia prioritaria:** [La estrategia #1 del tipo de negocio detectado]

---

## 6️⃣ OBJECIONES

### a. ¿Cuáles son los posibles "peros" por los cuales las personas no te comprarían?

**ANÁLISIS EN 2 PARTES:**
1. Objeciones detectadas en TUS fuentes (reviews negativas, preguntas frecuentes, búsquedas)
2. Objeciones detectadas en fuentes de COMPETIDORES (que tal vez tú no estás abordando)

---

**Lista completa de objeciones detectadas (mínimo 10):**

**1. [Objeción 1]**
- Detectada en: [Reviews negativas/Preguntas en redes/Búsquedas de Google/Amazon]
- Frecuencia: Mencionada [X] veces
- Evidencia: "[Cita textual]" — [Fuente]
- Cómo la resuelves actualmente: [Lo que dice tu web / "No la abordas"]

**2. [Objeción 2]**
- Detectada en: [fuente]
- Frecuencia: [X] menciones
- Evidencia: "[Cita]" — [Fuente]
- Cómo la resuelves: [respuesta o "No mencionada"]

**3. [Objeción 3]**
- Detectada en: [fuente]
- Frecuencia: [X] menciones
- Evidencia: "[Cita]" — [Fuente]
- Cómo la resuelves: [respuesta o "No mencionada"]

**4. [Objeción 4]**
- Detectada en: [fuente]
- Frecuencia: [X] menciones
- Evidencia: "[Cita]" — [Fuente]
- Cómo la resuelves: [respuesta o "No mencionada"]

**5. [Objeción 5]**
- Detectada en: [fuente]
- Frecuencia: [X] menciones
- Evidencia: "[Cita]" — [Fuente]
- Cómo la resuelves: [respuesta o "No mencionada"]

**6. [Objeción 6]**
- Detectada en: [fuente]
- Frecuencia: [X] menciones
- Evidencia: "[Cita]" — [Fuente]
- Cómo la resuelves: [respuesta o "No mencionada"]

**7. [Objeción 7]**
- Detectada en: [fuente]
- Frecuencia: [X] menciones
- Evidencia: "[Cita]" — [Fuente]
- Cómo la resuelves: [respuesta o "No mencionada"]

**8. [Objeción 8]**
- Detectada en: [fuente]
- Frecuencia: [X] menciones
- Evidencia: "[Cita]" — [Fuente]
- Cómo la resuelves: [respuesta o "No mencionada"]

**9. [Objeción 9]**
- Detectada en: [fuente]
- Frecuencia: [X] menciones
- Evidencia: "[Cita]" — [Fuente]
- Cómo la resuelves: [respuesta o "No mencionada"]

**10. [Objeción 10]**
- Detectada en: [fuente]
- Frecuencia: [X] menciones
- Evidencia: "[Cita]" — [Fuente]
- Cómo la resuelves: [respuesta o "No mencionada"]

---

**OBJECIONES DETECTADAS EN COMPETIDORES (que tú podrías estar pasando por alto):**

Revisa las reviews negativas, FAQs y páginas de ventas de los 3-5 competidores principales. Identifica objeciones que ELLOS mencionan/resuelven pero TÚ NO.

**1. [Objeción que competidores SÍ abordan pero tú NO]**
- Competidor que la menciona: [Nombre]
- Dónde la abordan: [FAQ/Garantía/Copy de venta]
- Cómo la resuelven: [Estrategia específica]
- ¿La abordas tú?: ❌ NO / ⚠️ Parcialmente / ✅ SÍ
- **Si NO la abordas:** Nivel de riesgo: 🔴 ALTO / 🟡 MEDIO / 🟢 BAJO

**2. [Objeción competidores abordan, tú no]**
- [Misma estructura]

**3. [Objeción competidores abordan, tú no]**
- [Misma estructura]

[Continúa si hay más...]

---

**Cómo competidores resuelven estas objeciones:**
- **Competidor 1:** [Estrategias observadas]
- **Competidor 2:** [Estrategias observadas]
- **Competidor 3:** [Estrategias observadas]

---

### b. ¿Cuál es la objeción MÁS IMPORTANTE? (Análisis integral: propias + competencia)

**METODOLOGÍA PARA SELECCIONAR LA #1:**

Considera estas 3 fuentes:
1. ✅ Objeciones más mencionadas en TUS reviews/interacciones
2. ✅ Objeciones que COMPETIDORES exitosos priorizan (la ponen en su home, garantía, etc.)
3. ✅ Objeciones con mayor impacto en conversión (aunque se mencionen poco, bloquean la venta)

**La objeción crítica es:** [Debe ser resultado del análisis de las 3 fuentes anteriores]

**"[Frase exacta de la objeción]"**

**Por qué es la más crítica:**
- Mencionada [X] veces (más que cualquier otra)
- Aparece en [fuentes específicas]
- Impacto: [Por qué afecta la venta — ej: "Evita que el 40% de personas interesadas finalicen la compra"]

**Evidencia:**
- "[Cita 1]" — [Fuente]
- "[Cita 2]" — [Fuente]
- "[Cita 3]" — [Fuente]

**¿Por qué esta y no otra?**
- Comparada con [objeción alternativa considerada], esta tiene [X razón de por qué es más crítica]
- Competidores exitosos la priorizan: [Evidencia - ej: "3 de 5 competidores la mencionan en su home"]
- Impacto estimado en conversión: [ej: "Resolver esto podría aumentar conversión en X% según benchmarks"]

**💡 Cómo resolverla:**

1. **En tu web:** [Acción específica — ej: "Agrega una sección de FAQ que diga: [texto sugerido]"]
2. **En tus anuncios:** [Acción específica — ej: "Incluye en el copy: [texto sugerido]"]
3. **En proceso de venta:** [Acción específica — ej: "Cuando alguien pregunte por WhatsApp, responde: [script sugerido]"]

**Lo que funciona para competidores:**
[Ejemplo específico de cómo un competidor exitoso resuelve esta objeción]

---

## 7️⃣ GARANTÍA

### a. ¿Qué garantía le ofrecerás a tus clientes para quitarles cualquier tipo de riesgo en el momento de la compra?

**Tu garantía actual:**

[Cita textual de tu web / "No mencionas garantía explícita actualmente"]

**Si NO tienes garantía clara, recomendación basada en el mercado:**

**Garantía sugerida:**
"[Texto de garantía específico basado en lo que funciona para competidores y tu tipo de negocio]"

**Por qué esta garantía funciona:**
- [Razón 1 basada en investigación]
- [Razón 2]

**Si SÍ tienes garantía, análisis:**

Tu garantía ofrece:
- ✅ [Aspecto positivo 1]
- ✅ [Aspecto positivo 2]
- ⚠️ Podría mejorar: [Aspecto específico]

---

### b. ¿Es esta garantía mejor que la de tus competidores?

**Comparación con competencia:**

| Aspecto | Tú | Comp 1 | Comp 2 | Comp 3 |
|---------|-----|--------|--------|--------|
| Devolución de dinero | [Sí/No/X días] | [Sí/No/X días] | [Sí/No/X días] | [Sí/No/X días] |
| Garantía de satisfacción | [Sí/No/Condiciones] | [Sí/No] | [Sí/No] | [Sí/No] |
| Cambios/reemplazos | [Sí/No/Condiciones] | [Sí/No] | [Sí/No] | [Sí/No] |
| Prueba gratis/periodo de prueba | [Sí/No/X días] | [Sí/No] | [Sí/No] | [Sí/No] |

**Evaluación:**

✅ **Superas a la competencia en:** [Aspectos donde eres mejor]

⚠️ **Oportunidad de mejora:** [Aspectos donde competidores te superan]

**Mejores prácticas del mercado:**
- [Competidor X ofrece: detalle específico que funciona bien]
- [Competidor Y ofrece: detalle específico]

**💡 Recomendación final:**

[Si tu garantía es mejor: "Tu garantía es MEJOR que la de tus competidores. DESTÁCALA más en tu web y anuncios porque es un diferencial poderoso"]

[Si puedes mejorar: "Mejora tu garantía a: [propuesta específica]. Esto eliminará la objeción #[X] y te hará más competitivo vs [competidor específico]"]

---

## 🎯 IDEAS DE ANUNCIOS (Basadas en esta investigación)

**Usando la información de las 7 Maletas, aquí hay 3 estructuras de anuncios:**

### Anuncio 1: Enfoque en Problema

**Headline:**
¿[Problema principal en pregunta]?

**Descripción:**
[Solución específica]. [Diferencial único].

[Resolver objeción crítica].

"[Mejor testimonial]" — [Nombre si está]

[Garantía] → [CTA]

**Ejemplo específico para tu negocio:**
[Llenar con datos reales del cliente]

---

### Anuncio 2: Enfoque en Diferencial Único

**Headline:**
[Diferencial que nadie más tiene]

**Descripción:**
[Cómo resuelve el problema principal].

[Testimonial].

[Garantía] → [CTA]

**Ejemplo específico:**
[Llenar con datos reales]

---

### Anuncio 3: Enfoque en Testimonial/Resultado

**Headline:**
"[Testimonio poderoso con resultado]"

**Descripción:**
[Problema que resuelve]. [Diferencial].

[Garantía] → [CTA]

**Ejemplo específico:**
[Llenar con datos reales]

---

## 📋 PRÓXIMOS PASOS CRÍTICOS

**Esta semana:**

1. **[Acción #1 más importante]**
   - Por qué: [Razón con datos]
   - Cómo: [Pasos específicos]
   - Impacto: [Resultado esperado]

2. **[Acción #2]**
   - Por qué: [Razón]
   - Cómo: [Pasos]
   - Impacto: [Resultado]

3. **[Acción #3]**
   - Por qué: [Razón]
   - Cómo: [Pasos]
   - Impacto: [Resultado]

**Este mes:**

**Validar con entrevistas reales:**
Habla con 5-10 clientes y pregunta:
1. "¿Por qué nos compraste a nosotros y no a [competidor X]?"
2. "¿Qué problema querías resolver cuando nos buscaste?"
3. "Si ya no existiéramos, ¿qué extrañarías más?"

Esto validará si los problemas y diferenciales detectados son correctos.

---

## 📚 FUENTES ANALIZADAS

**Sitios web:**
- [URL cliente]
- [URL competidor 1]
- [URL competidor 2]
- [URL competidor 3]

**Reviews analizadas:**
- Google Business [cliente]: [X] reviews
- Google Business [comp 1]: [Y] reviews
- Google Business [comp 2]: [Y] reviews
- Google Business [comp 3]: [Y] reviews
- Amazon [cliente/competidores]: [Z] reviews / ❌ N/A (servicio/infoproducto) / ❌ Sin presencia
- **Total:** [sum] reviews

**Búsquedas de Google procesadas:**
- [Query 1]
- [Query 2]
- [Query 3]
- [Query 4]
- [Query 5]

**Anuncios analizados:**
- [Competidor X]: [N] anuncios activos
- [Competidor Y]: [N] anuncios activos

**Redes sociales:**
- Instagram cliente: [URL]
- Facebook cliente: [URL]

**Total de datos procesados:**
- [X] páginas web
- [Y] reviews
- [Z] búsquedas
- [N] anuncios
- [M] posts/comentarios

---

*Investigación realizada con la metodología de Las 7 Maletas de Felipe Vergara - Adaptado por Muta Digital*
```

---

**PASO FINAL: Generar archivo HTML**

Usa el contenido markdown anterior para generar un archivo HTML con el siguiente template minimalista:

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Reporte 7 Maletas - [Nombre Empresa]</title>
    <link href="https://fonts.googleapis.com/css2?family=Montserrat:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { font-family: 'Montserrat', sans-serif; line-height: 1.7; color: #232323; background: #f5f5f5; }
        .container { max-width: 1200px; margin: 0 auto; background: white; }
        .header { background: #232323; padding: 48px 80px; border-bottom: 4px solid #FAD70B; }
        .header h1 { font-size: 48px; font-weight: 700; color: #FAD70B; margin-bottom: 12px; letter-spacing: -0.5px; text-transform: uppercase; }
        .header p { font-size: 17px; color: #a1a1a6; }
        .executive-summary { padding: 64px 80px; background: #fafafa; border-bottom: 1px solid #e5e5e5; }
        .summary-title { font-size: 32px; font-weight: 700; color: #232323; margin-bottom: 32px; text-transform: uppercase; }
        .metrics { display: grid; grid-template-columns: repeat(4, 1fr); gap: 32px; margin-top: 40px; }
        .metric-value { font-size: 56px; font-weight: 700; color: #FAD70B; line-height: 1; margin-bottom: 8px; }
        .metric-label { font-size: 13px; color: #6e6e73; font-weight: 600; text-transform: uppercase; letter-spacing: 0.5px; }
        .content { padding: 80px; }
        .section { margin-bottom: 96px; }
        .section-number { font-size: 13px; color: #232323; font-weight: 700; text-transform: uppercase; letter-spacing: 1px; margin-bottom: 8px; background: #FAD70B; display: inline-block; padding: 2px 10px; }
        .section-title { font-size: 40px; font-weight: 700; color: #232323; margin-bottom: 32px; text-transform: uppercase; }
        .subsection-title { font-size: 24px; font-weight: 600; color: #232323; margin: 48px 0 20px 0; }
        p { font-size: 17px; line-height: 1.7; margin-bottom: 16px; }
        ul, ol { margin: 24px 0; padding-left: 24px; }
        li { font-size: 17px; margin: 12px 0; }
        .data-table { width: 100%; border-collapse: collapse; margin: 32px 0; border: 1px solid #e5e5e5; border-radius: 12px; overflow: hidden; }
        .data-table thead { background: #232323; }
        .data-table th { padding: 16px 20px; font-size: 13px; font-weight: 600; text-align: left; border-bottom: 1px solid #e5e5e5; color: #FAD70B; text-transform: uppercase; letter-spacing: 0.5px; }
        .data-table td { padding: 20px; font-size: 16px; border-bottom: 1px solid #e5e5e5; }
        .info-box { background: #fafafa; padding: 32px; border-radius: 12px; margin: 32px 0; border-left: 4px solid #FAD70B; }
        .quote-box { background: white; border: 1px solid #e5e5e5; padding: 28px; border-radius: 12px; margin: 24px 0; }
        .footer { background: #232323; padding: 48px 80px; text-align: center; border-top: 4px solid #FAD70B; color: #a1a1a6; }
        @media (max-width: 768px) {
            .header, .content, .executive-summary, .footer { padding: 40px 24px; }
            .metrics { grid-template-columns: 1fr 1fr; }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>Reporte 7 Maletas</h1>
            <p>[Nombre Empresa] — [Fecha]</p>
        </div>
        <div class="executive-summary">
            <h2 class="summary-title">Resumen Ejecutivo</h2>
            <p>Investigación automatizada del mercado, competidores y clientes.</p>
            <div class="metrics">
                <div class="metric"><div class="metric-value">[X]</div><div class="metric-label">Reviews Analizadas</div></div>
                <div class="metric"><div class="metric-value">[Y]</div><div class="metric-label">Competidores</div></div>
                <div class="metric"><div class="metric-value">[Z]</div><div class="metric-label">Fuentes Consultadas</div></div>
                <div class="metric"><div class="metric-value">7</div><div class="metric-label">Maletas Completadas</div></div>
            </div>
        </div>
        <div class="content">
            <!-- Aquí va el contenido de las 7 maletas con el formato adecuado -->
            <!-- Usa <div class="section"> para cada maleta -->
            <!-- Usa <div class="info-box"> para destacar puntos clave -->
            <!-- Usa <div class="quote-box"> para evidencias/citas -->
        </div>
        <div class="footer">
            <p>Reporte generado por el sistema 7 Maletas</p>
        </div>
    </div>
</body>
</html>
```

Guarda el archivo con el nombre correcto (slug de la empresa + fecha) en el directorio actual.

---

Después de entregar el reporte HTML, infórmale al usuario:

```
✅ Investigación completada

📄 Reporte generado: 7-maletas-[empresa]-[fecha].html

📋 Resumen de la investigación:
- ✅ Las 7 Maletas respondidas con evidencia
- ✅ [X] fuentes consultadas
- ✅ [Y] reviews analizadas
- ✅ [Z] competidores analizados

🌐 Abre el archivo HTML en tu navegador para ver el reporte completo con diseño profesional.

💡 Próximo paso crítico: Valida estos hallazgos hablando con 5-10 clientes reales.

¿Quieres que te ayude con algo más?

Opciones:
1. Crear anuncios de Meta Ads basados en estas 7 Maletas
2. Investigar un competidor específico en profundidad
3. Hacer otra investigación para un negocio diferente
```

---

## Manejo de Situaciones Especiales

### Si la URL del cliente no carga
1. Ejecuta WebSearch: `"{nombre empresa}" sitio web`
2. Si encuentras otra URL, úsala
3. Si NO encuentras web: informa al usuario y pide URL alternativa o continúa sin análisis de web (usando solo Google Business y competidores)

### Si no tiene ficha de Google Business
1. Intenta buscarla con múltiples variantes del nombre
2. Si definitivamente no existe: informa al usuario y continúa sin ese análisis
3. Compensa con más énfasis en redes sociales y competidores

### Si no encuentras competidores
1. Amplía la búsqueda a nivel nacional (quita la ciudad del query)
2. Busca competidores indirectos (mismo problema, diferente solución)
3. Si aun así no hay: informa al usuario — puede ser un nicho muy específico o una oportunidad de mercado

### Si un competidor no tiene reviews
1. Sáltalo y busca otro competidor
2. Prioriza competidores con presencia digital completa
3. Si NINGÚN competidor tiene reviews: usa solo la información de sus webs

### Si el nicho no tiene presencia digital
1. Enfócate más en búsquedas de Google (trucos ESPACIO y ASTERISCO)
2. Busca productos similares en otros mercados/países
3. Informa al usuario que hay una GRAN oportunidad — poca competencia digital

### Si encuentras información conflictiva
1. Prioriza en este orden:
   - Reviews recientes (últimos 3 meses) > reviews antiguas
   - Patrones que se repiten > menciones únicas
   - Datos del cliente > datos de competidores
2. Menciona en el reporte si hay conflictos: "Mientras que X fuentes dicen A, Y fuentes dicen B — posible segmentación de mercado"

### Si el usuario pide investigación en otro idioma
1. Ejecuta las búsquedas en el idioma del mercado objetivo
2. Si encuentras contenido en otros idiomas, tradúcelo en el reporte
3. Mantén el reporte final en español (o el idioma que el usuario esté usando contigo)

### Si hay muy poca información disponible
1. Informa al usuario honestamente qué encontraste y qué NO
2. No inventes datos
3. En el reporte, marca claramente las secciones con "Información insuficiente — se requiere investigación manual"
4. Sugiere métodos alternativos (entrevistas, encuestas, etc.)

---

## Notas Importantes

- **Tiempo total estimado:** 20-30 minutos (puede variar según cantidad de competidores y disponibilidad de información)
- **No inventes datos:** Si no encuentras información, dilo explícitamente. Es mejor un "No encontrado" honesto que un dato inventado
- **Citas textuales:** Siempre que sea posible, usa citas textuales en lugar de parafrasear — son más poderosas
- **Patrones > casos únicos:** Busca qué se REPITE, no casos aislados
- **Accionable > teórico:** Cada recomendación debe ser algo que el usuario pueda HACER, no solo "mejorar la comunicación" sino "actualizar el headline de tu home page a: [texto específico]"
- **Evidencia > opinión:** Cada hallazgo debe estar respaldado por datos (reviews, búsquedas, webs)
- **Contexto local:** Si el negocio es local, todo debe estar contextualizado a esa ciudad/región
- **Validación manual necesaria:** Siempre recuerda al usuario que debe validar con entrevistas reales — esta investigación es el 70%, no el 100%

---

## Créditos

Metodología basada en **Las 7 Maletas de Cualquier Compra** de Felipe Vergara, adaptado por Muta Digital.


---

**¿Listo para empezar? Proporcióname los 3 datos de tu negocio y comenzaré la investigación automática.**