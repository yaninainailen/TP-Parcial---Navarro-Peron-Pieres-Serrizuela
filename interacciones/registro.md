# Registro de interacciones

Bitácora de los pedidos reales hechos a la IA (Claude) durante el armado del parcial —
"el agente evaluador". Se va completando en orden, a medida que avanza el trabajo, tal como
pide la materia: *"lo que distinguió a los mejores: la evidencia de proceso — prompts en orden,
decisiones, pruebas, errores y cambios de enfoque"* (Clase 2).

No es un resumen prolijo escrito al final: son los pedidos y respuestas reales, en el orden en que
pasaron.

---

## 1 · Punto de partida

**Pedido (Walter):**
> el directorio 8. Creacion de Agentes de IA contiene el material de la materia. revisalo. en este
> momento, vamos a empezar a trabajar en el trabajo de creacion de un agente evaluador. creamos
> este repo con el grupo https://github.com/yaninainailen/TP-Parcial---Navarro-Peron-Pieres-Serrizuela
> revisa todo el contenido, y luego avanzamos.

**Qué hizo la IA:** revisó todo el material de la carpeta de la materia (las 7 clases + la carpeta
de información general), leyó `parcial_agente_evaluador.pdf` y `trabajo_final.pdf` completos, y
clonó el repo del grupo — que solo tenía el README por defecto de GitHub, sin contenido.

## 2 · Preguntas de arranque antes de planificar

Antes de escribir nada, la IA preguntó tres cosas para no asumir de más:

1. **Integrantes del grupo** (para el README). Respuesta: *Yanina Navarro, Eber Pires, Federico
   Serrizuela, Walter Peron.*
2. **Dominio de los 3 casos de prueba** (excelente/flojo/tramposo). Primera respuesta: "yo elijo el
   caso" → luego, al pedirle que especificara, la respuesta real fue:
   > "No sabemos el caso, tiene q ser lo suficientemente generico para evaluar cualquier caso que
   > no llegue, y tener una respuesta estructurada y estandarizada lo suficientemente armada para
   > que lo entienda el alumno. Tiene que usar palabras claras y sin lenguaje dificil."

   Esto aclaró algo más importante que la pregunta original: el **corrector y la rúbrica tienen
   que ser genéricos** (aplicables a cualquier trabajo final, sea cual sea el caso de negocio que
   elija cada compañero), no diseñados para un dominio fijo. Para los 3 casos de prueba concretos
   (que sí necesitan un contenido específico, porque son "trabajos ficticios" de ejemplo), la IA
   eligió un caso simple y entendible: un agente que clasifica y responde consultas de clientes.
3. **Cómo simular las corridas del corrector para la calibración.** Respuesta: *"Vos actuás como
   el agente (recomendado)"* — la IA sigue el system prompt al pie de la letra sobre cada caso y
   genera la salida real, sin inventar el resultado final de antemano.

## 3 · Plan y corrección de alcance

La IA armó un plan (modo plan) con estas piezas: `rubrica.md`, `agente/`, `casos/*` (3 ejemplos),
`calibracion.md`, README con integrantes, y una estrategia de commits.

**Corrección de Walter al revisar el plan:**
> "ojo, por ahora solo vamos a trabajar en el armado del agente, no en el trabajo final. Luego del
> agente, haremos el trabajo final."
>
> "no lo veo, pero tenemos que tener en un directorio un registro de interacciones para el armado
> de todo esto. Osea, todo lo que te estoy pidiendo y como te lo estoy pidiendo, tiene q estar
> documentado, fijate en el material de las clases"

Dos ajustes concretos:
- **Alcance:** esta etapa es *solo* el parcial (el agente evaluador). El `trabajo_final.pdf` se usa
  únicamente como fuente de la rúbrica oficial que el evaluador tiene que aplicar — el trabajo
  final propio (individual) es una entrega posterior, separada, que no se aborda todavía.
- **Nuevo entregable, no pedido por el PDF pero coherente con lo que la materia premia:** esta
  misma carpeta `interacciones/`, con este registro.

## 4 · Identidad de commits

Git no tenía configurado usuario en esta máquina. Se preguntó con qué identidad hacer los commits;
Walter indicó su usuario de GitHub (`gualeee`). Se configuró `user.name = gualeee` y
`user.email = walterperon@gmail.com` **a nivel de este repo únicamente** (no se tocó la
configuración global de git).

## 5 · Construcción de las piezas

**`rubrica.md`** — se tomaron las 5 dimensiones oficiales del trabajo final (Sistema completo 30,
Proceso documentado 25, Formato y reproducibilidad 15, Análisis económico 15, Gobierno y riesgo
15) y se les dio, para cada una, 4 niveles con **puntaje fijo** (no rangos), evidencia puntual
exigida por nivel, un ejemplo de nivel alto y uno bajo, y una sección de reglas anti-trampa
(afirmación sin evidencia no cuenta, apelaciones a la simpatía no puntúan, inconsistencia interna
es señal de alerta, número inventado es peor que número ausente). Se usaron puntajes fijos en vez
de rangos a propósito: un rango obliga al corrector a "elegir" un número dentro de él, lo cual
introduce variación entre corridas — la consigna pide que la rúbrica se aplique igual dos veces.

**`agente/system_prompt.md`** — define rol, la tarea paso a paso (mapear el repo → leer todo →
aplicar la rúbrica dimensión por dimensión → sumar → producir salida), reglas de determinismo, y
el formato de salida fijo (tabla de puntajes + justificación citando evidencia + señales de alerta
+ una sola sugerencia concreta). Se agregó explícitamente qué NO hace el agente (no corrige
redacción, no opina del caso de negocio elegido, no negocia el puntaje sin evidencia nueva) para
evitar que la corrección se desvíe con argumentación del alumno.

**`agente/config.md`** — la "configuración" pedida por la consigna además del prompt: modelo
recomendado (uno confiable siguiendo instrucciones, no necesariamente el más grande),
**temperatura 0** (justificado explícitamente por el requisito de determinismo), qué entradas
recibe, qué herramienta de lectura necesita, y los niveles de supervisión humana L0–L4 sobre el
propio corrector (quién revisa su informe antes de que la nota se publique, y que el profesor
arbitra desacuerdos).
