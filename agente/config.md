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
