# Corrida 02 — Solicitud 002 (SYNNEX Corp)

**Fecha de la corrida:** 2026-08-31
**Caso de prueba usado:** Solicitud 002 de `prompts/user_prompt.md` (replicación de bases de datos entre 2 oficinas, 58 km de fibra oscura, 2 rutas independientes requeridas, sobrevivencia a corte de una ruta sin SLA, routers Cisco existentes con velocidad de interfaz sin definir, primera propuesta presupuestaria en 1 semana).
**Versión de `system_prompt.md` usada como punto de partida:** la resultante de la Corrida 01 (ver [`corrida_01_ACME.md`](corrida_01_ACME.md)) — ya incluye la sección "Clasificación de la información" y la restricción sobre no asumir esquema de protección.

## Expectativa a priori

Se esperaba que las mejoras de la Corrida 01 se sostuvieran (el agente no debería volver a comprometerse con un esquema de protección específico, ya que la Solicitud 002 tiene una ambigüedad de sobrevivencia muy similar a la de ACME). La incógnita principal era otra: esta solicitud es para **replicación de bases de datos**, un caso de uso mucho más sensible a la latencia que una interconexión genérica de datacenters, y el `system_prompt.md` no tenía en su checklist de la Fase 2 ningún ítem que empujara al agente a preguntar específicamente por el modo de replicación (síncrono/asíncrono) o la tolerancia a latencia — se esperaba que esa omisión apareciera como error. También se esperaba fricción entre el pedido del cliente ("primera propuesta presupuestaria en una semana") y el vocabulario de la Fase 4 original, pensada en términos de "cotización".

## Iteración 1 — Baseline (system_prompt.md post-Corrida 01)

**Cambio aplicado:** Ninguno; se usa tal cual quedó al cierre de la Corrida 01.

**Salida del agente (fragmento relevante, Fase 2 y Fase 4):**

> # Información faltante
> - Mecanismo de protección esperado por el cliente y SLA de disponibilidad asociado.
> - Velocidad exacta de las interfaces ópticas en los routers Cisco (1G/10G/100G).
> - Latencia máxima tolerada por la aplicación.
>
> # Evaluación de la preparación para la cotización
> Estado: Parcialmente listo
> Justificación: Falta confirmar velocidad de interfaces y mecanismo de protección antes de cotizar formalmente.

**Errores encontrados:**
- **Positivo (confirma la mejora previa):** el agente no asumió un esquema de protección por defecto; correctamente lo dejó en "Información faltante". La restricción agregada en la Corrida 01 se sostiene con un caso de uso distinto.
- **Error 1:** la pregunta de latencia quedó genérica ("latencia máxima tolerada por la aplicación") en vez de algo específico y accionable para un SE. Para una replicación de base de datos, la pregunta clave es el **modo de replicación** (síncrona vs. asíncrona), porque de eso depende si 58 km son viables en absoluto para el modo síncrono o si hay que replantear la arquitectura. El checklist de la Fase 2 del `system_prompt.md` no menciona nada relacionado a requisitos de la aplicación consumidora del enlace, así que el agente nunca llegó a esa pregunta más precisa.
- **Error 2:** el cliente pidió una "primera propuesta presupuestaria en una semana", no una cotización formal. El agente igual evaluó el caso bajo el título "preparación para la cotización" y el estado "Parcialmente listo" sin distinguir que lo que se pide es una estimación preliminar rápida, no un proceso formal de cotización — el `system_prompt.md` no tiene ningún campo que capture qué tipo de entregable pidió el cliente ni la urgencia declarada.

## Iteración 2 — Se agrega ítem de requisitos de aplicación en la Fase 2

**Cambio aplicado:** Se agregó al checklist de la Fase 2: *"Requisitos de la aplicación que consume el servicio (por ejemplo, modo de replicación de datos síncrono/asíncrono, tolerancia a latencia, ventanas de corte permitidas)."*

**Salida del agente tras el cambio (fragmento relevante):**

> # Preguntas de aclaración
> - ¿La replicación entre ambas oficinas es síncrona o asíncrona? Si es síncrona, ¿cuál es el RPO/RTO objetivo? La distancia de 58 km puede acercarse al límite práctico de latencia para replicación síncrona según la aplicación de base de datos utilizada.
> - ¿Existen ventanas de mantenimiento en las que un corte de fibra sea tolerable sin impacto?

**Errores encontrados:**
- La pregunta de aclaración ahora sí es específica y técnicamente relevante (distingue síncrono/asíncrono y menciona el límite práctico de latencia a 58 km, que es exactamente el tipo de brecha que un SE necesita cerrar antes de diseñar la solución). Se considera resuelto el Error 1.
- El Error 2 (terminología "cotización" vs. "propuesta presupuestaria", falta de campo de urgencia) sigue presente sin cambios — pasa a la siguiente iteración.

## Iteración 3 — Se amplía la Fase 4 y se agregan campos de entregable/urgencia

**Cambio aplicado:** Se renombró la Fase 4 a "Preparación para la cotización o estimación presupuestaria" (antes cubría solo cotización formal) y se agregaron dos campos nuevos al inicio del Formato de salida, dentro de "Resumen de la oportunidad": `Tipo de entregable solicitado por el cliente` y `Urgencia declarada por el cliente`.

**Salida del agente tras el cambio (fragmento relevante):**

> # Resumen de la oportunidad
> Tipo de entregable solicitado: Estimación presupuestaria preliminar (no cotización formal)
> Urgencia declarada por el cliente: 1 semana
> ...
> # Evaluación de la preparación para la cotización o estimación
> Estado: Parcialmente listo
> Justificación: Hay información suficiente para una estimación presupuestaria preliminar de alto nivel dentro del plazo de 1 semana solicitado, aunque no para una cotización formal, dado que faltan confirmar velocidad de interfaces, modo de replicación y mecanismo de protección.

**Errores encontrados:**
- Ninguno relevante nuevo. El resumen ahora refleja correctamente que lo pedido es una estimación preliminar rápida y no un ciclo completo de cotización, y la urgencia queda visible desde el principio del documento en vez de perderse en el texto. Se considera resuelto para esta corrida.

## Cambio de alcance

**Sí.** Se amplió el alcance de la Fase 4: en la versión original, la fase solo contemplaba preparar una **cotización formal**. Se lo reformuló para cubrir también **estimaciones presupuestarias preliminares**, que es un entregable más liviano y más rápido que los clientes piden con frecuencia (como en este caso, con 1 semana de plazo) y que el flujo original no distinguía de una cotización completa.

## Campos nuevos agregados al system_prompt en esta corrida

- Ítem de checklist en la Fase 2: requisitos de la aplicación (modo de replicación, latencia, ventanas de corte).
- Campo `Tipo de entregable solicitado por el cliente` en el Resumen de la oportunidad.
- Campo `Urgencia declarada por el cliente` en el Resumen de la oportunidad.
- Renombre/ampliación de la Fase 4 para cubrir cotización formal **o** estimación presupuestaria preliminar.
