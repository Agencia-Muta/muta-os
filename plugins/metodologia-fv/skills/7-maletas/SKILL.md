---
name: 7-maletas
description: Investiga clientes, competidores y mercado de un negocio con las 7 Maletas de Felipe Vergara y entrega el reporte base para campañas. Usar ante '7 maletas' o 'investigá a los clientes de X'.
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

**Estructura del contenido:** lee `references/plantilla-reporte.md` completo antes de escribir el reporte. Trae las preguntas de cada maleta y la forma de cada respuesta; se completan todas.

---

**PASO FINAL: Generar archivo HTML**

Con ese contenido genera el archivo HTML usando el template de `references/plantilla-html.md`.


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