---
name: meta-ads-analysismd
description: >
  Ejecuta un análisis técnico profundo y multinivel de campañas de Meta Ads. Usá esta skill SIEMPRE que el usuario pida analizar o revisar campañas de Meta/Facebook/Instagram; diagnosticar por qué una campaña no convierte o está cara; evaluar ROAS, CPL, CPM, CPC, CTR, frecuencia, costo por conversación o costo por resultado; revisar adsets, anuncios o creativos; detectar fatiga de audiencia; auditar el embudo; descubrir qué está pasando y por qué; recomendar qué pausar, escalar u optimizar. Activar también si el usuario dice "analizá mis campañas", "qué está pasando con mis ads", "mirá el rendimiento de Meta", "hay audiencias que se solapan", "los creativos están fatigados", "por qué me sube el CPL", "cómo está el ROAS". Funciona con archivos subidos (CSV, Excel), acceso directo a la cuenta vía token, o MCP activo (Windsor.ai, Supermetrics).
metadata:
  version: 1.0.0
---

# Meta Ads — Framework de Análisis

El objetivo es entender qué está pasando, por qué, y tomar decisiones concretas. El output es diagnóstico conversacional con accionables priorizados.

---

## Paso 0: Detectar la fuente de datos y el modo de trabajo

Identificar cómo llegaron los datos y actuar en consecuencia:

**Modo archivo (CSV / Excel subido por el usuario)**
Leer el archivo, mapear columnas al nombre estandarizado (ver tabla en references/benchmarks.md), informar si falta algún campo crítico, y continuar con los niveles posibles. No aplican reglas de seguridad de API.

**Modo API / token (acceso directo a la cuenta)**
El usuario proporcionó un token o hay un conector activo (Windsor.ai, Supermetrics, MetaAPI u otro). ANTES de hacer cualquier llamada, leer `references/anti-ban.md` completo y aplicar todas las reglas ahí definidas. Sin excepción.

**Sin datos todavía**
Pedirle al usuario que exporte desde el Administrador de Anuncios de Meta. Indicarle qué columnas incluir según el nivel de análisis deseado.

> Si hay ambigüedad sobre la fuente, preguntar antes de continuar.

---

## Principios centrales

- **Holístico primero**: evaluar a nivel agregado antes de hacer drill down. Meta optimiza para el todo, no para las partes.
- **Dinámico sobre estático**: analizar tendencias, no fotos puntuales. Los promedios mienten.
- **Marginal sobre promedio**: un segmento con CPA promedio alto puede estar protegiendo la eficiencia total de la campaña.
- **Diagnóstico antes que juicio**: antes de recomendar pausar cualquier cosa, verificar si hay learning phase, si es CBO, y si el dato tiene volumen suficiente.

---

## Reglas obligatorias para recomendaciones

- NUNCA recomendar pausar o bajar presupuesto de un segmento solo porque tiene CPA promedio más alto en un reporte de breakdowns.
- SIEMPRE justificar con evidencia de datos, mecánica del sistema de Meta, e impacto esperado en la campaña completa.
- CADA insight debe incluir evidencia y explicación. Cada recomendación debe ser accionable y verificable.
- Nunca usar "clicks" solo — usar "Clicks (all)" o "Link Clicks" para desambiguar.
- Los cambios de presupuesto o exclusiones de segmentos son tests, no sentencias: siempre con período de evaluación mínimo de 7 días.

---

## Proceso de análisis

### Paso 1: Nivel correcto de evaluación

ANTES de analizar nada: ¿la campaña usa CBO (presupuesto a nivel campaña)?
- Sí → evaluar a nivel campaña, no por adset
- No → evaluar a nivel adset, no por ubicación ni por anuncio individual

Esto es crítico para evitar el Breakdown Effect. Ver `references/mecanica-meta.md` si necesitás más contexto.

### Paso 2: Detectar el tipo de campaña

| Tipo | Objetivo configurado | Resultado principal |
|---|---|---|
| Ventas | Compras / ROAS | Compras, ROAS |
| Interacción / Mensajes | Conversaciones | Conversaciones iniciadas |
| Leads — Formulario | Clientes potenciales (formulario) | Leads |
| Leads — Sitio web | Clientes potenciales (web) | Leads |
| Reconocimiento | Alcance / Recuerdo / Thruplay | Alcance, CPM |

Si el tipo no está claro en los datos, preguntar al usuario antes de continuar.

### Paso 3: Pedir el objetivo de negocio

Los benchmarks genéricos no alcanzan. Preguntar según el tipo:

| Tipo | Pregunta |
|---|---|
| Ventas | "¿Cuál es tu ROAS objetivo?" |
| Leads | "¿Cuál es tu CPL objetivo?" |
| Mensajes | "¿Cuál es tu costo por conversación objetivo?" |
| Reconocimiento | No aplica — comparar vs período anterior |

Si el usuario no sabe su objetivo:
- ROAS mínimo ≈ 1 / margen neto × 1,5
- CPL máximo ≈ Ticket promedio × tasa de cierre × margen × 0,3

Regla de semáforo sobre el objetivo del usuario:
- ≥ objetivo → 🟢
- Entre 80% y 100% del objetivo → 🟡
- < 80% del objetivo → 🔴

### Paso 4: Check de learning phase

¿Está el adset en learning? ¿Hubo ediciones recientes? Si sí → caveatar TODOS los findings como preliminares. NUNCA hacer juicios definitivos sobre un adset en learning.

### Paso 5: Aplicar las 3 Qs

Para cada nivel relevante (campaña → adset → anuncio):

**1️⃣ ¿Qué pasó?** — Métricas principales vs objetivo del usuario
**2️⃣ ¿Por qué pasó?** — Diagnóstico: embudo, creativos, audiencia, mecánica del sistema
**3️⃣ ¿Qué haremos?** — Accionables ordenados por urgencia con período de evaluación

Leer `references/benchmarks.md` para aplicar los semáforos correctos según el tipo de campaña detectado en el Paso 2.

### Paso 6: Vista general de cuenta (si hay datos multi-campaña)

Inversión total, ROAS/CPA, CPM, frecuencia, volumen de conversiones — todo vs período anterior.

### Paso 7: Análisis temporal (si hay datos de fechas)

- Tendencia de ROAS y CPA en los últimos 14-30 días
- ¿Mejora o empeora?
- Días atípicos que expliquen variaciones
- Fluctuaciones normales (20-30%) vs preocupantes (>50% sostenido)

---

## Antes de recomendar pausar un adset o anuncio

Verificar siempre antes de recomendar pausar:
- ¿Es CBO? → Meta ya está optimizando; necesitás mínimo 7 días de datos sólidos
- ¿Está en learning? → Nunca pausar durante el aprendizaje
- ¿Representa <10% del gasto total? → Meta ya lo está ignorando; pausar no cambia mucho
- ¿Es el único anuncio activo del adset? → No pausar; el adset queda sin entrega
- Nunca pausar varios anuncios a la vez — un cambio a la vez para no resetear el aprendizaje

---

## Formato de accionables (obligatorio)

Cada accionable debe ser específico y ejecutable:

❌ "Mejorar los creativos"
✅ "Renovar el creativo del Ad #123 (CTR cayó 22% en 7 días, frecuencia 4,1). Mantener el ángulo pero cambiar el hook visual. Evaluar a los 7 días."

Niveles:
- URGENTE: impacta directamente el resultado principal (ROAS, CPL, costo por conversación)
- IMPORTANTE: impacta la eficiencia a mediano plazo
- OPORTUNIDAD: mejora posible sin urgencia

---

## Reglas generales

- NUNCA inventar datos. Si un campo no está disponible, decirlo.
- Si una métrica tiene <100 impresiones, marcarla como dato insuficiente.
- Comparar siempre contra período anterior cuando los datos estén disponibles.
- Tono: técnico y accionable. Cada sección termina con una conclusión clara y un "¿qué hacemos?".
- Los accionables son para el equipo de la agencia, no el cliente final.

---

## Referencias internas

- `references/benchmarks.md` — Tablas de semáforos y embudos por tipo de campaña. Leer en el Paso 5.
- `references/mecanica-meta.md` — Breakdown Effect, Learning Phase, subasta, pacing, Auction Overlap. Leer cuando el diagnóstico requiera explicar por qué el sistema se comporta de cierta manera.
- `references/anti-ban.md` — Reglas de seguridad para acceso directo a la API vía token. Leer COMPLETO en el Paso 0 si el modo de trabajo es API/token.

---

*Metodología de las 3 Qs basada en el trabajo de Felipe Vergara: https://www.youtube.com/@FelipeVergara*
