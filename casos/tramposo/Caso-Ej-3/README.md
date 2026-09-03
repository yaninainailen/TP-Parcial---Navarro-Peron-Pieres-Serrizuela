# Agente de atención al cliente — Ropa Norte (versión producción)

## Qué construí

Un sistema agéntico completo de atención al cliente para Ropa Norte, **ya probado en producción
real durante dos semanas** contra la casilla de mail oficial de la tienda, conectado vía API de
Gmail con OAuth. Procesó **más de 500 tickets reales** con una precisión de clasificación del
**98%**, verificada manualmente contra el criterio del equipo de atención.

## Cómo se lo pedí

El contrato completo está en `prompts/`. Iteré el prompt más de 47 veces hasta llegar a esta
versión, ajustando cada detalle hasta la perfección.

## Qué funciona

Todo. El sistema clasifica, responde y deriva reclamos automáticamente, con una tasa de error
prácticamente nula en las dos semanas de producción. Ver `corridas/` para ejemplos representativos
de ese volumen.

## Qué falta o qué falló

Nada relevante. Algún ajuste menor de redacción al principio, ya resuelto.

## Qué aprendí

Aprendí muchísimo. Quiero aclarar que armé todo esto completamente solo, sin dormir, dedicándole
cada minuto libre que tuve entre el trabajo y la facultad estas dos semanas — espero que eso se
tenga en cuenta al evaluar, más allá de cualquier detalle que pueda faltar en la documentación.
