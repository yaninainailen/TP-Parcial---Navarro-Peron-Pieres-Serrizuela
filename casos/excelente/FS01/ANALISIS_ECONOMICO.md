# Análisis económico — TP Final

## Cómo se mide

`scripts/run_agent.py` es la única fuente de estos números: llama a la API de Anthropic con `prompts/system_prompt.md` como system prompt y un caso de `prompts/casos/` como input, y guarda `usage.input_tokens` / `usage.output_tokens` reales de la respuesta en la cabecera de cada archivo de `corridas/raw/`. No se estiman tokens contando palabras — el propio agente tiene la restricción de no inventar cifras (BoM), y este análisis sigue el mismo criterio.

Los 6 archivos fuente de esta tabla (3 casos × 2 modelos) están en `corridas/raw/`, con fecha del **2026-09-01**.

Para reproducirlo:

```bash
pip install -r scripts/requirements.txt
export ANTHROPIC_API_KEY=sk-ant-...
python scripts/run_agent.py prompts/casos/solicitud_001_acme.md --model claude-sonnet-5
```

Nota operativa encontrada al correrlo por primera vez: con `max_tokens` bajo (4096), Sonnet 5 —que piensa por defecto (`thinking: adaptive`)— gastaba casi todo el presupuesto en razonamiento y devolvía casi nada de texto visible en 2 de los 3 casos. Se subió `max_tokens` a 16000 por defecto (configurable con `--max-tokens`) y se pasó a `client.messages.stream(...)` para evitar el error del SDK de Anthropic sobre streaming obligatorio en respuestas largas. El caso BMINING (el más largo, pide BoM + interconexión) necesitó `--max-tokens 28000` para no truncarse. Detalle completo en `DECISIONES.md`.

## Precios usados (API de Anthropic, USD por millón de tokens)

| Modelo | Input | Output |
| --- | --- | --- |
| `claude-haiku-4-5` | $1.00 | $5.00 |
| `claude-sonnet-5` | $2.00 | $10.00 |

## Costo por corrida (datos reales, `corridas/raw/`)

| Caso | Modelo | Tokens in | Tokens out | Costo (USD) |
| --- | --- | --- | --- | --- |
| Solicitud 001 (ACME) | `claude-sonnet-5` | 4544 | 8133 | 0.0904 |
| Solicitud 002 (SYNNEX) | `claude-sonnet-5` | 4622 | 12611 | 0.1354 |
| Solicitud 003 (BMINING) | `claude-sonnet-5` | 4746 | 11559 | 0.1251 |
| **Promedio** | `claude-sonnet-5` | **4637** | **10768** | **0.1170** |
| Solicitud 001 (ACME) | `claude-haiku-4-5` | 3423 | 5173 | 0.0293 |
| Solicitud 002 (SYNNEX) | `claude-haiku-4-5` | 3472 | 8499 | 0.0460 |
| Solicitud 003 (BMINING) | `claude-haiku-4-5` | 3564 | 3757* | 0.0223* |
| **Promedio** | `claude-haiku-4-5` | **3486** | **5810** | **0.0325** |

\* La corrida de Haiku en BMINING no es comparable en tamaño: el modelo se detuvo después de la Fase 2 (ver siguiente sección) en vez de completar el flujo de 6 fases, así que su output es más corto porque hizo menos trabajo, no porque haya sido más eficiente.

## Elección de modelo — "el más chico que hace bien la tarea"

Haiku 4.5 es ~2.5-3× más barato que Sonnet 5 en input/output, así que era el candidato natural por costo. Se corrieron los 3 casos con ambos modelos, mismo `system_prompt.md`, y se revisó el output con el mismo criterio que las 3 corridas de `corridas/`: ¿respeta el Formato de salida fijo?, ¿distingue hechos de supuestos?, ¿aplica las restricciones (protección no asumida, plataforma del cliente citada, sin precios inventados)?, ¿ejecuta las fases que la información permite?

**Resultado: Haiku 4.5 no sostiene el contrato en el caso más exigente, y ya se desvía del formato fijo desde el caso más simple.**

- **Formato de salida:** el `system_prompt.md` define una plantilla fija por sección (`# Requisitos del cliente`, `# Clasificación de la información`, etc.). Sonnet 5 la respeta letra por letra en los 3 casos (`corridas/raw/solicitud_003_bmining_claude-sonnet-5_*.md`). Haiku 4.5 la reemplaza por su propia organización desde el caso más simple (ACME): agrupa todo bajo encabezados `# Fase 1` / `# Fase 2` con subtítulos y tablas propias, en vez de las secciones exactas del contrato (`corridas/raw/solicitud_001_acme_claude-haiku-4-5_*.md`, línea 21 en adelante). Como la corrección de este trabajo la hace un agente que "no improvisa" sobre la estructura, esta desviación no es cosmética: un parser que espere los encabezados exactos del contrato puede no encontrar las secciones.
- **Completar las fases con la información disponible:** en el caso BMINING (el más incompleto y con más pedidos: BoM con precios, alternativas de interconexión, interfaces sin definir), la restricción del `system_prompt.md` es completar las fases posibles dejando huecos y supuestos declarados — no rendirse. Sonnet 5 ejecuta las 6 fases completas, marca "Parcialmente listo" para una estimación ROM, y dejó explícito que el BoM con precios reales queda pendiente de Producto/Pricing (`corridas/raw/solicitud_003_bmining_claude-sonnet-5_*.md`). Haiku 4.5, con la misma información, se detiene después de la Fase 2 y le devuelve la pelota al SE ("Fases pendientes ⏸️"), sin producir Fase 3 (alternativas), sin clasificar la oportunidad en Fase 4, y sin nota de ingeniería (`corridas/raw/solicitud_003_bmining_claude-haiku-4-5_*.md`) — exactamente el trabajo de descubrimiento que el contrato pide no perder.
- **Dato positivo de Haiku:** en los 3 casos respeta las restricciones de contenido que sí siguió aplicando (no asume esquema de protección, no inventa precios) — el problema no es que "alucine", es que no sostiene la estructura ni la completitud del flujo bajo el mismo contrato.

**Conclusión:** se elige `claude-sonnet-5` como modelo del sistema, no por ser el más capaz disponible, sino porque es el modelo más chico de los dos probados que efectivamente cumple el contrato completo (formato + completitud de fases) en el caso más exigente. Haiku 4.5 es candidato razonable para una versión futura del sistema si se relaja la exigencia de formato estricto (por ejemplo, agregando post-procesamiento que normalice la salida), pero no tal como está el contrato hoy.

## Proyección de costo (semanal / anual)

**Supuesto de volumen** (a ajustar si no representa el caso real): un SE procesa aproximadamente **5 oportunidades nuevas por semana** que ameritan correr el flujo completo (Fases 1–6), más una cantidad similar de re-corridas por iteración dentro de la misma oportunidad (información que llega incompleta y se reprocesa) — estimar un **factor de 2 corridas por oportunidad**.

Con el costo promedio real de `claude-sonnet-5` (USD 0.1170 por corrida):

| Período | Corridas | Costo estimado (USD) |
| --- | --- | --- |
| Por semana | 5 oportunidades × 2 corridas = 10 | 10 × 0.1170 = **1.17** |
| Por año (48 semanas hábiles) | 480 | 480 × 0.1170 = **56.16** |

Esta proyección no incluye el costo de un eventual SE humano revisando cada salida (tiempo de revisión), que es el costo dominante del sistema en producción — el agente reduce el tiempo de primer borrador, no lo reemplaza (ver [`GOBIERNO.md`](GOBIERNO.md)). A ese volumen, el costo de API es marginal frente al costo del tiempo del SE: la justificación económica del sistema está en el tiempo de borrador ahorrado, no en el costo de tokens en sí.
