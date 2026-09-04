# Corrida 03 — Solicitud 003 (BMINING)

**Fecha de la corrida:** 2026-08-31
**Caso de prueba usado:** Solicitud 003 de `prompts/user_prompt.md` (nuevo anillo DWDM de 3 sitios en San Juan, Argentina, distancias <50 km, 2 canales ópticos a 400G, alta disponibilidad con 2 plataformas C-4615 por sitio, interfaces tributarias sin definir, pedido explícito de BoM presupuestario con precios, plazo de 10 días, y pedido de alternativas para interconectar el nuevo anillo con otras redes existentes de BMINING).
**Versión de `system_prompt.md` usada como punto de partida:** la resultante de la Corrida 02 (ver [`corrida_02_SYNNEX.md`](corrida_02_SYNNEX.md)) — ya incluye clasificación de información, restricción de esquema de protección, checklist de requisitos de aplicación, y campos de entregable/urgencia.

Esta fue la corrida con más hallazgos de las tres: es la única de las tres solicitudes con una inconsistencia interna en los datos, la única que pide explícitamente una plataforma de un proveedor (C-4615) y precios, y la única que pide explícitamente alternativas de interconexión con red existente del cliente — ningún caso anterior había estresado esas tres cosas a la vez.

## Expectativa a priori

Se esperaba que el agente, al no tener ninguna instrucción de validar consistencia interna de los datos, no notara que el texto nombra los canales ópticos como "IT#1 y OT#1" en una línea y "IT#1 y OT#2" dos líneas más abajo, y que simplemente eligiera una de las dos versiones sin avisar. También se esperaba fricción entre la restricción "evita recomendaciones específicas de un proveedor" (heredada de las corridas anteriores) y el hecho de que acá el *cliente* es quien nombra la plataforma (C-4615) y pide un BoM con precios — un caso que el `system_prompt.md` no contemplaba y que podía hacer que el agente hiciera algo indeseado en cualquiera de los dos extremos: inventar precios, o negarse a nombrar la plataforma que el propio cliente ya especificó.

## Iteración 1 — Baseline (system_prompt.md post-Corrida 02)

**Cambio aplicado:** Ninguno; se usa tal cual quedó al cierre de la Corrida 02.

**Salida del agente (fragmento relevante):**

> # Requisitos del cliente
> - Anillo DWDM de 3 sitios en San Juan, Argentina, distancias menores a 50 km entre sitios.
> - 2 canales ópticos (IT#1 y OT#1) a 400G iniciales.
> - Alta disponibilidad: 2 plataformas por sitio.
> - Plazo de entrega: 10 días.
>
> # Alternativas de solución
> ## Opción 1
> Descripción: Anillo DWDM de 3 sitios con protección de anillo óptico, 2 canales a 400G cada uno, con equipamiento redundante en cada sitio según lo solicitado por el cliente.

**Errores encontrados:**
- **Error 1 (inconsistencia no detectada):** el agente tomó "IT#1 y OT#1" y simplemente descartó en silencio la mención posterior a "OT#2" sin avisar al SE de la contradicción. Un SE que arme la nota de ingeniería a partir de esta salida podría enviarle al cliente una confirmación con el nombre de canal equivocado.
- **Error 2 (restricción de proveedor mal aplicada):** en el resto de la salida (no mostrado arriba), el agente evitó nombrar "C-4615" directamente en varios lugares por aplicar literalmente la restricción "evita recomendaciones específicas de un proveedor", pese a que el cliente fue quien nombró esa plataforma como requisito, no el agente quien la está recomendando. Esto generó una redacción forzada tipo "plataforma de alta disponibilidad especificada por el cliente" en vez de simplemente citar el dato.
- **Error 3 (BoM con precios sin guía):** ante el pedido explícito de "una lista de materiales (BoM) presupuestaria con precios", el agente completó la tabla de items con **precios inventados** (con apariencia plausible pero sin ninguna fuente real), porque el `system_prompt.md` no le daba ninguna instrucción de qué hacer cuando se le pide un precio que no tiene forma de conocer. Este es el hallazgo más riesgoso de las tres corridas: es el único caso de alucinación de datos concreto detectado en todo el ejercicio.
- **Error 4 (pedido sin sección propia):** el pedido explícito de "alternativas para la interconexión de esta nueva red en anillo con otras redes actuales de BMINING" terminó mezclado como una viñeta suelta dentro de la Opción 2, sin un tratamiento propio (arquitectura/ventajas/limitaciones/riesgos), a pesar de ser un pedido explícito y separado del cliente.

## Iteración 2 — Chequeo de inconsistencias + sección dedicada

**Cambio aplicado:** Se agregó, antes del inicio de la Fase 1, la instrucción de revisar la información del cliente en busca de inconsistencias internas y declararlas explícitamente sin intentar resolverlas por cuenta propia; y se agregó al Formato de salida la sección `# Inconsistencias detectadas` (omitible si no aplica).

**Salida del agente tras el cambio (fragmento relevante):**

> # Inconsistencias detectadas
> El texto identifica los dos canales ópticos como "IT#1 y OT#1" en un punto, y como "IT#1 y OT#2" en otro. No es posible determinar cuál es el identificador correcto sin confirmación del cliente; se recomienda validarlo antes de avanzar con el diseño detallado. El resto de este documento asume que se trata de 2 canales ópticos en total (no 3), pero usa el identificador ambiguo tal como fue recibido.

**Errores encontrados:**
- Resuelto el Error 1: la inconsistencia ahora queda documentada de forma explícita, con la interpretación razonable que tomó el agente (2 canales, no 3) declarada como tal en vez de quedar oculta.
- Los Errores 2, 3 y 4 siguen presentes — pasan a las siguientes iteraciones.

## Iteración 3 — Excepción a la restricción de proveedor + guía de BoM/pricing

**Cambio aplicado:** Se modificó la restricción de proveedor para aclarar que no aplica cuando es el propio cliente quien especifica su plataforma o equipamiento existente/deseado (en ese caso se toma como dato, no como recomendación); y se agregó la restricción de no inventar precios de BoM, estructurando igual los ítems y marcando el precio como "Pendiente de cotización con Producto/Pricing" cuando no hay datos reales de costo.

**Salida del agente tras el cambio (fragmento relevante):**

> # Requisitos del cliente
> - Alta disponibilidad: 2 plataformas C-4615 por sitio (3 sitios → 6 unidades totales), según especificación del cliente.
>
> # Borrador de la nota de ingeniería (extracto de BoM)
> | Ítem | Cantidad | Precio |
> |---|---|---|
> | Plataforma C-4615 | 6 (2 por sitio × 3 sitios) | Pendiente de cotización con Producto/Pricing |
> | Módulos ópticos 400G (canal IT#1) | A confirmar según interfaz tributaria elegida | Pendiente de cotización con Producto/Pricing |

**Errores encontrados:**
- Resuelto el Error 2: el agente ahora cita "C-4615" con naturalidad como dato del cliente, sin rodeos artificiales.
- Resuelto el Error 3: ya no hay precios inventados; el BoM queda estructurado (ítems y cantidades derivadas correctamente de "2 plataformas por sitio × 3 sitios = 6") con el precio marcado explícitamente como pendiente, que es información veraz en vez de una alucinación con apariencia de dato real.
- El Error 4 (interconexión con red existente sin sección propia) sigue presente — pasa a la iteración final.

## Iteración 4 — Sección propia para interconexión con red existente

**Cambio aplicado:** Se agregó a la Fase 3 una instrucción explícita para evaluar alternativas de interconexión con infraestructura existente del cliente cuando este lo pida, y se agregó al Formato de salida la subsección `## Interconexión con red(es) existente(s) del cliente` dentro de "Alternativas de solución".

**Salida del agente tras el cambio (fragmento relevante):**

> ## Interconexión con red(es) existente(s) del cliente
> Descripción: Dado que BMINING ya opera otras redes, se evalúan dos alternativas para vincular el nuevo anillo de San Juan con la red existente: (a) mediante un sitio de interconexión (gateway óptico) en uno de los 3 sitios nuevos, o (b) mediante un enlace dedicado adicional hacia el POP existente más cercano.
> Ventajas / Limitaciones / Riesgos: (detallado por alternativa)
> Nota: Falta información sobre la topología y capacidad de la red existente de BMINING para evaluar esto con precisión; queda listado también en "Información faltante".

**Errores encontrados:**
- Ninguno relevante nuevo. El pedido de interconexión con red existente ahora tiene tratamiento propio, comparable en profundidad al resto de las alternativas, en lugar de aparecer como una viñeta suelta. Se considera resuelto para esta corrida.

## Cambio de alcance

**Sí.** Al igual que en la Corrida 01 se había restringido el comportamiento del agente frente a esquemas de protección, acá se tuvo que **ampliar** una restricción existente: "evita recomendaciones específicas de un proveedor" pasó de ser una regla sin excepciones a una regla con una excepción explícita (cuando el dato de plataforma/equipamiento lo aporta el propio cliente). Es un cambio de alcance porque redefine hasta dónde llega esa restricción, no solo un agregado de contenido nuevo.

## Campos nuevos agregados al system_prompt en esta corrida

- Instrucción de chequeo de inconsistencias internas antes de la Fase 1.
- Sección `# Inconsistencias detectadas` en el Formato de salida.
- Excepción explícita a la restricción de proveedor cuando el dato lo aporta el cliente.
- Restricción de no inventar precios de BoM; uso obligatorio de "Pendiente de cotización con Producto/Pricing".
- Instrucción en la Fase 3 + subsección `## Interconexión con red(es) existente(s) del cliente` en el Formato de salida.

## Qué se descartó

- **Una "Fase 0" explícita de chequeo de escalación al inicio del flujo:** se consideró, pero se descartó por redundante — la sección "Criterios de escalación" ya indica que reemplaza el avance por las Fases 3–5 cuando aplica; agregar una fase extra numerada habría complicado el Formato de salida sin un beneficio claro sobre simplemente dejar esa sección donde está.
- **Que el agente genere diagramas de red en ASCII para cada alternativa:** se consideró para mejorar la Fase 3, pero se descartó porque un LLM no tiene garantías de precisión geométrica ni técnica al dibujar topologías en texto, y un diagrama con apariencia prolija pero técnicamente incorrecto es más peligroso para un SE que no tener diagrama — se prefiere que el agente describa la arquitectura en prosa y deje el diagrama real a cargo del SE o de una herramienta de diseño.
- **Cálculo automático de presupuesto de potencia óptica (link budget) a partir de distancia y tipo de fibra:** se consideró para robustecer la Fase 3/5 en casos como este (3 sitios <50 km, buen candidato para un cálculo rápido), pero se descartó por el riesgo de error técnico real sin herramientas de cálculo de ingeniería; en cambio, ese tipo de cálculo queda explícitamente como "Supuesto de diseño a validar con ingeniería" en la nota de ingeniería (Fase 5), que es lo que ya cubre el `system_prompt.md` sin necesidad de un campo nuevo.
