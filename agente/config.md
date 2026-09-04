# Configuración del agente corrector

## Modelo

Un modelo de tipo "frontier" con buena capacidad de lectura y seguimiento de instrucciones largas
(ej. Claude, GPT). No hace falta el modelo más grande disponible — la tarea es lectura y
comparación contra una rúbrica escrita, no razonamiento creativo — pero sí uno confiable siguiendo
instrucciones estructuradas al pie de la letra, porque de eso depende el determinismo pedido en
`system_prompt.md`.

**Temperatura: 0 (o el valor más bajo disponible).** La consigna pide que la rúbrica se aplique
"igual dos veces" — con temperatura alta el mismo repo podría recibir puntajes distintos en
corridas distintas, lo cual invalida la corrección.

## Entradas que recibe

1. `system_prompt.md` (este directorio) — fijo, no cambia entre corridas.
2. `rubrica.md` (raíz del repo) — fijo, no cambia entre corridas.
3. El contenido completo del repositorio del alumno a corregir: `README.md`, `prompts/`,
   `corridas/`, `DECISIONES.md` (y cualquier otro archivo que el alumno haya incluido).

## Herramienta / conector necesario

**Acceso de lectura a archivos o a un repositorio de GitHub.** En la práctica, esto puede ser:
- Pegar el contenido de los archivos del repo directamente en el contexto del agente, o
- Un conector con acceso de lectura al repo (API de GitHub, un clon local, etc.)

No se necesita acceso de escritura en ningún momento — el agente corrector nunca modifica el
repo que evalúa.

`agente/sonda_v0.2.html` implementa esto, con tres caminos elegibles paso a paso en la misma
página:

1. **Un repo con API ("Con API de Claude"):** la página arma el prompt (system prompt + rúbrica +
   contenido del repo leído vía la API pública de GitHub) y llama directo a
   `api.anthropic.com/v1/messages` desde el navegador, con la API key, el Workspace ID y el modelo
   que elija quien corre la herramienta. Requiere una API key propia de Anthropic (consume crédito
   pago), guarda el resultado automáticamente en disco (ver más abajo) y muestra el uso de tokens
   (`usage.input_tokens`/`usage.output_tokens`) y un costo estimado de esa corrida al pie del
   informe.
2. **Un repo sin API ("Prompt Validador"):** arma el mismo prompt y lo deja listo para copiar a
   mano en cualquier chat de IA, sin key, sin costo y sin guardado en disco.
3. **Una lista de repos por CSV:** el mismo camino con API pero procesando muchas filas
   (`url,creador`) una por una.

La API key se guarda solo en `localStorage` del navegador de quien la usa, nunca en el repo ni en
ningún servidor propio del grupo.

**Nota sobre tipos de API key:** si la key es de tipo "identity-linked" (personal, con acceso a
varios workspaces de la cuenta de Anthropic), la API exige además el header
`anthropic-workspace-id` con el ID del workspace en el que corre el pedido — la página tiene un
campo aparte para pegarlo (Console → Settings → Workspaces, empieza con `wrkspc_`). Con una key de
workspace normal (no identity-linked) ese campo se puede dejar vacío.

### Guardado automático en disco

Cuando se usa la API (caminos 1 y 3), al terminar cada corrección Sonda: (a) clasifica el
resultado — `tramposo` si el informe trae la alerta obligatoria `⚠️ Posible caso de trabajo
tramposo detectado`; si no, `excelente` con `Puntaje total >= 70`, `flojo` por debajo de ese
número — y (b) crea `casos/<nivel>/<Creador del repo>/` con `Correccion_de_<Creador>.md` (el
informe completo + uso de tokens) más una copia de `corridas/`, `prompts/`,
`ANALISIS_ECONOMICO.md`, `DECISIONES.md`, `GOBIERNO.md` y `README.md` del repo evaluado.

**Por qué necesita un servidor local y no alcanza con abrir el archivo con doble clic:** escribir
carpetas y archivos en disco desde el navegador solo es posible con la File System Access API
(`showDirectoryPicker`), y esa API exige un "contexto seguro" — no funciona en páginas `file://`.
Sí funciona en `http://localhost`, así que Sonda se tiene que servir con un comando simple (ej.
`python -m http.server` desde la raíz del repo) y abrirse como
`http://localhost:8000/agente/sonda_v0.2.html`. Además, solo Chrome y Edge soportan esa API — en
Firefox o Safari, Sonda avisa explícitamente que no puede guardar en disco en vez de fallar en
silencio.

La primera vez que se usa "Analizar", el navegador pide elegir la carpeta raíz del repo (un único
permiso, se recuerda entre sesiones). El acceso es de lectura y escritura sobre esa carpeta
puntual — Sonda nunca pide ni necesita acceso a ninguna otra parte del disco.

## Salida

Texto en el formato fijo definido en `system_prompt.md`, sección "Formato de salida obligatorio".
No se acepta una salida que se desvíe de ese formato, aunque el contenido sea correcto: el formato
estructurado es parte de lo que se evalúa del propio corrector.

## Supervisión humana sobre el corrector

Siguiendo el vocabulario L0–L4 del curso:

- **L0-L1 (automático):** lectura del repo, mapeo de estructura, comparación contra la rúbrica y
  redacción del informe — el agente lo hace solo.
- **L2 (revisión humana antes de publicar):** el puntaje que decide *quién gana la prueba de
  fuego* o que resulta en la nota final de un compañero no se publica sin que una persona del
  grupo (o el profesor, en la corrección real de los trabajos finales) haya leído el informe
  completo y esté de acuerdo con que la evidencia citada es real.
- **L4 (firma humana):** el profesor arbitra cualquier desacuerdo entre lo que dice el agente y el
  criterio humano — el agente asiste la corrección, no la reemplaza en caso de conflicto.
