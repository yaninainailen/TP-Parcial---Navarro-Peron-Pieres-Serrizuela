# El agente evaluador

Parcial de la materia "Programación de y con Agentes de IA" — MBA UCEMA, 2026 2T.

**Integrantes:** Yanina Navarro, Eber Pires, Federico Serrizuela, Walter Perón.

## Qué construí

Un agente evaluador: un sistema que corrige el trabajo final de la materia aplicando una rúbrica
ejecutable, con una salida estructurada e idéntica en cada corrida. Incluye la rúbrica
(`rubrica.md`), el agente corrector (`agente/`), tres trabajos finales de ejemplo construidos por
el grupo para probarlo (`casos/excelente`, `casos/flojo`, `casos/tramposo`) y la evidencia de que
sus notas coinciden con el criterio del grupo (`calibracion.md`).

## Cómo se lo pedí

El proceso completo, con los pedidos reales en orden, está en `interacciones/registro.md`. En
resumen: se definió primero que el corrector y la rúbrica tenían que ser **genéricos** (aplicables
a cualquier caso de negocio que un compañero elija para su trabajo final, no a uno fijo), se
escribió la rúbrica con niveles de puntaje **fijos** (no rangos) para que fuera determinística, se
escribió el system prompt del corrector con un formato de salida obligatorio, se construyeron tres
trabajos finales ficticios sobre el mismo caso (un agente de triage de consultas de clientes) para
poder comparar calidad de construcción y no calidad de idea, y se corrió el corrector de verdad
sobre los tres para calibrarlo contra el criterio del grupo.

## Qué funciona

- La rúbrica aplica 5 dimensiones con 4 niveles cada una, evidencia exigida por nivel y una regla
  anti-trampa explícita.
- El agente corrector, siguiendo `agente/system_prompt.md` al pie de la letra, distingue
  correctamente los 3 casos: excelente 100/100, flojo 28/100, tramposo 28/100 **con** la alerta
  obligatoria `⚠️ Posible caso de trabajo tramposo detectado` que el caso flojo no dispara (ver
  `calibracion.md`).
- La calibración encontró dos desacuerdos reales entre el primer puntaje del agente y el criterio
  del grupo, y ambos quedaron resueltos con un ajuste concreto en la rúbrica o en el corrector,
  no con una segunda opinión improvisada.

## Qué falta o qué falló

El puntaje total de `flojo` y `tramposo` terminó siendo el mismo número (28/100) pese a ser
situaciones muy distintas — lo resolvimos haciendo que la detección de trampa quede en el informe
(la alerta obligatoria) y no en el número, pero es una limitación real: si alguien solo mira el
puntaje total sin leer el informe completo, no va a notar la diferencia. Además, el corrector no
fue probado todavía contra un trabajo final real de un compañero (fuera de los 3 casos que
construimos nosotros mismos) — eso recién va a pasar en la prueba de fuego.

## Qué aprendí

Que una rúbrica "ejecutable" no es solo escribir niveles y puntajes — hay que aplicarla de verdad
para encontrarle los huecos, porque en el papel una rúbrica puede sonar completa y recién al
correrla aparece la ambigüedad (como el caso de dos huecos de formato combinados). También que
detectar una trampa no es lo mismo que ponerle una nota baja: un trabajo tramposo y uno
simplemente flojo pueden terminar con el mismo puntaje, y si el sistema no lo señala de forma
explícita, la trampa pasa desapercibida igual.
