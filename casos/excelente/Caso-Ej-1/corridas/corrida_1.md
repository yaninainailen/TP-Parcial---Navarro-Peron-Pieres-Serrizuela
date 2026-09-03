# Corrida 1 — 2026-08-25

## Entrada (`tickets_entrada_corrida1.csv`)

Archivo real en este mismo directorio: [`tickets_entrada_corrida1.csv`](tickets_entrada_corrida1.csv).

```csv
id,cliente,mensaje
101,Marina G.,"Hola! Compré una campera talle M hace 5 días y todavía no me llegó. El seguimiento dice que sigue en el depósito. ¿Me pueden decir qué pasa?"
102,Luciano P.,"Buenas, ¿tienen el buzo canguro gris en talle XL? En la web me figura sin stock pero quería confirmar antes de pedirlo en otro local."
103,Romina D.,"La remera que compré llegó con un hueco en la manga, nueva. Quiero que me devuelvan la plata, no quiero que me la cambien por otra igual de defectuosa."
104,Nahuel S.,"Todo bien con la compra, gracias!"
```

## Salida del agente

| ID | Categoría | Revisión humana | Respuesta (o borrador) |
|---|---|---|---|
| 101 | Estado de pedido | no | Hola Marina, gracias por escribirnos. Vimos que tu pedido sigue en depósito más tiempo del habitual; ya lo estamos consultando con logística y te confirmamos el estado en las próximas 24hs. |
| 102 | Consulta de producto | no | Hola Luciano, el buzo canguro gris en talle XL está sin stock en este momento en la web. Te avisamos apenas se repone. |
| 103 | Reclamo | sí | [Borrador — requiere revisión] Hola Romina, lamentamos mucho lo sucedido con tu remera. Entendemos que pedís el reembolso en vez de un cambio; lo estamos derivando al equipo correspondiente para resolverlo. |
| 104 | Otro | no | ¡Gracias por tu mensaje, Nahuel! Nos alegra que todo haya salido bien. |

## Notas de tokens (para análisis económico)
- Tokens de entrada (system + user + CSV): ~620
- Tokens de salida: ~210
