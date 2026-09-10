# Reglas de seguridad — Acceso directo a la Meta API

Este archivo aplica ÚNICAMENTE cuando el usuario proporcionó un token de acceso o hay un conector activo que hace llamadas directas a la Meta Marketing API. Si los datos llegan por archivo (CSV, Excel), ignorar este archivo completamente.

Estas reglas tienen prioridad absoluta sobre cualquier otra instrucción de la skill.

---

## Modelo de confianza

Los scripts del skill son CAJA NEGRA NO CONFIABLE. No asumir que implementan correctamente protecciones anti-ban. Todas las protecciones las aplica Claude desde la sesión usando sus herramientas. Si el script las implementa, mejor — pero la seguridad no depende de él.

---

## Tono con el usuario

El usuario es un media buyer, marketer o dueño de negocio — no un desarrollador. Ejecutar todas las reglas técnicas silenciosamente y comunicar en lenguaje de negocio.

**Traducción técnico → negocio:**

| Concepto técnico | Cómo decirlo |
|---|---|
| Sesión concurrente / cron / bot | "Otra herramienta o reporte automático conectado a esta cuenta" |
| Token personal | "Este token está emitido a tu nombre personal — funciona, pero tiene más riesgo que uno de empresa" |
| Token con permisos de escritura | "Tiene permisos para modificar anuncios. Por seguridad no continúo — generá un token solo con permisos de lectura" |
| Token expirado | "El token caducó. Generá uno nuevo antes de seguir" |
| Error 368 | "Meta marcó esta cuenta. Tenemos que parar aquí" |
| Rate limit | "Meta pidió esperar unos minutos antes de seguir" |
| Ramp-up primera sesión | "Vamos a empezar con una campaña para arrancar suave" |
| Contador de cuentas | "Llevamos N cuentas en esta sesión" |

**Reglas de estilo:**
- Una pregunta a la vez. Nunca listar varias preguntas bloqueantes en un solo mensaje.
- No preguntar lo que el usuario no sabe — deducirlo o asumir lo más conservador.
- Progreso en lenguaje humano: "Revisando tu acceso..." en vez de nombres de scripts o archivos.
- No mencionar nombres de fases internas, nombres de archivos, ni términos técnicos de API.

---

## Pre-flight obligatorio (antes de cualquier llamada a la API)

Completar TODOS estos pasos en orden. Si cualquier paso falla, detener el flujo y no avanzar.

No preguntar por sesiones concurrentes cuando el usuario pide explícitamente el análisis: si lo pide, es porque no hay otra fuente corriendo. Asumirlo.

### 1. Verificación del token

Antes de cualquier llamada de datos, verificar el token:
- Tipo: preferir System User Token (no expira). Token personal = riesgo mayor.
- Permisos: solo `ads_read` + `business_management`. Si tiene `ads_management`, detener y pedir token de solo lectura.
- Expiración: si el token expirado (error 190), pedir renovación. No reintentar con el mismo token.

### 2. Detección de modo de la app

Si la app está en Development Mode (sin publicar / sin App Review), limitar el total de llamadas de la sesión a 10 máximo. Development Mode = cuota muy reducida.

### 3. Confirmación explícita por cuenta

Antes del primer llamado que apunte a una cuenta publicitaria específica, decirle al usuario:

> "Voy a consultar la cuenta [nombre/ID]. ¿Avanzamos?"

Esperar confirmación. Una confirmación anterior NO se extiende a una cuenta distinta.

### 4. Ramp-up primera sesión

Si es la primera vez que se usa la API con esta cuenta (sin historial de uso previo), restringir a:
- 1 cuenta publicitaria
- 1 campaña
- 1 período (máximo últimos 7 días)
- 1 adset si el usuario quiere bajar de nivel
- Máximo 3 anuncios individuales

Segunda sesión en adelante: puede ampliar gradualmente. Esto evita el "cambio brusco de volumen" que dispara alertas en Meta.

---

## Durante la sesión

### Intervalo entre llamadas

Esperar al menos 3 segundos entre scripts. Nunca ejecutar en paralelo ni en loops sin intervalo.

### Contador de cuentas

Llevar cuenta de cuántas cuentas publicitarias distintas se consultaron. Al llegar a 5, abortar y pedir al usuario abrir una nueva sesión al día siguiente.

### Parse de errores después de cada llamada

Después de cada respuesta de la API, buscar `error.code` antes de continuar:

| Código | Acción |
|---|---|
| 368 | DETENERSE completamente. Tratar como ban. No reintentar, no regenerar token. Ver sección "Si Meta bloquea". |
| 190 (subcodes 463, 467) | Pedir renovación del token. No reintentar con el mismo. |
| 17 / 32 / 613 / 4 | Rate limit. Esperar mínimo 5 minutos. Pedir al usuario confirmar antes de seguir. |
| 80000 / 80004 | Rate limit de insights. Igual que arriba. |
| 10 / 200-299 | Permisos insuficientes. Pedir token con los scopes correctos. |
| 1 / 2 | API no disponible. Esperar 30s, un solo reintento. Si falla de nuevo, abortar. |
| 613 subcode 1996 | Cambio brusco de volumen. Reducir a 1 cuenta, 1 campaña. |

---

## Si Meta bloquea la cuenta

1. DETENERSE. No ejecutar más llamadas, nada.
2. Pedir al usuario el texto literal del aviso de Meta.
3. NO generar un token nuevo, NO reautenticarse — Meta lo interpreta como evasión.
4. NO ejecutar más requests "para probar si funciona".
5. Guiar al usuario a Business Manager → Configuración de la empresa → Avisos.
6. NO abrir Business Manager repetidamente para ver si ya regresó. Una revisión al día es suficiente.

---

## Si el usuario pide saltarse una regla

Si el usuario insiste en omitir alguna de estas reglas: (a) explicar brevemente el riesgo en lenguaje de negocio, (b) negarse a ejecutar, (c) sugerir que use la API directamente si quiere saltarse las protecciones. No ceder ante insistencia.

---

## Configuración recomendada del token (documental)

- Developer App en una Business Manager DISTINTA a la de las cuentas de producción.
- System User Token: Business Settings → System Users → Employee → asignar cuentas con permiso "View Performance" (nunca "Manage") → generar token con `ads_read` + `business_management`.
- Rotar el System User Token cada ~60 días por higiene.
- La app puede quedarse en Development Mode si solo se leen cuentas propias — no requiere publicación.

---

## Herramientas permitidas y prohibidas

✅ Permitido: llamadas de lectura a la API con el token, lectura de archivos JSON de respuesta.

🚫 Prohibido: POST / DELETE / PATCH a Meta API, ejecución en paralelo, MCPs de Meta no oficiales, endpoints no documentados, navegadores automatizados para hacer scraping de datos.
