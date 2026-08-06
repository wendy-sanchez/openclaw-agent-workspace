---
name: "get-4geeks-progress"
description: "Show a general bootcamp progress dashboard with visual bar and metrics"
metadata:
  {
    "openclaw":
      {
        "primaryEnv": "TOKEN_4GEEKS",
        "emoji": "📈",
      },
  }
---

# get-4geeks-progress

## Metadatos

- **Nombre:** get-4geeks-progress
- **Versión:** 1.0.0
- **Autor:** Wendy Sanchez
- **Categoría:** 4Geeks
- **Descripción:**
  Muestra un panel de control visual con el progreso general del bootcamp, incluyendo una barra de progreso con 10 círculos, porcentaje global y desglose por categoría (proyectos, ejercicios, lecciones).

- **Disparadores de activación:**
  - "¿Cómo va mi progreso?"
  - "¿Qué porcentaje llevo del bootcamp?"
  - "Muéstrame mi progreso general"
  - "Panel de control de 4Geeks"
  - "Get 4geeks progress"

---

## Cuándo utilizar esta skill

Utiliza esta skill cuando Wendy quiera conocer de un vistazo su progreso global en el bootcamp de 4Geeks Academy.

Es útil para:

- Tener una visión general del avance en el bootcamp.
- Motivar el estudio al ver el progreso visual.
- Saber en qué áreas hay más trabajo pendiente.
- Compartir el progreso en redes o con su tutora.

Esta skill ofrece una visión global y complementa a `get-4geeks-projects` y `get-4geeks-pending`, que muestran el detalle específico.

---

## Instrucciones

### Flujo de trabajo

1. Obtén el token de la variable de entorno `TOKEN_4GEEKS`, que OpenClaw inyecta automáticamente al estar configurado en `skills.entries.get-4geeks-progress.env`.

2. Realiza una única petición GET a la API:
   ```
   GET https://breathecode.herokuapp.com/v1/assignment/user/me/task
   Authorization: Token ${TOKEN_4GEEKS}
   ```
   
   **Nota:** No incluyas el parámetro `limit` para obtener el listado completo sin paginación. Tampoco filtres por `task_type` para obtener todas las categorías en una sola llamada.

3. Procesa la respuesta:
   - La API devuelve un array plano con todos los items del estudiante.
   - Cada item incluye `task_type` (PROJECT, EXERCISE, LESSON) y `task_status` (PENDING, DONE).

4. Clasifica los items por categoría y calcula:

   | Categoría | Filtro `task_type` | Criterio de completado |
   |---|---|---|
   | 📋 Proyectos | `PROJECT` | `task_status == "DONE"` |
   | 💻 Ejercicios | `EXERCISE` | `task_status == "DONE"` |
   | 📖 Lecciones | `LESSON` | `task_status == "DONE"` |

5. Calcula las métricas globales:
   ```
   total_items = suma de todos los items
   completados = suma de items con task_status == "DONE"
   progreso_global = floor(completados / total_items * 100)
   ```

6. Construye la barra de progreso visual con 10 círculos:
   ```
   circulos_rellenos = progreso_global // 10
   circulos_vacios = 10 - circulos_rellenos
   
   # Usar 🟢 para relleno, ⚪ para vacío
   # Ejemplo con 73%: 🟢🟢🟢🟢🟢🟢🟢⚪⚪⚪  73%
   ```

7. Muestra el resultado siguiendo este formato exacto:
   ```
   📈 Progreso general del Bootcamp
   
   🟢🟢🟢🟢🟢🟢🟢⚪⚪⚪  73%
   
   📋 Proyectos:   24/29  (83%)
   💻 Ejercicios:  66/96  (69%)
   📖 Lecciones:   12/14  (86%)
   
   📊 Total: 102 completados · 37 pendientes
   ```

8. **No muestres** bajo ningún concepto:
   - Títulos individuales de proyectos, ejercicios o lecciones.
   - Nombres de cohortes.
   - IDs numéricos internos.
   - Fechas.
   - URLs.
   - Ningún dato personal del estudiante.
   - Listados detallados (esa es responsabilidad de `get-4geeks-projects` y `get-4geeks-pending`).

9. Entrega el resultado:
   - **Prioridad 1:** Telegram (si el canal está disponible).
   - **Prioridad 2:** Canal de conversación actual.

---

## Configuración

### Token

Se inyecta mediante OpenClaw desde la variable de entorno `TOKEN_4GEEKS`, configurada en `~/.openclaw/.env` y registrada en `skills.entries.get-4geeks-progress.env` dentro de `openclaw.json`.

### Endpoint

`GET https://breathecode.herokuapp.com/v1/assignment/user/me/task`

### Cabecera de autenticación

`Authorization: Token ${TOKEN_4GEEKS}`

---

## Privacidad

Esta skill sigue el principio de mínimo acceso:

- Solo consulta tareas del estudiante autenticado.
- No muestra datos personales ni información sensible.
- Solo muestra métricas agregadas (totales y porcentajes), nunca contenido individual.
- Los títulos de tareas, cohortes e IDs internos se omiten del resultado.

---

## Dependencias

- Conexión a Internet para alcanzar `breathecode.herokuapp.com`.
- Variable de entorno `TOKEN_4GEEKS` configurada en `~/.openclaw/.env`.
- Configuración en `openclaw.json`:
  ```json5
  {
    skills: {
      entries: {
        "get-4geeks-progress": {
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
