# Corrida 01 — Solicitud 001 (ACME Corp)

**Fecha de la corrida:** 2026-08-31
**Caso de prueba usado:** Solicitud 001 de `prompts/user_prompt.md` (interconexión de 2 datacenters, 120 km, 400G→800G, sobrevivencia a corte de una fibra sin SLA especificado, routers Cisco existentes con interfaz óptica sin definir, propuesta en 3 semanas).
**Versión de `system_prompt.md` usada como punto de partida:** la original (ver `TP 02 _Federico Serrizuela` commit inicial), sin ninguna de las mejoras de este trabajo.

## Expectativa a priori

Antes de correr el agente se esperaba que, dado que la Solicitud 001 es la más completa de las tres muestras (tiene objetivo, capacidad, geografía y plazo, aunque sin SLA de protección ni interfaces confirmadas), el agente pudiera completar razonablemente bien las Fases 1 y 2, y avanzar de forma parcial en la Fase 3 (alternativas de solución) marcando la falta de SLA de protección como pregunta abierta. La duda principal era cómo iba a manejar la frase ambigua del cliente "debe sobrevivir a un corte en una sola fibra" sin comprometerse a un mecanismo de protección concreto que el cliente nunca pidió.

## Iteración 1 — Baseline (system_prompt.md original, sin cambios)

**Cambio aplicado:** Ninguno; es la corrida de referencia.

**Salida del agente (fragmento relevante, Fase 3):**

> ## Opción 1
> Descripción: Sistema DWDM punto a punto entre los dos datacenters, con protección óptica 1+1 sobre dos rutas de fibra física independientes, dimensionado inicialmente a 400G y escalable a 800G mediante upgrade de transponders.
> Ventajas: Cumple el requisito de sobrevivir a un corte de fibra; escalable sin obra civil adicional.
> Limitaciones: Requiere confirmar que existen efectivamente dos rutas físicas independientes.
> Riesgos: Si solo existe una ruta física, la protección 1+1 no es implementable tal como está descripta.

**Errores encontrados:**
- El agente redactó "protección óptica 1+1 sobre dos rutas de fibra física independientes" dentro de la **Descripción** de la arquitectura, como si fuera parte de la solución ya definida, cuando en realidad es un supuesto no confirmado por el cliente (ACME nunca especificó cuántas rutas físicas de fibra tiene disponibles, ni pidió esquema 1+1 en particular — solo dijo "debe sobrevivir a un corte en una sola fibra").
- El `system_prompt.md` original le pide al agente "distinguir claramente entre Hechos, Supuestos y Recomendaciones" (sección Restricciones), pero el **Formato de salida** no tiene ningún campo donde volcar esa clasificación — la distinción queda diluida en prosa libre dentro de cada sección, y es fácil (como pasó acá) que un supuesto quede redactado como si fuera un hecho de diseño.
- No hay ninguna instrucción que impida al agente comprometerse con un mecanismo de protección específico (1+1, mesh, SNCP, etc.) cuando el cliente solo mencionó el requisito de negocio ("sobrevivir a un corte") sin indicar el mecanismo.

## Iteración 2 — Se agrega la sección "Clasificación de la información"

**Cambio aplicado:** Se agregó al Formato de salida una sección obligatoria `# Clasificación de la información` con subcampos `Hechos / Supuestos / Recomendaciones`, para forzar estructuralmente lo que la sección Restricciones ya pedía en texto libre.

**Salida del agente tras el cambio (fragmento relevante):**

> # Clasificación de la información
> Hechos: Distancia ~120 km; capacidad inicial 400G con crecimiento a 800G en 2 años; routers Cisco existentes en ambos extremos; plazo de propuesta 3 semanas.
> Supuestos: Se asume que existen dos rutas físicas de fibra independientes entre ambos datacenters (no confirmado); se asume esquema de protección 1+1 como mecanismo de sobrevivencia (no solicitado explícitamente por el cliente).
> Recomendaciones: Confirmar disponibilidad de ruta física redundante antes de comprometer una arquitectura de protección.

**Errores encontrados:**
- Mejora parcial: ahora el supuesto queda explícitamente etiquetado como tal, lo cual ya es auditable. Pero el problema de fondo sigue: la sección "Alternativas de solución" (Opción 1) sigue presentando "protección 1+1" como la arquitectura propuesta en su Descripción, en vez de dejar el mecanismo de protección como una variable abierta. Un SE que solo lea la sección de alternativas (sin cruzarla con "Clasificación de la información") puede seguir leyendo el 1+1 como una decisión ya tomada.
- Se confirma que el problema no es de formato sino de contenido: falta una restricción explícita que le prohíba al agente elegir un mecanismo de protección por defecto.

## Iteración 3 — Restricción explícita sobre esquema de protección

**Cambio aplicado:** Se agregó a la sección Restricciones: *"No asumas un esquema de protección específico (ej. 1+1, mesh, ODU SNCP) cuando el cliente menciona sobrevivencia a fallas sin detallar el mecanismo; trata el mecanismo de protección como información faltante en la Fase 2 en lugar de elegir uno por defecto."*

**Salida del agente tras el cambio (fragmento relevante):**

> # Información faltante
> - Mecanismo de protección esperado por el cliente (1+1 dedicado, protección compartida/mesh, restauración, etc.) y SLA de disponibilidad asociado.
> - Cantidad de rutas físicas de fibra independientes disponibles entre los dos datacenters.
>
> ## Opción 1
> Descripción: Sistema DWDM punto a punto entre los dos datacenters, con protección a nivel de capa óptica (mecanismo exacto —1+1, mesh u otro— a confirmar con el cliente según disponibilidad real de rutas físicas), dimensionado inicialmente a 400G y escalable a 800G.

**Errores encontrados:**
- Ninguno relevante nuevo. La arquitectura ahora se describe a un nivel correcto de abstracción (protección a nivel de capa óptica, mecanismo pendiente de confirmar) en vez de comprometerse a 1+1, y el mecanismo de protección quedó correctamente listado como brecha de descubrimiento en la Fase 2. Se considera resuelto para esta corrida.

## Cambio de alcance

No se aplicó ningún cambio de alcance en esta corrida — las tres iteraciones fueron mejoras de estructura y de restricciones dentro del alcance original del agente (Fases 1 a 6 sin agregar ni quitar fases).

## Campos nuevos agregados al system_prompt en esta corrida

- Sección `# Clasificación de la información` (Hechos / Supuestos / Recomendaciones) en el Formato de salida.
- Restricción nueva: prohibición de asumir un esquema de protección específico sin confirmación del cliente.
