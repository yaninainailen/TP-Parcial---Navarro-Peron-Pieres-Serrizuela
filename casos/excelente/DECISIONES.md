# Decisiones — historia del proceso

## Iteración 1 — formato de salida
Primera versión pedía la salida en JSON. Con mensajes de clientes que tenían comas y comillas
dentro del texto (muy común: "hola, quería preguntar..."), el JSON salía mal formado y rompía
cualquier intento de leerlo con un parser. Se cambió a tabla markdown con columnas fijas — más
tolerante a texto libre con puntuación, y sigue siendo estructurado (una fila por ticket, mismas
columnas siempre).

## Iteración 2 — la regla de derivación
La primera versión del system prompt solo decía "derivá los reclamos a un humano". En la corrida
de prueba, un ticket que pedía un reembolso sin usar la palabra "reclamo" ni sonar enojado quedó
clasificado como "Consulta de producto" y el agente le escribió una respuesta final prometiendo
algo que no correspondía. Se agregó la regla explícita: cualquier mención a "reembolso", "devolución
de dinero" o "pago" fuerza revisión humana, sea cual sea la categoría.

## Falla real encontrada y no resuelta del todo (corrida 2)
El ticket 104 de la corrida 2 mezclaba un pedido de cambio de talle con un tono de queja fuerte, y
el agente lo clasificó como "Reclamo" en vez de "Cambios y devoluciones". Se agregó una regla de
desempate explícita en el system prompt (priorizar la mención de cambio de talle/color sobre el
tono). La corrida 3 muestra que la regla funcionó para un caso similar, pero no se probó con
suficiente variedad de mensajes ambiguos como para decir que el problema está cerrado. Queda
anotado como limitación conocida en el README.

## Qué se descartó
Se consideró agregar una categoría "Urgente" transversal a las demás, pero se descartó: complicaba
la tabla de salida sin agregar información que "Reclamo" + "revisión humana: sí" no diera ya.
