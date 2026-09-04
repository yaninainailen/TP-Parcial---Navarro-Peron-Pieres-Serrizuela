# El agente evaluador

Parcial de la materia "Creación de Agentes de IA" — MADE N-2T, UCEMA, 2026 2T.

**Integrantes:**

* Navarro, Yanina
* Peron, Walter
* Pires, Eber
* Serrizuela, Federico

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
- Un stress test adicional ("caso medio tramposo": 4 de 5 dimensiones reales y excelentes, una
  sola con un número económico que no cierra) encontró que la regla de alerta original (2+
  dimensiones afectadas) dejaba pasar sin aviso una mentira aislada en una sola dimensión — daba
  85/100 sin ninguna señal. Se ajustó la regla para que también dispare con una sola dimensión
  penalizada específicamente por la regla anti-trampa del número inventado (ver `calibracion.md`).
- El corrector también se corrió, sin cambiar nada de `agente/`, contra 3 repos reales ajenos al
  grupo (de la Entrega 1 de la materia, no trabajos finales): no se rompió, devolvió el formato
  esperado, y esa prueba expuso una ambigüedad real en `rubrica.md` ("`DECISIONES.md` (o
  equivalente)") que quedó resuelta de forma explícita citando la consigna del trabajo final.

## Cómo correr esto en la prueba de fuego (10/9)

La herramienta es `agente/sonda_v0.2.html`, con tres caminos elegibles paso a paso en la misma
página (detalle completo en `agente/config.md`):

1. **Un repo con API de Claude:** pegar la URL del repo, el nombre del creador, la API key de
   Anthropic, el Workspace ID y elegir el modelo (por defecto Claude Opus 5; hay opciones más
   baratas — Sonnet 5, Haiku 4.5 — en el mismo selector), y apretar "Analizar el repo". La página
   llama directo a la API, muestra la corrección junto con el uso de tokens y el costo estimado de
   esa corrida, y la guarda **automáticamente** en `casos/<nivel>/<Creador del repo>/` (informe +
   copia de `corridas/`, `prompts/` y los 4 archivos de proceso del repo evaluado). Conviene tener
   la key y el Workspace ID ya cargados y probados *antes* de que arranque la prueba, no en el
   momento.
2. **Un repo sin API ("Prompt Validador"):** sin key, pegar la URL, apretar "Crear Prompt
   Validador", copiar el resultado y pegarlo en un chat de IA ya abierto de antemano (Claude o
   ChatGPT) — conviene tener esa pestaña lista de antes. No guarda nada en disco.
3. **Una lista de repos por CSV:** el mismo camino con API pero subiendo un CSV (`url,creador` por
   fila) para procesar muchos repos uno por uno.

Como el guardado automático en disco (camino 1 y 3) depende de una API del navegador que no
funciona abriendo el archivo con doble clic, hay que servir la página por `http://localhost` (ej.
`python -m http.server` desde la raíz del repo, y abrirla como
`http://localhost:8000/agente/sonda_v0.2.html`) y usar Chrome o Edge. El camino 2 (sin API, sin
guardado) sí funciona abriendo el archivo directo, en cualquier navegador.

Si la API de GitHub responde con error de rate-limit (límite de pedidos sin autenticar), esperar
unos minutos y reintentar, o probar desde otra red — no hay token de GitHub configurado a
propósito (decisión documentada en `interacciones/registro.md`), así que no hay forma de saltear
ese límite en el momento. Si en cambio falla la llamada a la API de Anthropic (key inválida, sin
crédito, rate-limit de la cuenta), el mensaje de error queda a la vista arriba del resultado — el
camino 2 (sin key) sigue funcionando como respaldo.

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
