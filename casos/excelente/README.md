# Agente de triage de consultas — Ropa Norte

## Qué construí

Un agente que lee las consultas de clientes que llegan al formulario de contacto de "Ropa Norte"
(tienda online de indumentaria), las clasifica en una categoría, redacta una respuesta borrador
para los casos simples y deriva a una persona los casos sensibles (reclamos, temas de pago). Está
pensado para el equipo de atención al cliente, que hoy responde todo a mano.

## Cómo se lo pedí

El contrato completo está en `prompts/system_prompt.md` y `prompts/user_prompt.md`. Las
instrucciones centrales, tal como quedaron después de iterar (ver `DECISIONES.md`):

> Sos el agente de triage de consultas de Ropa Norte. Por cada fila de la planilla de tickets que
> te paso, clasificá la consulta en una de estas categorías: Estado de pedido, Cambios y
> devoluciones, Reclamo, Consulta de producto, Otro. Si es Reclamo o menciona un reembolso/pago,
> marcala para revisión humana y no redactes una respuesta final, solo un borrador tentativo. Para
> el resto, redactá una respuesta breve y amable, en español neutro, sin prometer nada que no esté
> en la política de cambios. Devolvé la salida en la tabla markdown que se especifica en el
> formato de salida.

> Acá está la planilla de tickets pendientes de hoy (`corridas/tickets_entrada.csv`, columnas:
> id, cliente, mensaje). Procesá cada fila y devolveme la tabla completa.

## Qué funciona

Las 3 corridas en `corridas/` muestran el flujo completo: entrada real (planilla de tickets),
salida estructurada idéntica en formato entre las 3, y la derivación a humano funcionando en los
casos de reclamo. El costo y el modelo usado están documentados en `ANALISIS_ECONOMICO.md`. La
supervisión humana está definida en `GOBIERNO.md` con niveles L0–L4.

## Qué falta o qué falló

El agente confunde sistemáticamente "quiero cambiar el talle" con un Reclamo en vez de Cambios y
devoluciones cuando el cliente además se queja del tiempo de espera en el mismo mensaje (ver
`corrida_2.md`, ticket 104). Lo detectamos en la segunda corrida y lo dejamos anotado como
limitación conocida en `DECISIONES.md` — no llegamos a resolverlo del todo, solo mitigarlo
parcialmente pidiendo que priorice la palabra "cambio" sobre el tono de queja.

## Qué aprendí

Que la parte difícil no es que el agente clasifique bien los casos obvios, sino que sea
consistente en los casos ambiguos (un mensaje que es a la vez queja y pedido de cambio). También
que definir "revisión humana" en una frase suelta no alcanza — hasta que no escribimos los niveles
L0–L4 explícitos, cada uno del grupo entendía "revisión" de una forma distinta.
