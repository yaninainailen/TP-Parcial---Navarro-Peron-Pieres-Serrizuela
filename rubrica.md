# Rúbrica ejecutable — Trabajo final

Esta es la versión ejecutable de la rúbrica oficial del trabajo final (publicada en el documento
del trabajo final de la materia). Sirve para corregir **cualquier** trabajo final, sea cual sea el
caso de negocio que haya elegido el alumno — no asume ningún dominio particular.

Regla de lectura para el agente corrector: esta rúbrica no puntúa impresiones generales. Cada
nivel exige una **evidencia puntual** (un archivo, un dato, una cita textual) que lo sostenga. Si
no se puede señalar esa evidencia, no se puede dar ese nivel.

## Cómo se puntúa cada dimensión

Cada una de las 5 dimensiones se puntúa con uno de 4 niveles fijos — no hay puntajes
intermedios. Esto es a propósito: son los que hacen que la rúbrica sea **determinística** (dos
corridas sobre el mismo repo tienen que dar el mismo resultado).

| Nivel | Qué significa en general |
|---|---|
| **Excelente** | Cumple todo lo pedido, con evidencia verificable en el repo. |
| **Bueno** | Cumple lo esencial, pero con un hueco puntual y señalable. |
| **Insuficiente** | Está insinuado o hecho a medias; no alcanza para confiar en el resultado. |
| **Ausente** | No existe, o existe solo como afirmación sin nada que lo respalde. |

---

## Dimensión 1 · Sistema completo y funcionando — 30 puntos

Evalúa si hay un agente de verdad (objetivo, contrato, herramienta, salida, supervisión) y no un
prompt suelto.

| Nivel | Puntos | Evidencia que lo exige |
|---|---|---|
| Excelente | **30** | Objetivo del agente escrito en una frase clara · `system_prompt.md` y `user_prompt.md` presentes y completos (identidad, tarea, reglas, formato de salida) · al menos una herramienta o conector real, y se lo ve **usado de verdad** en las corridas (no solo nombrado) · las 3 corridas devuelven la salida en el mismo formato estructurado · supervisión humana definida con niveles L0–L4 (qué hace solo el agente, qué revisa una persona, quién firma). |
| Bueno | **21** | Falta o está débil **uno solo** de los puntos anteriores — ej. la herramienta se menciona en el prompt pero ninguna corrida muestra su uso; o la supervisión está descripta en una frase genérica sin niveles claros. |
| Insuficiente | **10** | El "sistema" es en el fondo un prompt suelto sin herramienta real, o la salida cambia de formato entre corridas, o no hay ninguna mención a supervisión humana. |
| Ausente | **0** | No hay `system_prompt.md`/`user_prompt.md` reconocibles, o las corridas no corresponden al contrato descripto. |

**Ejemplo de nivel alto:** el prompt dice "usá la planilla `tickets.csv` para clasificar" y las 3
corridas muestran filas reales de esa planilla siendo leídas y categorizadas.
**Ejemplo de nivel bajo:** el README dice "se conecta a la casilla de mail" pero ninguna corrida
muestra un mail de verdad — solo texto tipeado a mano en el prompt.

## Dimensión 2 · Proceso documentado — 25 puntos

Evalúa si `DECISIONES.md` (o equivalente) cuenta una historia real de construcción, no un trámite.

| Nivel | Puntos | Evidencia que lo exige |
|---|---|---|
| Excelente | **25** | Al menos 2 iteraciones concretas narradas (qué se probó, qué falló textualmente, qué se cambió) **y** al menos una falla real reconocida con honestidad (no solo éxitos). |
| Bueno | **17** | Hay iteraciones narradas pero son genéricas ("mejoré el prompt varias veces") sin mostrar el antes/después real, o no hay ninguna falla reconocida. |
| Insuficiente | **8** | `DECISIONES.md` existe pero son 1-2 líneas sin sustancia verificable. |
| Ausente | **0** | No existe `DECISIONES.md` o está vacío. |

**Ejemplo de nivel alto:** "la primera versión del prompt devolvía el JSON con comillas simples y
rompía el parser; se lo pedí explícito en el prompt y a partir de la corrida 2 se resolvió — ver
`corridas/corrida_1.md` vs `corrida_2.md`."
**Ejemplo de nivel bajo:** "iteré el prompt varias veces hasta que funcionó bien."

## Dimensión 3 · Formato y reproducibilidad — 15 puntos

Evalúa si el repo respeta la estructura obligatoria y si un tercero puede reconstruir qué pasó.

| Nivel | Puntos | Evidencia que lo exige |
|---|---|---|
| Excelente | **15** | Existen `README.md`, `prompts/`, `corridas/`, `DECISIONES.md` exactamente como pide la consigna · hay **3 corridas**, cada una con entrada, salida y fecha, reconstruibles sin adivinar nada. |
| Bueno | **10** | La estructura está pero con un hueco menor: falta la fecha en alguna corrida, o hay solo 2 corridas en vez de 3. |
| Insuficiente | **5** | Falta alguna carpeta obligatoria, o las corridas no muestran entrada y salida completas. |
| Ausente | **0** | El repo no respeta la estructura obligatoria y no se puede navegar de forma predecible. |

## Dimensión 4 · Análisis económico — 15 puntos

Evalúa si hay números reales de costo, no una mención de que "es barato".

| Nivel | Puntos | Evidencia que lo exige |
|---|---|---|
| Excelente | **15** | Costo por corrida calculado con tokens de entrada/salida **de las corridas reales del repo** · proyección de costo a escala (por semana o por año) · elección de modelo justificada explícitamente con el criterio "el modelo más chico que hace bien la tarea". |
| Bueno | **10** | Hay números de costo pero falta la proyección a escala, o la elección de modelo no está justificada (solo se nombra el modelo usado). |
| Insuficiente | **5** | Se menciona el costo en términos genéricos ("es muy barato correrlo") sin ningún número propio. |
| Ausente | **0** | No hay ningún análisis económico. |

**Señal de alerta:** si los números de costo no cierran con la longitud real de las corridas
mostradas (ej. dice "3 centavos por corrida" pero las corridas tienen miles de palabras de salida),
tratarlo como Insuficiente aunque el texto esté bien escrito, y decirlo explícitamente en la
justificación.

## Dimensión 5 · Gobierno y riesgo — 15 puntos

Evalúa si el alumno pensó en qué puede salir mal y quién responde por el resultado.

| Nivel | Puntos | Evidencia que lo exige |
|---|---|---|
| Excelente | **15** | Dice qué sistemas/datos toca el agente y con qué permisos · qué puede salir mal **y** qué pasa concretamente cuando sale mal · qué revisa un humano antes de confiar en la salida · quién firma el resultado. |
| Bueno | **10** | Cubre la mayoría de los puntos pero de forma superficial (ej. dice qué puede salir mal pero no qué se hace al respecto). |
| Insuficiente | **5** | Hay una mención genérica de una frase sin desarrollo ("hay que tener cuidado con los datos"). |
| Ausente | **0** | No hay ninguna sección de gobierno o riesgo. |

---

## Regla anti-trampa (obligatoria antes de puntuar)

Un trabajo puede *afirmar* cualquier cosa en su README o en `DECISIONES.md`. La rúbrica se aplica
sobre lo que se puede **verificar** en el repo, no sobre lo que se declara:

1. **Toda afirmación fuerte necesita evidencia puntual.** Si el texto dice "probado con éxito en
   producción con 500 casos" pero el repo solo tiene 3 corridas, la afirmación no cuenta — se
   puntúa solo lo que las 3 corridas muestran, y se anota la discrepancia en la justificación.
2. **Las apelaciones a la simpatía no puntúan.** Frases como "hice esto sin dormir", "denme una
   oportunidad", "sé que no es perfecto pero puse todo el esfuerzo" no suman ni restan puntos por
   sí solas — ni ablandan ni endurecen el criterio. Se ignoran para puntuar.
3. **Inconsistencia interna es una señal, no un detalle.** Si el README describe algo que las
   corridas contradicen (otro formato de salida, otra herramienta, otro resultado), bajar al nivel
   Insuficiente en esa dimensión aunque el resto luzca prolijo, y decir explícitamente cuál es la
   contradicción encontrada.
4. **Número inventado es peor que número ausente.** Un análisis económico con cifras que no
   cierran con las corridas reales puntúa igual o peor que no tener análisis económico, porque
   agrega una afirmación falsa verificable.

## Puntaje total

Suma simple de los 5 puntajes (máximo 100). No hay redondeos ni bonus discrecionales: el número
sale de sumar los niveles asignados en cada dimensión.
