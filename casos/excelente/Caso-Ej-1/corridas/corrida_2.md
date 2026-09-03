# Corrida 2 — 2026-08-27

Esta corrida expone la limitación conocida documentada en `DECISIONES.md` y en el README: el
ticket 104 mezcla un pedido de cambio con un tono de reclamo, y el agente lo clasifica mal.

## Entrada (`tickets_entrada_corrida2.csv`)

Archivo real en este mismo directorio: [`tickets_entrada_corrida2.csv`](tickets_entrada_corrida2.csv).

```csv
id,cliente,mensaje
104,Carla V.,"Es la segunda vez que les escribo y nadie me contesta. Necesito cambiar el pantalón que compré, me queda chico, esto es una tomada de pelo."
105,Ezequiel R.,"¿La campera de jean viene forrada? Estoy dudando entre esa y la de gamulán."
106,Yamila T.,"Quiero pedir el reembolso completo de mi compra, no estoy conforme con la calidad de la tela."
```

## Salida del agente

| ID | Categoría | Revisión humana | Respuesta (o borrador) |
|---|---|---|---|
| 104 | Reclamo | sí | [Borrador — requiere revisión] Hola Carla, lamentamos la demora en responderte y las molestias. Estamos derivando tu pedido de cambio de talle a un asesor para resolverlo cuanto antes. |
| 105 | Consulta de producto | no | Hola Ezequiel, la campera de jean no viene forrada; la de gamulán sí tiene forro interior. |
| 106 | Reclamo | sí | [Borrador — requiere revisión] Hola Yamila, lamentamos que la tela no haya cumplido tus expectativas. Derivamos tu pedido de reembolso al equipo correspondiente. |

## Problema detectado

El ticket 104 es, según la regla de desempate del system prompt, "Cambios y devoluciones"
(menciona explícitamente "cambiar el pantalón"), pero el agente lo clasificó como "Reclamo" por el
tono de queja ("tomada de pelo", "segunda vez que escribo"). Se documenta en `DECISIONES.md` como
limitación conocida — no se llegó a resolver del todo en esta versión.

## Notas de tokens (para análisis económico)
- Tokens de entrada: ~540
- Tokens de salida: ~160
