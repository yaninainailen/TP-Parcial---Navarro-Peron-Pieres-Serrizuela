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

## 10 · Auditoría contra `parcial.md` y llamada automática a la API (Federico, 1/9)

Federico pidió una auditoría completa del repo contra `parcial.md` — qué falta, qué sobra, qué
mejorar — dejando elegir qué aplicar. Se encontraron y corrigieron con su aprobación: la rama de
trabajo estaba desactualizada respecto de `origin/main` (se sincronizó), el caso `excelente` no
tenía un archivo CSV real (solo texto pegado en los `.md`, lo mismo que se le criticaba a `flojo`/
`tramposo` bajo la propia regla anti-trampa — se agregaron los 3 CSV reales), el `README.md` no
mencionaba el stress test ni la prueba contra repos reales ya mergeados, y faltaba un runbook para
la prueba de fuego — los tres se agregaron.

Después pidió un cambio de fondo en `agente/corrector_local.html`: que la página, al cargar la URL
del repo, llame directo a la API de Claude (en vez de solo armar el prompt para copiar a mano como
quedó en la sección 8) y muestre el uso de tokens de cada corrida en el propio informe. Esto
revierte parcialmente la decisión de la sección 8 (sacar la llamada automática por el tema de
facturación) — ahora es opcional: se agregó un botón "Evaluar con Claude" que llama a
`api.anthropic.com/v1/messages` desde el navegador (header
`anthropic-dangerous-direct-browser-access`, necesario para que la API acepte pedidos hechos desde
un origen de navegador) con la API key y el modelo que se elijan en la página (selector con Opus 5,
Sonnet 5 y Haiku 4.5, precios de referencia a la vista), y al pie del informe agrega una tabla con
tokens de entrada/salida y el costo estimado de esa corrida puntual. El botón "Preparar prompt"
manual de la sección 8 se mantuvo intacto como alternativa sin key y sin costo. Se actualizó
`agente/config.md` y el runbook del `README.md` para que describan las dos formas de correrlo.

**Bug real encontrado al probarlo (no simulado):** Federico probó el botón "Evaluar con Claude"
con su propia API key y la API devolvió 400: `anthropic-workspace-id is required when
authenticating with an identity-linked API key; send the id of the workspace this request acts
in.` Su key es de tipo "identity-linked" (personal, con acceso a varios workspaces de su cuenta),
un caso que la primera versión del código no contemplaba — solo mandaba `x-api-key` +
`anthropic-version`. Se agregó un campo "Workspace ID" en la página (persistido en `localStorage`
igual que la key y el modelo) que, si se completa, se manda como header `anthropic-workspace-id`;
con una key de workspace normal (no identity-linked) el campo se puede dejar vacío. También se
mejoró el mensaje de error para que, si la API vuelve a responder ese 400 puntual, indique
explícitamente qué campo completar en vez de mostrar solo el texto crudo de la API.

## 11 · Sonda: la landing con 3 caminos y guardado automático (Federico, 2/9)

Federico copió a `agente/` un mockup nuevo, `sonda_v0.2.html` — una landing con mejor diseño que
ya tenía armados los 3 caminos de evaluación que pidió (un repo con API, un repo sin API con
"Prompt Validador", y una lista de repos por CSV), pero solo como formulario: los botones
simulaban con `setTimeout`, sin leer ningún repo ni llamar a ninguna API de verdad.

Pidió: darle la lógica real de `corrector_local.html` a los 3 caminos, y que — solo para los
caminos que usan la API — al terminar cada corrección se (1) muestre el resultado en la página, (2)
se clasifique en excelente/flojo/tramposo, y (3) se cree `casos/<nivel>/<Creador del repo>/` con un
`Correccion_de_<Creador>.md` y una copia de `corridas/`, `prompts/`, `ANALISIS_ECONOMICO.md`,
`DECISIONES.md`, `GOBIERNO.md` y `README.md` del repo evaluado.

**Restricción real encontrada antes de escribir nada:** una página HTML abierta con doble clic
(`file://`) no puede crear carpetas ni escribir archivos en disco — es una limitación de seguridad
del navegador, no algo evitable con más código. Se confirmó contra la documentación del navegador
que la única API que lo permite (File System Access, `showDirectoryPicker`) exige un "contexto
seguro" y no funciona en `file://`, solo en `http://localhost` o HTTPS.

Con eso sobre la mesa, se le presentaron 3 decisiones a Federico antes de tocar código:

1. **Cómo escribir a disco.** Elegido: servidor local + File System Access API (ej.
   `python -m http.server`, abrir Sonda como `http://localhost:.../sonda_v0.2.html`; la primera vez
   pide elegir la carpeta raíz del repo, un solo permiso, recordado después vía IndexedDB). Solo
   funciona en Chrome/Edge — se descartó explícitamente un fallback de `.zip` para otros
   navegadores.
2. **Umbral excelente/flojo** (la rúbrica no lo fija — solo aplica niveles por dimensión, no un
   corte del total). Elegido: `Puntaje total >= 70` → excelente, si no → flojo. La alerta
   obligatoria de trampa manda siempre a `tramposo`, sin importar el número.
3. **Dónde crear la carpeta.** Elegido: literal, dentro de `casos/<nivel>/<Creador>/`, a sabiendas
   de que eso mezcla las evaluaciones de repos ajenos con los 3 casos de prueba propios del grupo
   (se le ofreció una carpeta separada `evaluaciones/` para evitar esa mezcla, pero se prefirió lo
   pedido originalmente).

Se portaron a Sonda las funciones ya probadas de `corrector_local.html` (lectura del repo por la
API de GitHub, armado del prompt, llamada a la API de Anthropic con el fix de
`anthropic-workspace-id`, cálculo de uso de tokens) — con una diferencia: en vez de embeber
`system_prompt.md` y `rubrica.md` como texto duplicado, Sonda los lee en vivo con `fetch()` desde
sus rutas reales, aprovechando que de todos modos ahora se sirve por `http://localhost` — así no
quedan dos copias que se puedan desincronizar. Se agregó la lógica nueva (acceso a la carpeta con
permiso persistente, escritura de carpetas/archivos, clasificación del resultado, copia de las
carpetas y archivos del repo evaluado) y se conectó a los 3 botones del mockup, que hasta ahora
solo simulaban. El camino "Prompt Validador" (sin API) no clasifica ni guarda nada en disco,
conforme a lo pedido. `agente/corrector_local.html` no se tocó — sigue siendo la versión simple,
sin guardado a disco ni modo lote.

**Limitación reconocida, no resuelta:** no hay forma de probar esto de punta a punta desde la
sesión que lo construyó — no hay un navegador real ni una API key disponibles ahí. La validación
hecha fue sintáctica (el script completo parsea sin errores) y de lectura del código; falta que el
grupo lo pruebe de verdad en Chrome, con una key real, contra un repo público chico, antes de
confiar en él para la prueba de fuego.
