# Cómo correr los evals

Cada skill que los tiene guarda sus casos en `skills/<skill>/evals/evals.json`. Un caso es tres cosas: un prompt de entrada realista, qué debería salir, y una lista de aserciones verificables (sí o no, sin gris).

## Cuándo correrlos

Cada vez que se toca una skill, antes de subirle la versión. No son un ritual diario: son la forma de saber si un cambio la mejoró o la empeoró.

## Cómo

1. Abrí una sesión limpia de Claude con el plugin instalado (sin historial que contamine).
2. Pegá el prompt del caso, tal cual. Donde dice `[MARCA]`, `[CASO]` o `[GUION...]`, usá material real: el brief y la guía de voz de Muta sirven de fixture estándar.
3. Dejá que la skill responda y, si el caso lo pide, seguí la conversación (por ejemplo, el caso 1 espera que la skill pregunte; contestale y mirá qué hace después).
4. Marcá cada aserción: cumplida o no. Sin "más o menos" — si dudás, no cumplió.
5. Anotá el resultado como fracción ("7 de 8") con la fecha y la versión de la skill.

## Dónde se anota

En `VERSIONS.md` (raíz del repo), en la fila de la skill, o como línea aparte si el historial crece. Lo que importa es poder comparar: si la versión anterior daba 7 de 8 y la nueva da 5 de 8, el cambio empeoró la skill y no se pushea hasta entender por qué.

## La regla

Un cambio a una skill sin evals corridos es un cambio a ciegas. "Me parece que quedó mejor" fue exactamente lo que dejó pasar meses de guiones genéricos.
