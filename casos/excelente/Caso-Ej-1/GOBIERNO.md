# Gobierno y riesgo

## Qué sistemas toca y con qué permisos
El agente tiene acceso de **lectura únicamente** a la planilla compartida de tickets pendientes
(`tickets_entrada_corridaN.csv`, exportada del formulario de contacto de la web). No tiene acceso a la
casilla de mail real, ni a los sistemas de pago, ni a la base de clientes — solo a los datos que
el equipo de atención le pega manualmente para procesar cada tanda.

## Qué puede salir mal y qué pasa cuando sale mal
- **Riesgo:** el agente clasifica mal un reclamo grave como "Consulta de producto" y le manda una
  respuesta final automática en vez de derivarlo. *Mitigación:* la regla de derivación por palabras
  clave ("reembolso", "pago", "devolución de dinero") es independiente de la categoría asignada,
  como capa extra de seguridad — aunque falle la clasificación, esas palabras igual disparan
  revisión humana.
- **Riesgo:** el agente promete un plazo o una excepción que el negocio no puede cumplir.
  *Mitigación:* el system prompt prohíbe explícitamente prometer plazos o excepciones fuera de la
  política de cambios de 30 días.
- **Riesgo conocido y no resuelto del todo:** un mensaje que mezcla queja fuerte con un pedido de
  cambio puede seguir clasificándose mal en casos no probados (ver `DECISIONES.md`). Mientras esto
  no esté resuelto, cualquier ticket marcado "Reclamo" pasa igual por revisión humana antes de
  responder, así que el peor caso es una demora, no una respuesta incorrecta enviada al cliente.

## Qué revisa un humano antes de confiar en la salida
Un miembro del equipo de atención revisa, antes de enviar nada al cliente:
- Todo ticket con `revision_humana: sí`.
- Una muestra al azar (1 de cada 5) de los tickets con `revision_humana: no`, para detectar
  clasificaciones erróneas silenciosas que no dispararon ninguna alerta.

## Niveles de supervisión (L0–L4)
- **L0 — automático:** lectura de la planilla y clasificación inicial de cada ticket.
- **L1 — automático con registro:** redacción de la respuesta borrador o final; queda guardada en
  `corridas/` para poder auditarla después.
- **L2 — revisión humana obligatoria:** cualquier ticket marcado `revision_humana: sí`, antes de
  que se envíe algo al cliente.
- **L3 — revisión humana muestral:** 1 de cada 5 tickets sin marcar, como control de calidad.
- **L4 — firma:** el responsable de atención al cliente es quien firma que la tanda del día fue
  revisada según estas reglas antes de que cualquier respuesta salga por el canal real.
