# Registro de interacciones

Bitácora de los pedidos reales hechos a la IA (Claude) durante el armado del parcial —
"el agente evaluador". Se va completando en orden, a medida que avanza el trabajo, tal como
pide la materia: *"lo que distinguió a los mejores: la evidencia de proceso — prompts en orden,
decisiones, pruebas, errores y cambios de enfoque"* (Clase 2).

No es un resumen prolijo escrito al final: son los pedidos y respuestas reales, en el orden en que
pasaron.

---

## 1 · Punto de partida

**Pedido (Walter):**
> el directorio 8. Creacion de Agentes de IA contiene el material de la materia. revisalo. en este
> momento, vamos a empezar a trabajar en el trabajo de creacion de un agente evaluador. creamos
> este repo con el grupo https://github.com/yaninainailen/TP-Parcial---Navarro-Peron-Pieres-Serrizuela
> revisa todo el contenido, y luego avanzamos.

**Qué hizo la IA:** revisó todo el material de la carpeta de la materia (las 7 clases + la carpeta
de información general), leyó `parcial_agente_evaluador.pdf` y `trabajo_final.pdf` completos, y
clonó el repo del grupo — que solo tenía el README por defecto de GitHub, sin contenido.

## 2 · Preguntas de arranque antes de planificar

Antes de escribir nada, la IA preguntó tres cosas para no asumir de más:

1. **Integrantes del grupo** (para el README). Respuesta: *Yanina Navarro, Eber Pires, Federico
   Serrizuela, Walter Peron.*
2. **Dominio de los 3 casos de prueba** (excelente/flojo/tramposo). Primera respuesta: "yo elijo el
   caso" → luego, al pedirle que especificara, la respuesta real fue:
   > "No sabemos el caso, tiene q ser lo suficientemente generico para evaluar cualquier caso que
   > no llegue, y tener una respuesta estructurada y estandarizada lo suficientemente armada para
   > que lo entienda el alumno. Tiene que usar palabras claras y sin lenguaje dificil."

   Esto aclaró algo más importante que la pregunta original: el **corrector y la rúbrica tienen
   que ser genéricos** (aplicables a cualquier trabajo final, sea cual sea el caso de negocio que
   elija cada compañero), no diseñados para un dominio fijo. Para los 3 casos de prueba concretos
   (que sí necesitan un contenido específico, porque son "trabajos ficticios" de ejemplo), la IA
   eligió un caso simple y entendible: un agente que clasifica y responde consultas de clientes.
3. **Cómo simular las corridas del corrector para la calibración.** Respuesta: *"Vos actuás como
   el agente (recomendado)"* — la IA sigue el system prompt al pie de la letra sobre cada caso y
   genera la salida real, sin inventar el resultado final de antemano.

## 3 · Plan y corrección de alcance

La IA armó un plan (modo plan) con estas piezas: `rubrica.md`, `agente/`, `casos/*` (3 ejemplos),
`calibracion.md`, README con integrantes, y una estrategia de commits.

**Corrección de Walter al revisar el plan:**
> "ojo, por ahora solo vamos a trabajar en el armado del agente, no en el trabajo final. Luego del
> agente, haremos el trabajo final."
>
> "no lo veo, pero tenemos que tener en un directorio un registro de interacciones para el armado
> de todo esto. Osea, todo lo que te estoy pidiendo y como te lo estoy pidiendo, tiene q estar
> documentado, fijate en el material de las clases"

Dos ajustes concretos:
- **Alcance:** esta etapa es *solo* el parcial (el agente evaluador). El `trabajo_final.pdf` se usa
  únicamente como fuente de la rúbrica oficial que el evaluador tiene que aplicar — el trabajo
  final propio (individual) es una entrega posterior, separada, que no se aborda todavía.
- **Nuevo entregable, no pedido por el PDF pero coherente con lo que la materia premia:** esta
  misma carpeta `interacciones/`, con este registro.

## 4 · Identidad de commits

Git no tenía configurado usuario en esta máquina. Se preguntó con qué identidad hacer los commits;
Walter indicó su usuario de GitHub (`gualeee`). Se configuró `user.name = gualeee` y
`user.email = walterperon@gmail.com` **a nivel de este repo únicamente** (no se tocó la
configuración global de git).

## 5 · Construcción de las piezas

**`rubrica.md`** — se tomaron las 5 dimensiones oficiales del trabajo final (Sistema completo 30,
Proceso documentado 25, Formato y reproducibilidad 15, Análisis económico 15, Gobierno y riesgo
15) y se les dio, para cada una, 4 niveles con **puntaje fijo** (no rangos), evidencia puntual
exigida por nivel, un ejemplo de nivel alto y uno bajo, y una sección de reglas anti-trampa
(afirmación sin evidencia no cuenta, apelaciones a la simpatía no puntúan, inconsistencia interna
es señal de alerta, número inventado es peor que número ausente). Se usaron puntajes fijos en vez
de rangos a propósito: un rango obliga al corrector a "elegir" un número dentro de él, lo cual
introduce variación entre corridas — la consigna pide que la rúbrica se aplique igual dos veces.

**`agente/system_prompt.md`** — define rol, la tarea paso a paso (mapear el repo → leer todo →
aplicar la rúbrica dimensión por dimensión → sumar → producir salida), reglas de determinismo, y
el formato de salida fijo (tabla de puntajes + justificación citando evidencia + señales de alerta
+ una sola sugerencia concreta). Se agregó explícitamente qué NO hace el agente (no corrige
redacción, no opina del caso de negocio elegido, no negocia el puntaje sin evidencia nueva) para
evitar que la corrección se desvíe con argumentación del alumno.

**`agente/config.md`** — la "configuración" pedida por la consigna además del prompt: modelo
recomendado (uno confiable siguiendo instrucciones, no necesariamente el más grande),
**temperatura 0** (justificado explícitamente por el requisito de determinismo), qué entradas
recibe, qué herramienta de lectura necesita, y los niveles de supervisión humana L0–L4 sobre el
propio corrector (quién revisa su informe antes de que la nota se publique, y que el profesor
arbitra desacuerdos).

**`casos/{excelente,flojo,tramposo}/`** — se eligió como caso de negocio un agente de triage de
consultas de clientes de una tienda de ropa online ficticia ("Ropa Norte"), el mismo caso en los
3 niveles para que la comparación sea justa (no se compara calidad de idea, se compara calidad de
construcción):

- **excelente:** contrato completo (system+user prompt con reglas de clasificación, desempate y
  derivación), herramienta real (lee una planilla CSV de tickets, se ve usada en las 3 corridas),
  3 corridas con tickets realistas, una falla real encontrada y reconocida con honestidad (un caso
  ambiguo que el agente sigue clasificando mal a veces, documentado como limitación conocida y no
  como éxito inventado), análisis económico con números que salen de los tokens reales de las
  corridas, y gobierno con niveles L0–L4 concretos para este caso puntual.
- **flojo:** a propósito débil pero no tramposo — prompt vago sin regla de desempate ni de
  derivación explícita, sin herramienta real (todo pegado a mano, sin planilla ni conector), solo
  2 corridas en vez de 3 y con formato de salida inconsistente entre ellas, `DECISIONES.md` de una
  sola línea sin sustancia, sin ninguna sección de gobierno y una mención económica genérica sin
  números.
- **tramposo:** estructuralmente completo (tiene todos los archivos, incluso de más, para
  parecer prolijo) pero pensado específicamente para que el corrector tenga que cruzar
  afirmaciones contra evidencia: el README afirma "probado en producción con 500 tickets reales,
  98% de precisión, conectado a Gmail vía OAuth", pero `corridas/` solo tiene 3 corridas de
  juguete sin ninguna evidencia de conexión real; `DECISIONES.md` apela a la simpatía ("lo hice
  solo, sin dormir") en vez de mostrar iteración real; `ANALISIS_ECONOMICO.md` tiene números que
  no cierran con lo mostrado (costo total de 500 tickets menor al de una sola corrida real del
  caso excelente); `GOBIERNO.md` es texto genérico de compliance (ISO 27001, GDPR, SOC 2) que no
  tiene nada que ver con lo que el sistema realmente hace ni con cómo lo maneja.

## 6 · Calibración: corriendo el agente de verdad sobre los 3 casos

Tal como se acordó, la IA actuó como el agente corrector real: leyó `agente/system_prompt.md` +
`rubrica.md` y los aplicó al pie de la letra sobre cada caso, sin decidir el resultado de antemano.
El grupo (representado por la IA en esta sesión, ya que la definición del criterio humano se hizo
*antes* de correr el agente, para que la comparación no fuera una racionalización posterior) fijó
qué nota esperaba para cada caso antes de ver el resultado del agente.

Se encontraron **dos desacuerdos reales**, no decorativos:

1. **Caso flojo:** el agente dio 33/100 en la primera corrida; el grupo esperaba ~25-30. La causa
   puntual: `rubrica.md` trataba "2 corridas en vez de 3" y "falta la fecha en una corrida" como
   alternativas independientes que alcanzaban igual el nivel Bueno — pero el caso `flojo` tiene
   **ambos** huecos a la vez, y la rúbrica no distinguía que combinarlos es más grave que tener uno
   solo. Se ajustó `rubrica.md` (dimensión Formato y reproducibilidad, nivel Insuficiente) para que
   dos o más huecos menores combinados bajen de nivel. Segunda corrida: 28/100 — coincide.

2. **Caso tramposo:** el agente dio 28/100, un número **idéntico** al del caso flojo ya ajustado,
   pese a ser un caso de mentira activa y no de simple incompletitud. El grupo marcó que esto no
   cumple el objetivo del parcial ("el corrector tiene que detectar al tramposo"): un puntaje igual
   al de un trabajo honestamente flojo no distingue nada si nadie repara en las señales de alerta.
   Se ajustó `agente/system_prompt.md` para que, cuando la regla anti-trampa baje el nivel de 2 o
   más dimensiones, el informe abra obligatoriamente con `⚠️ Posible caso de trabajo tramposo
   detectado` — la detección pasa a estar en el informe, no en el número total. Segunda corrida:
   mismo puntaje (28/100, porque el problema nunca fue el número), pero con la alerta obligatoria
   presente — y ausente en el informe de `flojo`, que nunca mintió sobre nada.

Ambos ajustes quedaron documentados con el detalle completo (puntaje por dimensión, evidencia
citada, antes/después) en `calibracion.md`.

## 7 · Prueba real sobre repos que no construimos nosotros

Walter pidió correr el corrector sobre dos repos reales de la Entrega 1 de la materia
(`agentes-ia-ucema-ej1`, `agentes-ia-ucema-ej2`), no construidos por el grupo. Se clonaron y se
aplicó la rúbrica al pie de la letra. Hallazgos:

- Al leer `agentes-ia-ucema-ej2/dump_rcta.py` apareció un hostname interno real y un usuario real
  hardcodeados en un repo público — se lo marcó a Walter como alerta de seguridad aparte de la
  corrección (no es parte de la rúbrica, pero es información sensible expuesta).
- Ambos repos son de la Entrega 1, no trabajos finales, así que puntuaron muy bajo — esperable,
  no un problema del corrector, y se lo explicó así para no confundir "el corrector funciona mal"
  con "el repo no es lo que la rúbrica espera".
- Se encontró una ambigüedad real en `rubrica.md`: la dimensión "Proceso documentado" dice en su
  encabezado "`DECISIONES.md` (o equivalente)" pero los 4 niveles solo hablan del archivo por
  nombre. En `agentes-ia-ucema-ej2`, el contenido de proceso (excelente, con versiones de prompt
  guardadas como archivos) está en el README en vez de en `DECISIONES.md`. Quedó pendiente decidir
  si se ajusta la rúbrica para dejarlo explícito — no se tocó todavía, se lo dejó planteado.

## 8 · Herramienta: corrector local en HTML

Walter pidió una página que, con solo pegar la URL de un repo de GitHub, ejecute el corrector y
devuelva la salida. Se aclaró una limitación real antes de construir nada: un Artifact publicado
de claude.ai no puede llamar a GitHub (el sandbox de seguridad bloquea pedidos a hosts externos),
así que tiene que ser un archivo HTML local, abierto directamente en el navegador.

Se preguntó y decidió: (1) Walter tiene una API key y prefiere que la página llame al modelo
automáticamente, en vez de solo armar el prompt para copiar a mano; (2) API de Anthropic; (3) solo
repos públicos de GitHub (sin token de GitHub adicional).

Primera versión de `agente/corrector_local.html`: un único archivo con el `system_prompt.md` y la
`rubrica.md` embebidos, que busca el repo en la API de GitHub, lee sus archivos de texto (con
límites de tamaño para no romper el contexto), arma el mensaje y llama directo a
`api.anthropic.com` desde el navegador. La API key se guarda solo en el navegador del usuario
(localStorage), nunca en el repo.

**Bug real encontrado al probarlo (no simulado):** al abrir el archivo como `file://`, Chrome tira
una excepción de seguridad al tocar `localStorage`, y como esa llamada estaba sin `try/catch`, el
script entero se rompía en silencio antes de conectar el botón "Corregir" — el botón no hacía nada
y no había ningún error visible en consola. Se probó en un navegador real (vía las herramientas de
browser), se aisló la causa comparando qué parte del script sí se ejecutaba, y se corrigió
envolviendo todo acceso a `localStorage` en funciones con try/catch (`storageGet/Set/Remove`) que
no rompen la página si el navegador lo bloquea. Se volvió a probar el flujo completo (fetch real a
GitHub + llamada real a la API de Anthropic con una key inválida a propósito) y se confirmó que
llega de punta a punta.

**Pedido de Walter, mid-construcción:** *"para tener la api key tengo que pagar aparte por uso? no
podemos correrlo local? no hace falta q corra online"*. Aclaración: la API de Anthropic
(`api.anthropic.com`) es un servicio pago por uso, facturado aparte de cualquier suscripción de
Claude.ai — no es gratis solo por tener una cuenta. Y el archivo ya corría 100% local; lo único que
salía del navegador era la llamada al modelo. Se rediseñó la herramienta para sacar esa llamada
automática y la API key por completo: ahora solo busca el repo en GitHub (gratis, sin key) y arma
el prompt completo en un `<textarea>` de solo lectura con un botón "Copiar al portapapeles", para
pegarlo a mano en cualquier chat de IA (Claude, ChatGPT, o esta misma conversación) — sin key, sin
facturación aparte, sin llamada automática a ningún lado. Se volvió a probar en el navegador contra
`agentes-ia-ucema-ej1`: leyó el repo real y armó un prompt de 18.041 caracteres, listo para copiar.

## 9 · README final

Se reescribió el `README.md` de la raíz con el formato estándar de la materia (Qué construí / Cómo
se lo pedí / Qué funciona / Qué falta o qué falló / Qué aprendí) y los integrantes, resumiendo el
resultado real de la calibración en vez de una descripción genérica del proyecto.
