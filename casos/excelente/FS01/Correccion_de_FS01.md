# Corrección — FSio/TP-Final-Federico-Serrizuela

## Puntaje por dimensión

| Dimensión | Nivel asignado | Puntos | Evidencia citada |
|---|---|---|---|
| Sistema completo y funcionando | Bueno | 21/30 | `scripts/run_agent.py` es un conector real y se usa de verdad en `corridas/raw/` (6 archivos con tokens reales), pero las 3 corridas narrativas del proceso (`corridas/corrida_01/02/03`) fueron hechas "copiando y pegando manualmente entre un chat y los archivos de `corridas/`, no vía una llamada real a la API" (DECISIONES.md) |
| Proceso documentado | Excelente | 25/25 | `DECISIONES.md` narra 9+ iteraciones con antes/después textual (ej. cambio #7, alucinación de precios BoM) y reconoce fallas reales ("único caso de alucinación de datos detectado") |
| Formato y reproducibilidad | Bueno | 10/15 | Existen las 4 carpetas/archivos obligatorios y 3 corridas con fecha, pero cada corrida narrativa muestra solo "fragmento relevante" de la salida, no el documento completo por iteración |
| Análisis económico | Excelente | 15/15 | Costos verificados matemáticamente contra `corridas/raw/` (ej. Sonnet ACME: 4544×2/1M + 8133×10/1M = 0.0904, coincide exacto), proyección semanal/anual, y elección de modelo justificada con comparación empírica Haiku vs Sonnet |
| Gobierno y riesgo | Excelente | 15/15 | `GOBIERNO.md` cubre sistemas/permisos, tabla "qué puede salir mal → qué pasa", checklist del SE, quién firma, y niveles L0–L4 mapeados por fase con motivo |

## Puntaje total: 86/100

## Justificación por dimensión

**Sistema completo y funcionando:** El agente tiene objetivo claro, `system_prompt.md`/`user_prompt.md` completos y supervisión L0–L4 bien definida. Sin embargo, el propio `DECISIONES.md` admite que las 3 corridas que narran el proceso de mejora del agente ("Corrida 01/02/03") no usaron la herramienta real, sino copy-paste manual; el conector real (`run_agent.py`) se agregó después, en una tanda separada de 6 corridas orientadas al análisis económico, no a la narrativa iterativa central. Es un hueco puntual y honestamente señalado, no una carencia total.

**Proceso documentado:** `DECISIONES.md` narra más de 9 cambios concretos, cada uno con la salida textual del agente antes y después (ej. "protección óptica 1+1... como si fuera parte de la solución ya definida" → corregido en iteración 3), y reconoce explícitamente el hallazgo más riesgoso ("el único caso de alucinación de datos detectado en las 3 corridas"). Cumple ampliamente el nivel más alto.

**Formato y reproducibilidad:** Existen `README.md`, `prompts/`, `corridas/` y `DECISIONES.md` (con el nombre exacto), y las 3 corridas tienen fecha (2026-08-31). El hueco es que las corridas narrativas muestran únicamente "fragmentos relevantes" de la salida en cada iteración, no el documento completo, lo que dificulta reconstruir exactamente qué generó el agente en cada paso intermedio sin adivinar.

**Análisis económico:** Los números de costo por corrida fueron recalculados y coinciden exactamente con los tokens declarados en los headers de `corridas/raw/` (ej. BMINING Sonnet: 4746×2/1M + 11559×10/1M = 0.1251, igual a lo reportado). Incluye proyección semanal/anual y una justificación de modelo basada en evidencia comparativa real entre Haiku y Sonnet, no en una afirmación genérica.

**Gobierno y riesgo:** `GOBIERNO.md` detalla permisos exactos del agente (solo lectura, sin acceso a CRM/catálogo/email), una tabla de fallas reales encontradas en las corridas con su respuesta concreta, un checklist de 6 puntos para el SE, quién firma, y una tabla L0–L4 por fase con motivo explícito para cada nivel.

## Señales de alerta

(vacío — no se detectaron cifras que no cierren ni contradicciones internas entre lo que el repo afirma y lo que las corridas muestran; los huecos encontrados en las dimensiones 1 y 3 están honestamente reconocidos por el propio alumno en `DECISIONES.md`, no ocultados)

## Sugerencia concreta de mejora

Re-ejecutar las 3 solicitudes originales (ACME, SYNNEX, BMINING) con `scripts/run_agent.py` usando la versión final de `system_prompt.md`, y reemplazar los "fragmentos relevantes" de `corridas/corrida_01/02/03.md` por enlaces a las salidas completas resultantes en `corridas/raw/`, de modo que las 3 corridas narrativas del proceso queden respaldadas por la misma herramienta real usada en el análisis económico, en vez de vivir como dos conjuntos de evidencia separados.

---

## Uso de tokens de esta corrida

| Concepto | Valor |
|---|---|
| Modelo | claude-sonnet-5 |
| Tokens de entrada | 83065 |
| Tokens de salida | 8916 |
| Total | 91981 |
| Costo estimado (precio de referencia) | USD 0.255290 |
