# Mecánica interna de Meta Ads

Referencia técnica sobre cómo funciona el sistema de Meta por dentro. Leer cuando el diagnóstico requiera explicar por qué el sistema se comporta de cierta manera, o cuando el usuario pregunte por qué Meta está haciendo algo que parece ilógico.

---

## Cómo funciona la subasta

Cada vez que hay una oportunidad de mostrar un anuncio, Meta corre una subasta. Gana el que tiene mayor Valor Total:

```
Valor Total = (Bid del anunciante) × (Tasa de acción estimada) + (Calidad del ad)
```

- **Bid**: definido por la estrategia de puja
- **Tasa de acción estimada**: probabilidad predicha de que el usuario haga la acción deseada
- **Calidad del ad**: feedback de usuarios, señales de baja calidad

Implicación clave: un bid más bajo puede ganar si la tasa de acción estimada y la calidad son más altas. La relevancia baja costos. Por eso mejorar creativos muchas veces baja el CPM.

---

## Estrategias de puja

| Tipo | Estrategia | Qué hace |
|---|---|---|
| Gasto | Highest Volume | Maximiza conversiones gastando todo el presupuesto |
| Gasto | Highest Value | Maximiza valor de compra gastando todo |
| Objetivo | Cost per Result Goal | Intenta mantener CPA alrededor del target |
| Objetivo | ROAS Goal | Intenta mantener ROAS alrededor del target |
| Manual | Bid Cap | Pone un techo al bid en cada subasta |

---

## Breakdown Effect (efecto desglose)

El error más común de interpretación: ver en un reporte que un segmento, ubicación o anuncio tiene CPA más alto y concluir que Meta está "desperdiciando" presupuesto ahí.

**Por qué es un error:** el sistema optimiza por eficiencia *marginal*, no promedio. Un segmento con CPA promedio alto puede estar siendo usado para cubrir audiencia que de otro modo no se alcanza, protegiendo la eficiencia total de la campaña.

**Nivel correcto de evaluación según configuración:**

| Configuración | Nivel correcto |
|---|---|
| Advantage+ Campaign Budget / CBO | Campaña — no tomar decisiones por adset |
| Placements automáticos sin CBO | Ad Set — no tomar decisiones por ubicación |
| Múltiples ads en un adset | Ad Set — no tomar decisiones por anuncio individual |

**Regla de oro:** nunca juzgar las decisiones del sistema por CPA promedio en reportes de breakdowns.

---

## Reparto forzado de presupuesto dentro de una CBO

Meta **no respeta el gasto mínimo por conjunto, pero sí respeta el máximo.**

Cuando hace falta que un anuncio concreto consuma presupuesto sí o sí (una colaboración, una acción con influencer, un contenido que tiene que mostrarse aunque el sistema prefiera otro), el camino es **poner tope máximo a los conjuntos que no queremos que se coman el presupuesto**, no poner mínimo al que queremos empujar. Para un reparto 50/50 entre dos conjuntos, tope máximo del 50% en cada uno: el sistema se mueve dentro de ese margen y lo que no puede gastar de un lado se va al otro.

Caso típico donde aparece: en campañas de tráfico, un anuncio en colaboración compite contra anuncios que salen solo desde la cuenta propia, y el sistema tiende a posicionar los de la cuenta propia. Abrir una campaña nueva para separarlos resuelve el reparto pero rompe el tracking, porque quedan dos campañas midiendo lo mismo. Antes de partir la campaña, evaluar el tope.

---

## Learning Phase

Cuando un adset es nuevo o recibió ediciones significativas, entra en fase de aprendizaje:

- El delivery muestra "Learning" en el Administrador de Anuncios
- Meta está explorando a quién mostrar el ad y cómo
- Performance inestable — CPA típicamente más alto que el estado estable
- Sale de la fase después de ~50 resultados en 7 días

**Learning Limited:** indica que el adset no puede completar el aprendizaje porque tiene presupuesto muy bajo, audiencia muy restringida, o hay demasiados adsets fragmentando el presupuesto.

Regla: NUNCA hacer juicios definitivos sobre un adset en learning. Todos los findings deben presentarse como preliminares.

---

## Pacing

El pacing controla cómo Meta distribuye el presupuesto a lo largo del tiempo para no gastarlo todo de golpe al inicio del día o el período.

Explica por qué:
- El spend diario varía aunque el presupuesto sea el mismo
- Meta "retiene" en momentos donde el costo de impresión sube (ej: fines de semana, fechas especiales)
- Un día de bajo spend no es señal de problema si el promedio semanal está bien

No alarmar al usuario por variaciones diarias normales.

---

## Auction Overlap

Cuando varios adsets de la misma cuenta tienen audiencias que se solapan, múltiples anuncios propios compiten en la misma subasta. Meta elige el de mayor Valor Total y excluye los demás.

**Síntomas:**
- Adsets que no gastan el presupuesto completo
- No alcanzan suficientes resultados para salir de learning
- Performance impredecible al escalar

**Soluciones:**
- Combinar adsets similares → consolida el aprendizaje en uno solo
- Apagar los adsets solapados y mover presupuesto al que está funcionando

---

## Ad Relevance Diagnostics

Meta muestra tres indicadores de diagnóstico para cada anuncio:

| Indicador | Qué mide | Si está BELOW_AVERAGE |
|---|---|---|
| Quality Ranking | Calidad percibida vs ads que compiten por la misma audiencia | Creativo de baja calidad, clickbait |
| Engagement Rate Ranking | Tasa de engagement esperada vs competencia | El ad no genera interés ni acción |
| Conversion Rate Ranking | Tasa de conversión esperada vs competencia | Problema post-clic (landing, oferta, precio) |

Todos BELOW_AVERAGE → mismatch entre audiencia y creativo. Reconsiderar targeting o creativo desde cero.
