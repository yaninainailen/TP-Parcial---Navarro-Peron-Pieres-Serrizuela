# Gobierno y riesgo — TP Final

Este archivo responde, para el agente asistente de SE descripto en [`prompts/system_prompt.md`](prompts/system_prompt.md), qué sistemas toca, qué puede salir mal, qué revisa un humano antes de confiar en la salida, y quién firma el resultado.

## Sistemas que toca y permisos

El agente **no tiene acceso a ningún sistema externo**. Es de solo lectura sobre lo que el Sales Engineer (SE) le entrega explícitamente en cada corrida:

| Sistema | Acceso del agente |
| --- | --- |
| Texto/archivo de la solicitud del cliente (notas de reunión, correos, requisitos) | Lectura, vía `scripts/run_agent.py` o pegado manual |
| `prompts/system_prompt.md` (su propio contrato) | Lectura |
| Sistema de archivos local (`corridas/raw/`) | Escritura únicamente del output que genera, vía `scripts/run_agent.py` |
| CRM / historial real del cliente | Sin acceso |
| Catálogo de producto / lista de precios vigente | Sin acceso — por eso todo BoM se marca "Pendiente de cotización con Producto/Pricing" (ver Restricciones en `system_prompt.md`) |
| Inventario de red / disponibilidad de fibra del cliente | Sin acceso |
| Email o cualquier canal de envío al cliente | Sin acceso — el agente nunca envía nada, solo redacta un borrador local |

El único conector real del sistema es `scripts/run_agent.py`: lee un archivo de solicitud de `prompts/casos/` y escribe el output completo en `corridas/raw/`. No hay ninguna vía por la que el agente escriba, modifique o envíe algo fuera de esa carpeta.

## Qué puede salir mal → qué pasa cuando sale mal

Estos son los modos de falla reales, encontrados en las 3 corridas documentadas en `corridas/` (no hipotéticos):

| Qué puede salir mal | Encontrado en | Qué pasa cuando sale mal |
| --- | --- | --- |
| Un supuesto se redacta como si fuera un hecho confirmado (ej. esquema de protección 1+1 no pedido por el cliente) | Corrida 01 | El output nunca es la nota final: pasa por la revisión del SE (ver checklist abajo) antes de convertirse en algo que se comparte. Si el SE no cruza "Clasificación de la información" contra el resto del texto, el supuesto puede colarse — por eso ese cruce está en el checklist, no es opcional |
| Pregunta de descubrimiento genérica en vez de la pregunta técnica específica que determina viabilidad | Corrida 02 | El SE completa la brecha en la siguiente interacción con el cliente; no bloquea el flujo pero sí puede demorar una cotización si no se detecta a tiempo |
| Inconsistencia interna del texto del cliente resuelta en silencio (el agente elige una versión sin avisar) | Corrida 03 | El agente ahora la declara explícitamente en "Inconsistencias detectadas" en lugar de resolverla; el SE la confirma con el cliente antes de avanzar |
| Precio de BoM inventado con apariencia plausible | Corrida 03 (único caso de alucinación de datos encontrado) | El agente ahora usa "Pendiente de cotización con Producto/Pricing" en vez de inventar cifras; cualquier precio real requiere pasar por Producto/Pricing, nunca sale del agente |
| Redacción no determinística entre corridas con el mismo input | Inherente a un LLM (ver `DECISIONES.md`) | No afecta el contenido sustantivo si el formato de salida se respeta; el SE revisa contenido, no compara redacción byte a byte |

## Qué revisa el SE antes de confiar en la salida

Checklist mínimo antes de que cualquier salida del agente se use en una conversación real con el cliente:

1. La sección "Clasificación de la información" es consistente con el resto del documento — ningún supuesto quedó redactado como hecho en otra sección.
2. Si el criterio de escalación aplica, está declarado explícitamente y las Fases 3–5 no avanzaron como ROM estándar.
3. Ningún precio de BoM aparece inventado — todo lo que no viene de Producto/Pricing dice "Pendiente de cotización".
4. Las inconsistencias detectadas en el texto del cliente están listadas, no resueltas en silencio.
5. Las preguntas de aclaración son específicas al caso (no genéricas) y cubren lo que realmente falta para diseñar.
6. Los supuestos de diseño de la nota de ingeniería (Fase 5) son técnicamente razonables — el agente no hace cálculos de ingeniería reales (potencia óptica, dispersión), así que esto lo valida ingeniería, no el agente.

## Quién firma

El SE es el responsable final de cualquier nota de ingeniería, propuesta o cotización que llegue al cliente. El agente redacta un borrador; nunca firma, nunca envía, nunca se comunica directamente con el cliente. Cuando el entregable involucra precios reales de BoM, el paso adicional de Producto/Pricing es obligatorio antes de que el SE pueda firmar una cotización formal.

## Niveles de autonomía por fase (L0–L4)

| Nivel | Significado |
| --- | --- |
| L0 | El SE hace la tarea manualmente; el agente no participa |
| L1 | El agente redacta una propuesta; el SE la reescribe o la usa como punto de partida, sin obligación de revisarla campo por campo |
| L2 | El agente ejecuta la fase solo; el SE la revisa después de generada, antes de la siguiente acción externa |
| L3 | El agente ejecuta la fase solo; el SE debe revisarla y aprobarla explícitamente antes de que el resultado se use en cualquier paso siguiente |
| L4 | El agente ejecuta y actúa de forma autónoma, sin revisión humana previa |

Este sistema **nunca opera en L4** para ninguna fase — no tiene permisos para enviar nada al cliente por su cuenta. La asignación por fase:

| Fase | Nivel | Motivo |
| --- | --- | --- |
| Chequeo de inconsistencias | L2 | Bajo riesgo: solo declara, no resuelve — el SE la lee al pasar |
| Fase 1 — Análisis de requisitos | L2 | Resume información ya provista por el cliente; error de resumen es fácil de detectar al leer |
| Fase 2 — Análisis de brechas | L2 | Genera preguntas, no compromisos; el SE las cura antes de mandarlas al cliente |
| Fase 3 — Evaluación de la solución | L3 | Compromete arquitectura y riesgos técnicos — requiere aprobación explícita del SE antes de que la propuesta avance |
| Fase 4 — Preparación de cotización/estimación | L3 | Clasifica la oportunidad como lista/parcialmente lista/no lista — decisión comercial que el SE debe validar antes de comunicarla |
| Fase 5 — Nota de ingeniería | L3 | Incluye supuestos de diseño no verificados por herramientas reales; requiere aprobación de ingeniería/SE antes de circular |
| Fase 6 — Reunión de seguimiento | L2 | Es una preparación interna del SE, no algo que llega al cliente directamente |

*Nota: esta tabla usa el vocabulario L0–L4 tal como se definió en el curso hasta donde lo tengo documentado en este repo. Antes de la entrega final conviene contrastarla contra el material exacto de la última clase (jueves 10/9), por si el curso define los niveles con otro criterio.*
