# Análisis económico

## Costo por corrida (datos reales de `corridas/`)

| Corrida | Tickets procesados | Tokens entrada | Tokens salida |
|---|---|---|---|
| 1 | 4 | 620 | 210 |
| 2 | 3 | 540 | 160 |
| 3 | 3 | 510 | 150 |
| **Promedio** | **~3,3** | **~557** | **~173** |

Se usó un modelo de nivel "mini" (variante liviana del modelo de la familia elegida), con precio
de referencia de **USD 0,25 por millón de tokens de entrada** y **USD 1,25 por millón de tokens de
salida** (rango típico de un modelo mini/liviano actual).

Costo por corrida promedio:
- Entrada: 557 × (0,25 / 1.000.000) ≈ **USD 0,00014**
- Salida: 173 × (1,25 / 1.000.000) ≈ **USD 0,00022**
- **Total por corrida ≈ USD 0,00036**, sobre ~3,3 tickets → **≈ USD 0,0001 por ticket**.

## Proyección a escala

Ropa Norte recibe en promedio 50 consultas por día por el formulario de contacto.

- Por día: 50 tickets × USD 0,0001 ≈ **USD 0,005/día**
- Por semana: ≈ **USD 0,035/semana**
- Por año (365 días): ≈ **USD 1,83/año**

## Elección de modelo, justificada

La tarea es clasificar texto corto en 5 categorías fijas y redactar respuestas breves siguiendo
una plantilla — no requiere razonamiento complejo ni conocimiento especializado. Por el criterio
del curso ("el modelo más chico que hace bien la tarea"), se descartó el modelo frontier más grande
de la familia: en las 3 corridas, el modelo mini clasificó y redactó de forma consistente, con la
única falla real (ticket ambiguo de la corrida 2) que probablemente tampoco se resuelve solo con
más tamaño de modelo, sino con una regla más clara en el prompt (que es lo que se hizo). Subir de
modelo multiplicaría el costo sin evidencia de que resuelva el problema real encontrado.
