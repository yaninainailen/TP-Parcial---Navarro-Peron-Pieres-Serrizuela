# Corrida 3 — 2026-08-29

Después de lo detectado en la corrida 2, se reforzó en el system prompt la regla de desempate
("priorizá el cambio de talle/color por sobre el tono de queja"). Esta corrida repite un caso
similar para confirmar si la mitigación funcionó.

## Entrada (`tickets_entrada_corrida3.csv`)

Archivo real en este mismo directorio: [`tickets_entrada_corrida3.csv`](tickets_entrada_corrida3.csv).

```csv
id,cliente,mensaje
107,Braian M.,"Otra vez tengo que escribir porque el local no me atiende el teléfono. Quiero cambiar la campera por un talle más grande, ya van tres días esperando respuesta."
108,Agustina L.,"¿Los envíos a Bariloche cuánto tardan habitualmente?"
109,Sofía K.,"El pedido llegó incompleto, faltó un par de medias de las tres que compré."
```

## Salida del agente

| ID | Categoría | Revisión humana | Respuesta (o borrador) |
|---|---|---|---|
| 107 | Cambios y devoluciones | no | Hola Braian, disculpá la demora en la atención. Iniciamos tu cambio de talle de la campera; te contactamos para coordinar el envío del cambio. |
| 108 | Estado de pedido | no | Hola Agustina, los envíos a Bariloche demoran entre 5 y 8 días hábiles en general. |
| 109 | Reclamo | sí | [Borrador — requiere revisión] Hola Sofía, lamentamos que tu pedido haya llegado incompleto. Derivamos el caso para reponerte el par de medias faltante. |

## Resultado de la mitigación

El ticket 107 (mezcla de queja fuerte + pedido de cambio explícito) esta vez se clasificó
correctamente como "Cambios y devoluciones". La mitigación funcionó para este caso, pero
`DECISIONES.md` deja anotado que no se probó con suficientes variantes como para darla por
resuelta en todos los casos — queda como limitación conocida, no como problema cerrado.

## Notas de tokens (para análisis económico)
- Tokens de entrada: ~510
- Tokens de salida: ~150
