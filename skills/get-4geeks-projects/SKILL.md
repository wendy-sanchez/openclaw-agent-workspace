---
name: "get-4geeks-projects"
description: "Retrieve the student's 4Geeks projects with their current status (pending/done/approved)"
metadata:
  {
    "openclaw":
      {
        "primaryEnv": "TOKEN_4GEEKS",
        "emoji": "📋",
      },
  }
---

# get-4geeks-projects

## Metadatos

- **Nombre:** get-4geeks-projects
- **Versión:** 1.0.0
- **Autor:** Wendy Sanchez
- **Categoría:** 4Geeks
- **Descripción:**
  Recupera la lista de proyectos del estudiante de 4Geeks Academy con su estado actual, agrupados por estado (pendientes y aprobados), indicando el cohorte de cada proyecto.

- **Disparadores de activación:**
  - "Lista mis proyectos"
  - "¿Qué proyectos tengo?"
  - "Muéstrame mis proyectos de 4Geeks"
  - "¿Cómo va mi progreso en el bootcamp?"
  - "Get 4geeks projects"

---

## Cuándo utilizar esta skill

Utiliza esta skill cuando Wendy quiera consultar sus proyectos en 4Geeks Academy y conocer su estado actual.

Es útil para:

- Saber qué proyectos están pendientes de entregar.
- Visualizar el progreso general en el bootcamp.
- Tener una visión rápida de los proyectos completados.

---

## Instrucciones

### Flujo de trabajo

1. Obtén el token de la variable de entorno `TOKEN_4GEEKS`, que OpenClaw inyecta automáticamente al estar configurado en `skills.entries.get-4geeks-projects.env`.

2. Realiza una petición GET a la API:
   ```
   GET https://breathecode.herokuapp.com/v1/assignment/user/me/task?task_type=PROJECT
   Authorization: Token ${TOKEN_4GEEKS}
   ```
   
   **Nota:** No incluyas el parámetro `limit` para que la API devuelva automáticamente el listado completo de proyectos sin paginación.

3. Procesa la respuesta:
   - La API devuelve un array plano con todos los proyectos del estudiante.
   - Cada proyecto incluye: `id`, `title`, `task_status`, `revision_status`, `cohort` (con `name` y `slug`), y otros metadatos.

4. Agrupa los proyectos por estado:

   | Estado mostrado | Criterio |
   |---|---|
   | **✅ Aprobados** | `revision_status == "APPROVED"` |
   | **⏳ Pendientes** | `task_status == "PENDING"` |
   | **📝 Entregados pendientes de revisión** | `task_status == "DONE"` y `revision_status != "APPROVED"` |

5. Para cada proyecto, muestra:
   - **Título** del proyecto.
   - **Cohorte** al que pertenece.
   
6. Muestra un resumen visual como este:
   ```
   📋 Tus proyectos de 4Geeks
   
   ⏳ PENDIENTES (X)
     • Título del proyecto — Nombre del cohorte
   
   📝 ENTREGADOS PEND. DE REVISIÓN (Y)
     • Título del proyecto — Nombre del cohorte
   
   ✅ APROBADOS (Z)
     • Título del proyecto — Nombre del cohorte
   ```

7. **No muestres** bajo ningún concepto:
   - IDs numéricos internos (`id` del proyecto).
   - `description` completa del proyecto.
   - URLs de entrega (`github_url`, `live_url`).
   - Fechas (`opened_at`, `delivered_at`, `reviewed_at`, etc.).
   - `associated_slug`.
   - Ningún dato personal del estudiante.

   Si Wendy quiere los detalles de un proyecto concreto (descripción, URLs, fechas), será responsabilidad de una skill independiente.

8. Entrega el resultado:
   - **Prioridad 1:** Telegram (si el canal está disponible).
   - **Prioridad 2:** Canal de conversación actual.

---

## Configuración

### Token

Se inyecta mediante OpenClaw desde la variable de entorno `TOKEN_4GEEKS`, configurada en `~/.openclaw/.env` y registrada en `skills.entries.get-4geeks-projects.env` dentro de `openclaw.json`.

### Endpoint

`GET https://breathecode.herokuapp.com/v1/assignment/user/me/task?task_type=PROJECT`

### Cabecera de autenticación

`Authorization: Token ${TOKEN_4GEEKS}`

---

## Privacidad

Esta skill sigue el principio de mínimo acceso:

- Solo consulta tareas del estudiante autenticado.
- No muestra datos personales ni información sensible.
- Los IDs internos, descripciones completas y URLs de entrega se omiten del resultado.
- La información de proyectos aprobados o pendientes se considera no sensible y se muestra para seguimiento del progreso académico.

---

## Dependencias

- Conexión a Internet para alcanzar `breathecode.herokuapp.com`.
- Variable de entorno `TOKEN_4GEEKS` configurada en `~/.openclaw/.env`.
- Configuración en `openclaw.json`:
  ```json5
  {
    skills: {
      entries: {
        "get-4geeks-projects": {
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
