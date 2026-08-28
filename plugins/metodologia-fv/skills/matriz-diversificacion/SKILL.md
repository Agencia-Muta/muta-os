---
name: matriz-diversificacion
description: >
  Genera una matriz de diversificación creativa para anuncios: cruza los deseos humanos de la audiencia (16 Deseos de Reiss) con los 5 niveles de conciencia de Schwartz para producir 30 hooks con sus ángulos estratégicos, listos para campañas de Meta Ads. Usá esta skill cuando el usuario diga "matriz de diversificación", "diversificá los ángulos", "hooks por nivel de conciencia", "ángulos para la campaña", "matriz de hooks", "necesito variedad de ángulos para los ads", o quiera cubrir prospección fría y retargeting con creatividades distintas. Basada en la metodología de Felipe Vergara.
metadata:
  version: 1.0.0
---

# Matriz de Diversificación Creativa

Producís una matriz de hooks que diversifica los ángulos de una campaña cruzando **deseos humanos profundos** con **niveles de conciencia del comprador**. El resultado: 30 hooks accionables que cubren desde la prospección más fría hasta el cierre.

## Paso 0 — Datos del negocio

Pedí lo que falte (no avances con campos vacíos):
1. **¿Qué vende?** (producto/servicio)
2. **¿A quién?** (audiencia general)
3. **¿Precio o rango?**
4. **¿Qué transformación ofrece?** (qué logra el cliente)
5. **Diferenciadores** (opcional: prueba social, garantías, credenciales)

Si el usuario tiene research previo del negocio (análisis de audiencia, reviews, testimonios, un reporte de investigación de mercado), pedilo como input: mejora la calidad de los deseos y perfiles. Si no tiene, avanzá igual con los 5 datos de arriba.

**Rondas previas (input opcional).** Preguntá si ya se hicieron matrices anteriores para este producto y pedí qué deseos y ángulos se trabajaron. No recordás nada entre conversaciones: si el usuario no te lo pasa, trabajás desde cero.

Con historial, la regla es: **no repitas la combinación deseo + ángulo ya trabajada, pero sí podés repetir el deseo.** Los deseos que mueven a un mercado son finitos y se agotan; los ángulos para expresarlos no. Cuando un deseo ya fue usado, buscá una entrada distinta: otra situación de uso, otro momento del día, otro personaje, otra consecuencia, otro enemigo común, otro formato de prueba. Ahí vive la diversificación real.

Los dos modos conviven y el usuario elige (o proponé vos el mix): **profundizar** (mismos deseos ya validados, ángulos nuevos) o **explorar** (deseos que todavía no se tocaron). Si hay research previo, usalo para saber qué está confirmado, no para limitarte a eso: un mercado también se abre por deseos que los clientes no verbalizan en las reviews.

## Paso 1 — Seleccioná los 3 deseos más relevantes

De los **16 Deseos Básicos de Steven Reiss**, elegí los 3 que más aplican a esta audiencia y justificá cada uno en una línea:

Acceptance (ser apreciado) · Curiosity (aprender) · Eating (alimentarse) · Family (criar/cuidar) · Honor (lealtad a valores del grupo) · Idealism (justicia social) · Independence (autonomía) · Order (estructura y organización) · Physical activity (ejercitar el cuerpo) · Power (influencia y control) · Romance (sexo y belleza) · Saving (acumular) · Social contact (pertenecer) · Social status (prestigio) · Tranquility (seguridad, evitar ansiedad) · Vengeance (competir y ganar)

Atajo alternativo si Reiss resulta demasiado granular — los 6 **Primary Life Outcomes**: salud, estatus, riqueza, relaciones, seguridad, tiempo libre/autonomía. Mismo uso: elegí los 3 que más pesan para esta audiencia.

## Paso 2 — Definí 2 perfiles de cliente

Dos segmentos con motivaciones o situaciones distintas, aunque compren lo mismo. Descripción corta y concreta de cada uno.

## Paso 3 — Generá la matriz (6 filas × 5 columnas = 30 hooks)

Columnas = **niveles de conciencia de Eugene Schwartz**:
1. **Inconsciente:** no sabe que tiene un problema
2. **Problema:** sabe que algo está mal, no conoce soluciones
3. **Solución:** busca soluciones, no conoce tu producto
4. **Producto:** conoce tu producto, no está convencido
5. **Decisión:** listo para comprar, necesita el empujón

**Regla crítica de ángulo:**
- Columnas **Inconsciente y Problema** → ángulo de **DOLOR/EVITAR** (el reverso negativo del deseo)
- Columnas **Solución, Producto y Decisión** → ángulo de **GANANCIA/LOGRAR** (el deseo en positivo)

**Ejemplo calibrado (brownie keto):** Inconsciente: "Tu fuerza de voluntad se agota y terminás comprando tres." · Problema: "¿Cuántas veces arruinaste tu semana de dieta por un antojo?" · Solución: "Existe una forma de comer un brownie todos los días sin salirte de tus macros." · Producto: "6 gramos de carbohidratos netos, sin azúcar, sin gluten. Envíos a todo México." Notá la progresión dolor → beneficio y la especificidad creciente.

**Por qué esto importa en Meta:** el algoritmo sabe en qué estadio está cada usuario y exige diversificación creativa — volumen amplio de anuncios con mensajes radicalmente distintos. Una matriz bien hecha alimenta exactamente eso.

Cada celda incluye: el hook entre comillas + el ángulo estratégico en una línea (por qué funciona para ese nivel).

## Formato de salida

```
1. RESUMEN ESTRATÉGICO
- Deseos: [3 deseos + justificación de 1 línea c/u]
- Perfiles: [2 perfiles + descripción corta]

2. MATRIZ DE HOOKS
| Deseo | Perfil | Inconsciente | Problema | Solución | Producto | Decisión |
[6 filas: deseo 1×perfil 1, deseo 1×perfil 2, deseo 2×perfil 1, ...]

3. RECOMENDACIONES DE USO
- Prospección fría (no te conocen): [qué celdas usar y por qué]
- Retargeting (ya interactuaron): [qué celdas usar y por qué]
```

Si el usuario quiere la matriz en una planilla, entregala en formato Google Sheets para Argentina (separador `;`, decimales con `,`) o generá el xlsx.

## Reglas de calidad

- Cada hook debe ser específico del producto y la audiencia — si sirve para cualquier marca de la categoría, reescribilo
- Los hooks de dolor generan identificación, no terror: agitar sin exagerar
- No inventes datos, testimonios ni estadísticas: si no hay prueba social real, usá ángulos que no la requieran
- Los 30 hooks deben ser genuinamente distintos entre sí — si dos celdas suenan parecido, una está mal
- Si el usuario pasó rondas anteriores, ninguno de los 30 puede repetir una combinación deseo + ángulo ya trabajada. Entregá siempre los 30 completos: el historial cambia por dónde entrás, no cuántos producís

Siguiente paso natural: los hooks ganadores pasan a `guion-video` (guion completo del ad) o a `hook-lab` (más variantes del ángulo ganador).
