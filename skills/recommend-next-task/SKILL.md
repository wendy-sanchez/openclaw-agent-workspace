---
name: "recommend-next-task"
description: "Recommend the next most urgent task for the student based on API data analysis"
metadata:
  {
    "openclaw":
      {
        "primaryEnv": "TOKEN_4GEEKS",
        "emoji": "🎯",
      },
  }
---

# recommend-next-task

## Metadatos

- **Nombre:** recommend-next-task
- **Versión:** 1.0.0
- **Autor:** Wendy Sanchez
- **Categoría:** 4Geeks
- **Descripción:**
  Analiza el estado del estudiante en 4Geeks Academy y recomienda una única acción prioritaria para continuar avanzando en el bootcamp, actuando como un mentor académico.

- **Disparadores de activación:**
  - "¿Qué debería hacer ahora?"
  - "Recomiéndame mi siguiente tarea"
  - "¿Cuál es mi prioridad?"
  - "¿Por dónde sigo?"
  - "Recommend next task"

---

## Cuándo utilizar esta skill

Utiliza esta skill cuando Wendy quiera una recomendación concreta sobre cuál debería ser su siguiente paso en el bootcamp.

Es útil para:

- Tener claridad sobre qué hacer a continuación.
- Identificar qué módulos están bloqueando el avance.
- Priorizar el trabajo eficientemente.
- Recibir una recomendación justificada sin tener que analizar manualmente el estado.

Complementa a `get-4geeks-projects`, `get-4geeks-pending` y `get-4geeks-progress`: mientras esas skills muestran información, esta la interpreta para recomendar una acción.

---

## Instrucciones

### Flujo de trabajo

1. Obtén el token de la variable de entorno `TOKEN_4GEEKS`, que OpenClaw inyecta automáticamente al estar configurado en `skills.entries.recommend-next-task.env`.

2. Realiza una única petición GET a la API:
   ```
   GET https://breathecode.herokuapp.com/v1/assignment/user/me/task
   Authorization: Token ${TOKEN_4GEEKS}
   ```
   
   **Nota:** No incluyas el parámetro `limit` para obtener el listado completo sin paginación. Tampoco filtres por `task_type` para obtener todas las categorías en una sola llamada.

3. Procesa la respuesta y clasifica los items por cohorte y tipo:

   La API devuelve un array plano. Para cada item, extrae:
   - `title` — título de la tarea
   - `task_type` — PROJECT, EXERCISE o LESSON
   - `task_status` — PENDING o DONE
   - `cohort.name` — nombre del cohorte/módulo

4. Aplica el algoritmo de recomendación:

   **Paso 1 — Identificar cohortes con bloqueo activo:**
   Busca cohortes que tengan simultáneamente ejercicios pendientes (`task_type == "EXERCISE"` y `task_status == "PENDING"`) Y proyectos pendientes (`task_type == "PROJECT"` y `task_status == "PENDING"`).

   De estos, calcula un score para cada uno:
   ```
   score = (ejercicios_pendientes * 1) + (proyectos_pendientes * 5) + 15
   ```
   El score más alto indica el cohorte con mayor urgencia.

   Si hay empate, prioriza el que tenga MENOS ejercicios pendientes (quick win).

   **Paso 2 — Si no hay bloqueos activos:**
   Busca cohortes que tengan solo proyectos pendientes (ejercicios ya completados). Recomienda entregar el proyecto pendiente.

   **Paso 3 — Si no hay proyectos pendientes:**
   Busca cohortes con solo ejercicios pendientes. Recomienda el que tenga menos ejercicios para cerrar módulos rápidamente.

5. Construye la recomendación siguiendo este formato:

   ```
   🎯 Recomendación de hoy
   
   Continúa con el módulo:
   
   📚 [Nombre del Cohorte]
   
   Motivo:
   • Tienes X ejercicios pendientes en este módulo.
   • Al completarlos desbloquearás Y proyectos del módulo.
   • [Frase motivacional personalizada basada en el caso]
   ```

   Frases motivacionales según el caso:
   - Si bloquea proyectos: "Finalizar este módulo te permitirá avanzar al siguiente bloque del bootcamp."
   - Si es un quick win: "Completar este módulo te dará impulso para seguir avanzando."
   - Si solo tiene ejercicios: "Cerrar este módulo te acercará a completar el bootcamp."

6. **No muestres** bajo ningún concepto:
   - Listados completos de ejercicios o proyectos pendientes.
   - Títulos individuales de cada tarea.
   - IDs numéricos internos.
   - Fechas.
   - URLs.
   - Ningún dato personal del estudiante.
   - Información detallada que ya cubren `get-4geeks-projects`, `get-4geeks-pending` o `get-4geeks-progress`.

7. Entrega el resultado:
   - **Prioridad 1:** Telegram (si el canal está disponible).
   - **Prioridad 2:** Canal de conversación actual.

---

## Configuración

### Token

Se inyecta mediante OpenClaw desde la variable de entorno `TOKEN_4GEEKS`, configurada en `~/.openclaw/.env` y registrada en `skills.entries.recommend-next-task.env` dentro de `openclaw.json`.

### Endpoint

`GET https://breathecode.herokuapp.com/v1/assignment/user/me/task`

### Cabecera de autenticación

`Authorization: Token ${TOKEN_4GEEKS}`

---

## Privacidad

Esta skill sigue el principio de mínimo acceso:

- Solo consulta tareas del estudiante autenticado.
- No muestra datos personales ni información sensible.
- Solo muestra métricas agregadas necesarias para justificar la recomendación.
- No expone listados completos de tareas pendientes.

---

## Dependencias

- Conexión a Internet para alcanzar `breathecode.herokuapp.com`.
- Variable de entorno `TOKEN_4GEEKS` configurada en `~/.openclaw/.env`.
- Configuración en `openclaw.json`:
  ```json5
  {
    skills: {
      entries: {
        "recommend-next-task": {
          enabled: true,
          env: {
            TOKEN_4GEEKS: "${TOKEN_4GEEKS}"
          }
        }
      }
    }
  }
  ```
- Canales de entrega: Telegram (preferente) o canal actual.
