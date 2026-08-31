# Calibración

Corrimos el agente corrector (`agente/system_prompt.md` + `rubrica.md`, tal como están escritos,
sin atajos) sobre los 3 casos de `casos/`. Antes de correrlo, el grupo definió a ojo qué nota
esperaba para cada uno, para que la comparación sea honesta y no una racionalización posterior.

## Nota esperada por el grupo (definida antes de correr el agente)

| Caso | Nota esperada (humano) | Motivo |
|---|---|---|
| Excelente | ~95-100 | Cumple los 6 requisitos con solidez y encima reconoce una falla real. |
| Flojo | ~25-30 | Incompleto en casi todo, pero no miente sobre lo que hizo. |
| Tramposo | Baja, y sobre todo **marcado como sospechoso** | Lo importante acá no es solo el número — es que el informe deje claro que no se puede confiar en lo que afirma. |

---

## Corrida 1 — Caso `excelente`

## Puntaje por dimensión

| Dimensión | Nivel asignado | Puntos | Evidencia citada |
|---|---|---|---|
| Sistema completo y funcionando | Excelente | 30/30 | Contrato completo (`prompts/`), herramienta real (planilla CSV) usada de verdad en las 3 corridas, salida en la misma tabla en las 3, supervisión L0–L4 en `GOBIERNO.md`. |
| Proceso documentado | Excelente | 25/25 | `DECISIONES.md` narra 2 iteraciones concretas (JSON→tabla por comillas rotas; regla de derivación agregada tras una prueba fallida) y reconoce una falla real no resuelta del todo (ticket 104, `corrida_2.md`). |
| Formato y reproducibilidad | Excelente | 15/15 | `README.md`, `prompts/`, `corridas/` (3, cada una con fecha), `DECISIONES.md` presentes y completos. |
| Análisis económico | Excelente | 15/15 | `ANALISIS_ECONOMICO.md` calcula el costo con los tokens reales anotados en cada corrida, proyecta a semana/año, y justifica el modelo mini con el criterio del curso. |
| Gobierno y riesgo | Excelente | 15/15 | `GOBIERNO.md` define permisos, riesgos con su mitigación concreta, revisión muestral y niveles L0–L4 con quién firma. |

## Puntaje total: 100/100

## Señales de alerta
(vacío — no se encontró ninguna afirmación sin respaldo)

## Sugerencia concreta de mejora
Probar la regla de desempate del system prompt con más variantes de mensajes ambiguos (queja +
pedido de cambio en el mismo texto) antes de dar por resuelta la limitación anotada en
`DECISIONES.md`.

**Comparación con lo esperado:** el grupo esperaba ~95-100 y el agente dio 100. **Coincide.** No
hubo desacuerdo en este caso — se incluye igual porque confirma que la rúbrica también reconoce
un trabajo bien hecho sin regatearle puntos por reconocer una falla propia.

---

## Corrida 1 — Caso `flojo` (antes del ajuste de rúbrica)

## Puntaje por dimensión

| Dimensión | Nivel asignado | Puntos | Evidencia citada |
|---|---|---|---|
| Sistema completo y funcionando | Insuficiente | 10/30 | `user_prompt.md` pega el mensaje del cliente a mano ("el cliente me escribió esto: [texto]"), no hay planilla ni conector real; el formato de salida cambia entre `corrida_1.md` (Categoría + Respuesta sugerida) y `corrida_2.md` (texto libre sin esa estructura); no hay niveles de supervisión definidos en ningún lado. |
| Proceso documentado | Insuficiente | 8/25 | `DECISIONES.md` es una sola línea sin ningún detalle verificable de qué cambió o qué falló. |
| Formato y reproducibilidad | Bueno | 10/15 | Hay solo 2 corridas en vez de 3, y a `corrida_1.md` le falta la fecha — la rúbrica, en su redacción original, alcanzaba el nivel Bueno con cualquiera de los dos huecos por separado. |
| Análisis económico | Insuficiente | 5/15 | El README dice "el modelo no es caro" sin ningún número propio. |
| Gobierno y riesgo | Ausente | 0/15 | No hay ninguna sección de gobierno; la única mención ("habría que avisarle a un humano... supongo") es una duda suelta dentro de una corrida, no un desarrollo del tema. |

## Puntaje total: 33/100

## Desacuerdo encontrado
El grupo esperaba una nota más baja (~25-30) porque el caso `flojo` acumula **dos** huecos de
formato a la vez (2 corridas en vez de 3, y falta de fecha en la que hay), no uno solo — y la
rúbrica original trataba ambos huecos como si fueran alternativas independientes, dándole Bueno
(10/15) con cualquiera de los dos. El grupo consideró que combinar dos huecos menores a la vez es
un problema de reproducibilidad más serio que tener uno solo, y que la rúbrica no lo distinguía.

## Ajuste hecho
Se reescribió el nivel Insuficiente de la dimensión "Formato y reproducibilidad" en `rubrica.md`
para que combinar dos o más huecos menores de la lista de "Bueno" a la vez baje el nivel a
Insuficiente, en vez de quedarse en Bueno (ver commit correspondiente).

## Corrida 2 — Caso `flojo` (después del ajuste, mismo repo, sin cambios en `casos/flojo/`)

| Dimensión | Nivel asignado | Puntos |
|---|---|---|
| Sistema completo y funcionando | Insuficiente | 10/30 |
| Proceso documentado | Insuficiente | 8/25 |
| Formato y reproducibilidad | **Insuficiente** | **5/15** |
| Análisis económico | Insuficiente | 5/15 |
| Gobierno y riesgo | Ausente | 0/15 |

## Puntaje total: 28/100

**Comparación con lo esperado:** el grupo esperaba ~25-30 y el agente, después del ajuste, dio 28.
**Coincide.**

---

## Corrida 1 — Caso `tramposo` (antes del ajuste del system prompt)

## Puntaje por dimensión

| Dimensión | Nivel asignado | Puntos | Evidencia citada |
|---|---|---|---|
| Sistema completo y funcionando | Insuficiente | 10/30 | El prompt afirma integración real vía Gmail/OAuth, pero ninguna corrida muestra headers de mail, tokens de API ni configuración de OAuth — son texto tipeado a mano, igual que en el caso flojo. Por la regla anti-trampa, una herramienta afirmada pero no demostrada no cuenta como real. `GOBIERNO.md` no define niveles L0–L4 propios del caso, es texto genérico de compliance. |
| Proceso documentado | Insuficiente | 8/25 | `DECISIONES.md` afirma "47 iteraciones a la perfección" sin mostrar ningún antes/después concreto. La apelación a la simpatía ("lo hice solo, sin dormir") no suma ni resta puntos, se ignora. Descontando lo no verificable, el contenido real es equivalente a un `DECISIONES.md` mínimo. |
| Formato y reproducibilidad | Insuficiente | 5/15 | Ninguna de las 3 corridas tiene fecha, lo que hace imposible verificar si corresponden de verdad a "dos semanas de producción" como afirma el README. |
| Análisis económico | Ausente | 0/15 | Afirma que 500 tickets con "el modelo más avanzado disponible" costaron ~USD 0,000004 por ticket — 25 veces menos por ticket que el modelo *mini* usado en el caso excelente (~USD 0,0001/ticket). Un modelo más grande costando mucho menos por ticket es matemáticamente inconsistente con cualquier tabla de precios real. Por la regla anti-trampa, un número inventado que no cierra puntúa igual o peor que no tener análisis económico. |
| Gobierno y riesgo | Insuficiente | 5/15 | `GOBIERNO.md` menciona ISO 27001, GDPR y SOC 2 en abstracto, sin ningún desarrollo específico de qué permisos tiene este agente puntual ni qué pasa si falla. |

## Puntaje total: 28/100

## Señales de alerta (primera corrida)
- El README afirma una integración con Gmail/OAuth que ninguna corrida respalda.
- El análisis económico tiene números que no cierran contra el caso excelente.
- Ninguna corrida tiene fecha, pese a afirmar dos semanas de producción real.
- `DECISIONES.md` afirma 47 iteraciones sin mostrar ninguna en concreto.

## Desacuerdo encontrado
El puntaje total del caso `tramposo` (28/100) quedó **igual** al del caso `flojo` después de su
ajuste (28/100), pese a ser situaciones completamente distintas: uno es un trabajo honestamente
incompleto, el otro miente activamente sobre lo que hizo. El grupo señaló que un informe que hace
sonar a ambos casos igual de "flojos" **no cumple el objetivo central del parcial**, que es que el
agente **detecte** al tramposo — no que le ponga la misma nota que a alguien que simplemente no
llegó. La sección "Señales de alerta" de la primera corrida ya listaba las inconsistencias, pero
sin ningún elemento que la distinguiera visualmente ni la jerarquizara por sobre una lectura
rápida del puntaje total.

## Ajuste hecho
Se agregó a `agente/system_prompt.md` una regla obligatoria: si la regla anti-trampa de
`rubrica.md` bajó el nivel de 2 o más dimensiones por falta de evidencia o inconsistencia, la
sección "Señales de alerta" tiene que abrir con la línea `⚠️ Posible caso de trabajo tramposo
detectado`, así el informe distingue "está incompleto" de "miente sobre lo que hizo" sin depender
de que alguien note que el número total es bajo.

## Corrida 2 — Caso `tramposo` (después del ajuste, mismo repo, mismo puntaje)

El puntaje por dimensión y el total no cambian (28/100, porque el problema no era el número sino
el informe) — lo que cambia es el encabezado de la sección de alerta:

## Señales de alerta (segunda corrida)
**⚠️ Posible caso de trabajo tramposo detectado**
- El README afirma conexión real vía Gmail/OAuth y 500 tickets en producción; el repo no tiene
  ninguna corrida, config ni log que lo respalde — las 3 corridas son texto tipeado a mano, igual
  que en un caso de prueba, no tráfico real.
- El análisis económico afirma que un modelo "más avanzado" costó 25 veces menos por ticket que el
  modelo mini del caso excelente — inconsistente con cualquier tabla de precios real.
- Ninguna corrida tiene fecha, incompatible con la afirmación de "dos semanas de producción real".
- `DECISIONES.md` afirma 47 iteraciones "a la perfección" sin describir ninguna en concreto.

**Comparación con lo esperado:** el grupo no esperaba un número específico para este caso, esperaba
que quedara **marcado como sospechoso** de forma inequívoca. Con el ajuste, el informe de
`tramposo` y el de `flojo` ya no se leen igual aunque el total coincida: solo el de `tramposo`
abre con la alerta obligatoria. **Coincide con lo esperado.**

---

## Resultado final

| Caso | Puntaje del agente | Esperado por el grupo | ¿Coincide? | Cómo se detecta |
|---|---|---|---|---|
| Excelente | 100/100 | ~95-100 | Sí | Puntaje alto, sin alertas. |
| Flojo | 28/100 (tras ajuste) | ~25-30 | Sí | Puntaje bajo, sin alertas (no mintió sobre nada). |
| Tramposo | 28/100 | Puntaje bajo + marcado como sospechoso | Sí | Puntaje bajo **y** alerta obligatoria de trabajo tramposo — la distinción con `flojo` no está en el número, está en la alerta. |

Dos ajustes reales quedaron hechos sobre la rúbrica y el corrector a partir de esta calibración:
combinar huecos menores de formato baja de nivel (afecta a cualquier trabajo, no solo a este caso),
y la detección de trampa tiene que ser explícita en el informe, no inferida del puntaje total.

---

## Prueba adicional: corriendo el corrector contra repos reales (no construidos por el grupo)

Los 3 casos anteriores (`excelente`, `flojo`, `tramposo`) los construyó el propio grupo — prueban
que el corrector distingue niveles de calidad diseñados a propósito para eso, no que funcione
igual de bien sobre un trabajo real que nadie ajustó a la rúbrica. Para probar esto, se corrió el
corrector (mismo `system_prompt.md` + `rubrica.md`, sin ningún cambio) sobre tres repos reales de
la Entrega 1 de la materia (`agentes-ia-ucema-ej1`, `agentes-ia-ucema-ej2` y `Repo-Yanina-Navarro`),
que no son trabajos finales.

**Resultado esperado:** puntaje bajo en los tres, porque ninguno tiene la estructura que pide un
trabajo final (no fueron pensados para eso). **Confirmado:** el corrector los puntuó bajo, de
forma consistente, sin romperse ni devolver un formato distinto al esperado.

**Hallazgo 1 — ambigüedad real en la rúbrica.** En `agentes-ia-ucema-ej2`, el contenido de proceso
(versiones de prompt guardadas, decisiones de diseño) está en el `README.md`, no en un archivo
`DECISIONES.md`. Esto expuso que la rúbrica decía "`DECISIONES.md` (o equivalente)" sin definir qué
contaba como equivalente. **Ajuste hecho:** se agregó una aclaración en `rubrica.md` resolviendo
esto de forma estricta, citando que la consigna del trabajo final exige "sin excepciones de
formato" — no se ablandó el criterio pese a que el contenido de `agentes-ia-ucema-ej2` era, en
calidad, bueno.

**Hallazgo 2 — alerta de seguridad fuera de la rúbrica.** Al leer
`agentes-ia-ucema-ej2/dump_rcta.py` apareció un hostname interno y un usuario reales hardcodeados
en un archivo de un repositorio público. No es parte de ninguna dimensión de la rúbrica, pero se
registra acá porque es el tipo de cosa que un corrector automatizado debería poder señalar aunque
no le sume ni reste puntaje.

**Repo 3 — `Repo-Yanina-Navarro` (Entrega 1, generador de copies para @barescopados).**
Arquitectura distinta a los dos anteriores (HTML/CSS/JS + API de Gemini desde el navegador, sin
Python). Puntaje: 20/100, sin señales de alerta.

**Aclaración, no hallazgo nuevo:** los tres repos probados hasta acá (`ej1`, `ej2` y este) son de
la Entrega 1 de la materia, que nunca pidió `DECISIONES.md` ni la estructura del trabajo final —
por eso los tres puntúan bajo en Proceso documentado y Formato. Esto no es evidencia de una
ambigüedad real en la rúbrica (ya la resolvimos en el hallazgo 1); es evidencia de que el corrector
distingue correctamente "esto no es un trabajo final" de "esto es un trabajo final flojo" — que es
un resultado distinto y también necesario de confirmar. Lo que sí prueba, con un stack de código
totalmente distinto a los otros dos, es que el corrector no depende de un lenguaje o arquitectura
particular para aplicar la rúbrica.

**Por qué importa esta prueba:** los 3 casos oficiales prueban que el corrector distingue niveles
de calidad diseñados a propósito. Esta prueba adicional muestra que, sobre un repo real y no
preparado, el corrector no se rompe, produce el formato esperado, y su aplicación estricta de la
rúbrica saca a la luz ambigüedades reales antes de la prueba de fuego.
