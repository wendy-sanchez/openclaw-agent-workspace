---
name: "get-4geeks-pending"
description: "Retrieve the student's pending 4Geeks exercises grouped by cohort/module"
metadata:
  {
    "openclaw":
      {
        "primaryEnv": "TOKEN_4GEEKS",
        "emoji": "📋",
      },
  }
---

# get-4geeks-pending

## Metadatos

- **Nombre:** get-4geeks-pending
- **Versión:** 1.0.0
- **Autor:** Wendy Sanchez
- **Categoría:** 4Geeks
- **Descripción:**
  Recupera exclusivamente los ejercicios pendientes del estudiante de 4Geeks Academy, agrupados por cohorte/módulo, para facilitar la priorización del estudio diario.

- **Disparadores de activación:**
  - "¿Qué ejercicios me quedan?"
  - "¿Qué tengo pendiente?"
  - "Ejercicios pendientes de 4Geeks"
  - "Muéstrame lo que me falta por hacer"
  - "Get 4geeks pending"

---

## Cuándo utilizar esta skill

Utiliza esta skill cuando Wendy quiera saber qué ejercicios tiene pendientes y organizar su estudio.

Es útil para:

- Saber qué ejercicios debe completar hoy o esta semana.
- Identificar qué módulos tienen más trabajo pendiente.
- Prepararse antes de abordar los proyectos del bootcamp.
- Tener una visión táctica del trabajo diario.

Complementa a `get-4geeks-projects`: mientras esa skill muestra el progreso general (proyectos), esta se centra en las tareas diarias (ejercicios pendientes).

---

## Instrucciones

### Flujo de trabajo

1. Obtén el token de la variable de entorno `TOKEN_4GEEKS`, que OpenClaw inyecta automáticamente al estar configurado en `skills.entries.get-4geeks-pending.env`.

2. Realiza una petición GET a la API:
   ```
   GET https://breathecode.herokuapp.com/v1/assignment/user/me/task?task_type=EXERCISE
   Authorization: Token ${TOKEN_4GEEKS}
   ```
   
   **Nota:** No incluyas el parámetro `limit` para que la API devuelva automáticamente el listado completo sin paginación.

3. Procesa la respuesta:
   - La API devuelve un array plano con todos los ejercicios del estudiante.
   - Filtra únicamente aquellos cuyo `task_status` sea `"PENDING"`.
   - Cada ejercicio pendiente incluye: `title` y `cohort` (con `name`).

4. Agrupa los ejercicios pendientes por cohorte (módulo):

   Para cada cohorte, lista los ejercicios que siguen pendientes.

5. Para cada ejercicio pendiente, muestra:
   - **Título** del ejercicio.

6. Muestra un resumen visual como este:
   ```
   📋 Ejercicios pendientes de 4Geeks
   Total: X pendientes
   
   📚 Nombre del Cohorte (cantidad)
     • Título del ejercicio
     • Título del ejercicio
   
   📚 Otro Cohorte (cantidad)
     • Título del ejercicio
   ```

7. **No muestres** bajo ningún concepto:
   - IDs numéricos internos.
   - `description` del ejercicio.
   - URLs (`github_url`, `live_url`).
   - Fechas (`opened_at`, `read_at`, `reviewed_at`, `delivered_at`).
   - `associated_slug`.
   - Ningún dato personal del estudiante.
   - Proyectos (son responsabilidad de `get-4geeks-projects`).
   - Ejercicios ya completados o aprobados (solo pendientes).

8. Entrega el resultado:
   - **Prioridad 1:** Telegram (si el canal está disponible).
   - **Prioridad 2:** Canal de conversación actual.

---

## Configuración

### Token

Se inyecta mediante OpenClaw desde la variable de entorno `TOKEN_4GEEKS`, configurada en `~/.openclaw/.env` y registrada en `skills.entries.get-4geeks-pending.env` dentro de `openclaw.json`.

### Endpoint

`GET https://breathecode.herokuapp.com/v1/assignment/user/me/task?task_type=EXERCISE`

### Cabecera de autenticación

`Authorization: Token ${TOKEN_4GEEKS}`

---

## Privacidad

Esta skill sigue el principio de mínimo acceso:

- Solo consulta tareas del estudiante autenticado.
- No muestra datos personales ni información sensible.
- Solo muestra ejercicios pendientes; el contenido de ejercicios ya completados queda fuera de su alcance.
- Los IDs internos, descripciones completas y URLs se omiten del resultado.

---

## Dependencias

- Conexión a Internet para alcanzar `breathecode.herokuapp.com`.
- Variable de entorno `TOKEN_4GEEKS` configurada en `~/.openclaw/.env`.
- Configuración en `openclaw.json`:
  ```json5
  {
    skills: {
      entries: {
        "get-4geeks-pending": {
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
