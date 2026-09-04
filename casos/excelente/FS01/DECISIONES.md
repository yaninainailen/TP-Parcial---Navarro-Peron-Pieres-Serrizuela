# Decisiones — TP Final

Este archivo consolida los cambios aplicados a `prompts/system_prompt.md` y `prompts/user_prompt.md` durante las 3 corridas documentadas en `corridas/`, con el motivo de cada uno. El detalle iteración por iteración, con las salidas del agente y los errores encontrados, está en cada archivo de corrida:

- [`corridas/corrida_01_ACME.md`](corridas/corrida_01_ACME.md) — 2026-08-31
- [`corridas/corrida_02_SYNNEX.md`](corridas/corrida_02_SYNNEX.md) — 2026-08-31
- [`corridas/corrida_03_BMINING.md`](corridas/corrida_03_BMINING.md) — 2026-08-31

Las corridas se ejecutaron en ese orden y cada una parte del `system_prompt.md` resultante de la anterior — es decir, las mejoras se acumulan.

## Cambios aplicados a `prompts/system_prompt.md`

| # | Cambio | Corrida que lo originó | Motivo |
|---|---|---|---|
| 1 | Sección `# Clasificación de la información` (Hechos / Supuestos / Recomendaciones) en el Formato de salida | Corrida 01, iteración 2 | La sección Restricciones ya pedía distinguir hechos/supuestos/recomendaciones, pero no había ningún campo de salida donde volcarlo — un supuesto (esquema de protección 1+1) terminó redactado como si fuera un hecho de diseño. |
| 2 | Restricción: no asumir un esquema de protección específico sin confirmación del cliente | Corrida 01, iteración 3 | Aun con la clasificación agregada, el agente seguía comprometiéndose con "protección 1+1" en la descripción de la arquitectura cuando el cliente solo pidió sobrevivir a un corte de fibra, sin especificar el mecanismo. |
| 3 | Ítem de checklist en Fase 2: requisitos de la aplicación (modo de replicación síncrono/asíncrono, latencia, ventanas de corte) | Corrida 02, iteración 2 | Ante un caso de replicación de bases de datos, el agente solo preguntaba por "latencia máxima tolerada" en forma genérica; faltaba la pregunta técnica específica (síncrono/asíncrono) que realmente determina si la distancia es viable. |
| 4 | Ampliación de la Fase 4 para cubrir "cotización formal **o** estimación presupuestaria preliminar" + campos `Tipo de entregable solicitado` y `Urgencia declarada` en el Resumen de la oportunidad | Corrida 02, iteración 3 | El cliente pidió una "primera propuesta presupuestaria en una semana" y el agente lo evaluó con el vocabulario y el criterio de una cotización formal, sin distinguir la urgencia ni el tipo de entregable esperado. |
| 5 | Instrucción de revisar inconsistencias internas antes de la Fase 1 + sección `# Inconsistencias detectadas` en el Formato de salida | Corrida 03, iteración 2 | El texto de la Solicitud 003 nombra los canales ópticos como "IT#1 y OT#1" en un punto y "IT#1 y OT#2" en otro; el agente elegía una versión en silencio sin avisar de la contradicción. |
| 6 | Excepción a la restricción de proveedor: no aplica cuando el dato de plataforma/equipamiento lo aporta el propio cliente | Corrida 03, iteración 3 | El cliente (BMINING) especificó su propia plataforma (C-4615) como requisito, no como algo a recomendar; la restricción original hacía que el agente evitara nombrarla con rodeos artificiales. |
| 7 | Restricción de no inventar precios de BoM; usar "Pendiente de cotización con Producto/Pricing" cuando no hay datos reales de costo | Corrida 03, iteración 3 | Ante el pedido explícito de un BoM con precios, el agente completó la tabla con precios inventados de apariencia plausible — el único caso de alucinación de datos detectado en las 3 corridas. |
| 8 | Instrucción en Fase 3 + subsección `## Interconexión con red(es) existente(s) del cliente` en el Formato de salida | Corrida 03, iteración 4 | El cliente pidió explícitamente alternativas para interconectar la nueva red con su infraestructura existente; sin un lugar propio en el formato, ese pedido terminaba mezclado como una viñeta suelta dentro de otra opción de solución. |
| 9 | Sección `# Supervisión y niveles de autonomía` (vocabulario L0–L4 del curso, mapeado a las 6 fases) | Revisión de cierre contra `trabajo-final.md` (2026-09-01) | La supervisión humana existía solo como prosa genérica en el README ("revisión humana obligatoria"), no como parte del contrato mismo ni con el vocabulario L0–L4 que exige el ítem 1 de la rúbrica del trabajo final. |

## Cambios estructurales al repositorio (fuera de los prompts)

Al revisar el repo completo contra `trabajo-final.md`, el 2026-09-01 se encontró que dos dimensiones enteras de la rúbrica (Análisis económico y Gobierno y riesgo, 30% combinado) no tenían ningún artefacto, y que el sistema no cumplía el requisito de "al menos una herramienta o conector real" — las 3 corridas se habían hecho copiando y pegando manualmente entre un chat y los archivos de `corridas/`, no vía una llamada real a la API. Se aplicaron estos cambios:

| Cambio | Motivo |
| --- | --- |
| Rename `DESICIONES.md` → `DECISIONES.md` | `trabajo-final.md` exige ese nombre exacto para la estructura obligatoria; el nombre original tenía un typo que un evaluador automático no habría podido resolver por su cuenta. |
| `scripts/run_agent.py` (+ `prompts/casos/*.md`, `corridas/raw/`) | Resuelve el requisito de herramienta/conector real: lee un caso desde un archivo real y escribe el output completo (con tokens y costo reales de la API) a otro archivo real, en vez de depender de copy-paste manual. |
| `ANALISIS_ECONOMICO.md` | Cubre el ítem 5 de la rúbrica (costo por corrida, proyección semanal/anual, elección de modelo justificada), inexistente hasta esta revisión. |
| `GOBIERNO.md` | Cubre el ítem 6 de la rúbrica (sistemas y permisos, qué puede salir mal y respuesta, checklist de revisión del SE, quién firma, niveles L0–L4 por fase), que antes vivía disperso e implícito en "Limitaciones conocidas". |

`ANALISIS_ECONOMICO.md` quedó inicialmente con los números de costo marcados como pendientes (no había `ANTHROPIC_API_KEY` en el entorno). El 2026-09-01 se corrió `scripts/run_agent.py` con una API key real contra los 3 casos, con `claude-sonnet-5` y `claude-haiku-4-5`, y se completó el análisis con datos reales de `corridas/raw/`.

### Iteración operativa al correr el runner por primera vez (2026-09-01)

Correr el sistema de verdad, y no solo describirlo, encontró tres problemas reales que no habían aparecido en las 3 corridas manuales (hechas por copy-paste, sin API):

1. **Bug de `max_tokens` demasiado bajo:** con `max_tokens=4096` (el valor inicial del script), Sonnet 5 —que piensa por defecto (`thinking: adaptive`)— gastó casi todo el presupuesto en razonamiento interno y devolvió casi nada de texto visible en 2 de los 3 casos (las corridas de SYNNEX y BMINING quedaron con apenas la cabecera). Se subió el default a `max_tokens=16000` y se agregó un flag `--max-tokens` para ajustarlo por caso.
2. **Error del SDK al pedir mucho `max_tokens` sin streaming:** al subir `max_tokens` más allá de cierto punto para el caso BMINING (el más largo), el SDK de Anthropic rechazó la llamada no-streaming con `ValueError: Streaming is required for operations that may take longer than 10 minutes`. Se cambió `client.messages.create(...)` por `client.messages.stream(...).get_final_message()`, que no tiene ese límite.
3. **Autenticación con key "identity-linked":** la key usada requería además el header `anthropic-workspace-id` (error `400 anthropic-workspace-id is required...`). Se agregó soporte a una variable de entorno opcional `ANTHROPIC_WORKSPACE_ID` que el script manda como `default_headers` si está presente.

Con esos tres fixes, las 6 corridas (3 casos × 2 modelos) completaron sin truncarse. Los archivos quedan en `corridas/raw/`.

### Hallazgo de la comparación de modelo (Haiku 4.5 vs. Sonnet 5)

Con las 6 corridas reales se pudo hacer la comparación de modelo que pedía `ANALISIS_ECONOMICO.md` (antes solo planeada, no ejecutada). Resultado no trivial: **Haiku 4.5 no sostiene el contrato en el caso más exigente (BMINING)** — se detiene después de la Fase 2 en vez de completar las 6 fases con supuestos declarados como pide el `system_prompt.md`, y ya desde el caso más simple (ACME) reemplaza la plantilla fija de "Formato de salida" por su propia organización (encabezados `# Fase 1`/`# Fase 2`, tablas propias) en vez de las secciones exactas del contrato. Sonnet 5 respeta el formato letra por letra en los 3 casos y completa el flujo. Por eso se elige `claude-sonnet-5` como modelo del sistema pese a costar ~2.5-3× más por token que Haiku — el criterio "el más chico que hace bien la tarea" se aplicó de forma literal: Haiku es el más chico, pero no hace bien la tarea bajo este contrato. Detalle completo en `ANALISIS_ECONOMICO.md`.

De paso, estas corridas reales validaron algo agregado en esta misma revisión: en el caso BMINING, Sonnet 5 usó espontáneamente el vocabulario de la sección `# Supervisión y niveles de autonomía` agregada al `system_prompt.md` ("Nota de gobernanza: esta fase requiere revisión y aprobación explícita del SE...") en las fases marcadas como L3, sin que se le haya pedido literalmente esa frase — confirma que quedó bien integrado al contrato y no es una sección aislada que el modelo ignora.

## Cambios aplicados a `prompts/user_prompt.md`

Se aplicaron únicamente correcciones editoriales menores (tildes, un typo de "intercoenctar", una mayúscula fuera de lugar en "Una semana"). **No** se corrigió la inconsistencia "IT#1/OT#1" vs. "IT#1/OT#2" de la Solicitud 003 — ver "Qué se descartó" abajo.

## Cambios de alcance aplicados

- **Corrida 02:** se amplió el alcance de la Fase 4, que originalmente solo contemplaba preparar una cotización formal, para cubrir también estimaciones presupuestarias preliminares (entregable más liviano que los clientes piden con frecuencia bajo plazos cortos).
- **Corrida 03:** se amplió (con una excepción explícita) el alcance de la restricción "evita recomendaciones específicas de un proveedor", que pasó de ser una regla sin excepciones a una regla que no aplica cuando el dato de plataforma lo aporta el propio cliente.

## Qué se descartó

- **Corregir la inconsistencia OT#1/OT#2 en `user_prompt.md`:** se consideró, pero se descartó a propósito — corregirla habría eliminado el único caso de prueba real de detección de inconsistencias entre las 3 solicitudes, que es justamente lo que motivó el cambio #5 de la tabla de arriba.
- **Una "Fase 0" explícita de chequeo de escalación al inicio del flujo:** descartada por redundante frente a la sección "Criterios de escalación" ya existente (ver detalle en `corridas/corrida_03_BMINING.md`).
- **Generación de diagramas de red en ASCII por parte del agente:** descartada por riesgo de imprecisión técnica en un diagrama generado por un LLM sin herramienta de diseño real.
- **Cálculo automático de presupuesto de potencia óptica (link budget):** descartado por riesgo de error de ingeniería sin herramientas de cálculo reales; se prefiere dejarlo como supuesto de diseño a validar (cubierto ya por la Fase 5, sin necesidad de un campo nuevo).

## Limitaciones conocidas del agente

Estas limitaciones no se intentaron resolver vía prompt porque son inherentes al hecho de ser un agente basado en un LLM sin herramientas ni datos externos, y quedan anotadas también en `README.md`:

1. **No procesa diagramas ni archivos adjuntos reales.** El `Contexto` del `system_prompt.md` menciona "diagramas de red" como posible input, pero el agente solo puede razonar sobre texto: si el usuario tiene un diagrama, tiene que transcribir la información relevante a texto.
2. **No tiene acceso a datos reales** de disponibilidad de fibra oscura, catálogo de productos, precios vigentes ni inventario de red existente del cliente. Todo BoM o alternativa de interconexión que produce es estructural (ítems, cantidades, arquitectura), no verificado contra sistemas reales.
3. **No hace cálculos de ingeniería reales** (presupuesto de potencia óptica, dispersión cromática, etc.) — los deja explícitamente marcados como supuestos a validar por ingeniería en la nota de ingeniería (Fase 5), y así se descartó automatizarlos (ver arriba).
4. **No es determinístico.** A diferencia de un agente evaluador con rúbrica de puntajes fijos, dos corridas con el mismo input pueden variar levemente en redacción aunque sigan el mismo Formato de salida — no hay garantía de salida idéntica byte a byte.
5. **Solo detecta inconsistencias explícitas en el texto que recibe** (como el caso IT#1/OT#2); no puede validar la solicitud contra un CRM, un ERP o el historial real del cliente para detectar contradicciones con datos externos.
6. **Requiere revisión humana obligatoria** antes de enviar cualquier nota de ingeniería, propuesta o cotización al cliente — es un asistente para el Sales Engineer, no un reemplazo de su criterio.
