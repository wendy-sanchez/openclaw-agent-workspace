---
name: "discover-career-paths"
description: "Estimate professional career affinity based on completed bootcamp modules"
metadata:
  {
    "openclaw":
      {
        "primaryEnv": "TOKEN_4GEEKS",
        "emoji": "🚀",
      },
  }
---

# discover-career-paths

## Metadatos

- **Nombre:** discover-career-paths
- **Versión:** 1.0.0
- **Autor:** Wendy Sanchez
- **Categoría:** 4Geeks
- **Descripción:**
  Estima la afinidad del estudiante hacia distintos perfiles profesionales (Frontend, Backend, AI, Full Stack, DevOps) basándose en los módulos completados durante el bootcamp.

- **Disparadores de activación:**
  - "¿A qué perfil profesional me acerco?"
  - "Descubre mis áreas profesionales afines"
  - "¿Qué tipo de desarrolladora estoy siendo?"
  - "¿Hacia qué perfil me orienta el bootcamp?"
  - "Discover career paths"

---

## Cuándo utilizar esta skill

Utiliza esta skill cuando Wendy quiera entender hacia qué perfiles profesionales se está aproximando según los módulos que ha completado en el bootcamp.

Es útil para:

- Reflexionar sobre la dirección profesional.
- Identificar fortalezas y áreas de mejora.
- Preparar conversaciones de orientación profesional.
- Planificar estudios complementarios después del bootcamp.

Complementa a `recommend-next-task`: mientras esa skill dice qué hacer ahora, esta dice hacia dónde te dirige el bootcamp.

---

## Instrucciones

### Flujo de trabajo

1. Obtén el token de la variable de entorno `TOKEN_4GEEKS`, que OpenClaw inyecta automáticamente al estar configurado en `skills.entries.discover-career-paths.env`.

2. Realiza una única petición GET a la API:
   ```
   GET https://breathecode.herokuapp.com/v1/assignment/user/me/task
   Authorization: Token ${TOKEN_4GEEKS}
   ```
   
   **Nota:** No incluyas el parámetro `limit` ni filtres por `task_type`.

3. Procesa la respuesta: identifica los módulos (cohortes) en los que el estudiante ha completado al menos una tarea (`task_status == "DONE"`), y calcula el porcentaje de completado de cada módulo.

4. Aplica el mapa de competencias. Cada módulo del bootcamp tiene pesos predefinidos hacia distintos perfiles profesionales. Un módulo completamente terminado aporta su peso completo; un módulo parcialmente completado aporta el peso proporcional a su porcentaje de avance.

   **Mapa de pesos por módulo:**

   | Módulo | Frontend | Backend | AI App Dev | Full Stack | DevOps/Infra |
   |---|---|---|---|---|---|
   | Web UI fundamentals with Tailwind | 0.40 | 0.00 | 0.00 | 0.25 | 0.00 |
   | Coding Fundamentals with Typescript | 0.25 | 0.10 | 0.00 | 0.25 | 0.00 |
   | Frontend development with Coding Agents | 0.35 | 0.05 | 0.15 | 0.25 | 0.00 |
   | Command Line, Git and Github | 0.05 | 0.10 | 0.05 | 0.10 | 0.25 |
   | Coding fundamentals with Python | 0.00 | 0.20 | 0.15 | 0.15 | 0.00 |
   | Managing relational databases with FastAPI | 0.00 | 0.35 | 0.10 | 0.20 | 0.10 |
   | Authentication in web applications | 0.05 | 0.25 | 0.05 | 0.20 | 0.05 |
   | Error handling, debugging and testing | 0.05 | 0.20 | 0.05 | 0.15 | 0.20 |
   | Personal assistants with Openclaw | 0.00 | 0.00 | 0.30 | 0.10 | 0.00 |
   | Advanced personal assistants with Openclaw | 0.00 | 0.05 | 0.35 | 0.15 | 0.00 |
   | Working with AI coding agents | 0.00 | 0.05 | 0.30 | 0.15 | 0.05 |
   | AI Engineering Introduction | 0.00 | 0.05 | 0.25 | 0.10 | 0.05 |
   | Container applications with Docker | 0.00 | 0.10 | 0.05 | 0.10 | 0.35 |
   | Agentic Engineering | 0.00 | 0.05 | 0.30 | 0.10 | 0.05 |
   | Agentic Workflows | 0.00 | 0.05 | 0.30 | 0.10 | 0.05 |
   | Real-Time Communication | 0.05 | 0.15 | 0.05 | 0.15 | 0.05 |
   | Secure AI Applications | 0.00 | 0.15 | 0.15 | 0.10 | 0.10 |
   | Architecture optimization | 0.00 | 0.10 | 0.05 | 0.10 | 0.20 |
   | Application telemetry | 0.00 | 0.10 | 0.05 | 0.10 | 0.20 |
   | Backend development with Coding Agents | 0.00 | 0.30 | 0.15 | 0.25 | 0.05 |
   | Implementing Data Pipelines | 0.00 | 0.15 | 0.15 | 0.10 | 0.10 |
   | Asynchronous processing and offloading | 0.00 | 0.15 | 0.10 | 0.10 | 0.10 |
   | Models training & RAG | 0.00 | 0.10 | 0.35 | 0.10 | 0.10 |

5. Calcula la afinidad para cada perfil:

   ```
   peso_obtenido = suma de (peso_del_modulo_para_este_perfil * %_completado_del_modulo)
   peso_maximo = suma de todos los pesos posibles para este perfil (con módulos al 100%)
   afinidad = round(peso_obtenido / peso_maximo * 100)
   ```

6. Ordena los perfiles de mayor a menor afinidad. Muestra los 5 perfiles con su afinidad, usando un indicador visual de nivel:

   - 🟢 Afinidad ≥ 60%
   - 🟡 Afinidad entre 30% y 59%
   - 🟠 Afinidad entre 10% y 29%
   - ⚪ Afinidad < 10%

7. Para cada perfil, genera un motivo basado en los módulos con mayor contribución a ese perfil. Si la afinidad es baja (🟠 o ⚪), sugiere áreas de conocimiento que podrían reforzar ese perfil.

8. Sigue este formato de salida:

   ```
   🚀 Áreas profesionales afines
   
   Basado en los módulos que has completado del bootcamp:
   
   🟢 Frontend Developer
     Afinidad estimada: 72%
     • Has completado módulos de Tailwind, React, TypeScript y diseño UI.
   
   🟡 AI Application Developer
     Afinidad estimada: 58%
     • Has trabajado con OpenClaw, agentes de IA y automatización de procesos.
   
   🟠 Backend Developer
     Afinidad estimada: 35%
     • Para reforzar este perfil: APIs, bases de datos, autenticación, Docker.
   
   ⚪ DevOps / Infraestructura
     Afinidad estimada: 12%
     • Para reforzar este perfil: Docker, pipelines CI/CD, testing, cloud.
   
   📊 Afinidades calculadas según los módulos del bootcamp completados.
     Son estimaciones orientativas basadas en tu progreso actual, no certificaciones.
   ```

9. **No uses** bajo ningún concepto:
   - Frases categóricas como "eres un X Developer" o "ya eres Y".
   - Listados de tareas individuales.
   - Nombres de proyectos o ejercicios específicos.
   - IDs, fechas o URLs.
   - Datos personales del estudiante.
   - Recomendaciones de qué estudiar a continuación (eso es `recommend-next-task`).
   - Afirmaciones sobre empleabilidad o salarios.

   Usa siempre lenguaje prudente y orientativo: "afinidad estimada", "basado en módulos completados", "estimación orientativa".

10. Entrega el resultado:
    - **Prioridad 1:** Telegram (si el canal está disponible).
    - **Prioridad 2:** Canal de conversación actual.

---

## Configuración

### Token

Se inyecta mediante OpenClaw desde la variable de entorno `TOKEN_4GEEKS`, configurada en `~/.openclaw/.env` y registrada en `skills.entries.discover-career-paths.env` dentro de `openclaw.json`.

### Endpoint

`GET https://breathecode.herokuapp.com/v1/assignment/user/me/task`

### Cabecera de autenticación

`Authorization: Token ${TOKEN_4GEEKS}`

---

## Privacidad

Esta skill sigue el principio de mínimo acceso:

- Solo consulta tareas del estudiante autenticado.
- No muestra datos personales ni información sensible.
- Solo utiliza nombres de módulos y métricas de completado agregadas.
- Las afinidades son estimaciones orientativas, no diagnósticos vinculantes.

---

## Dependencias

- Conexión a Internet para alcanzar `breathecode.herokuapp.com`.
- Variable de entorno `TOKEN_4GEEKS` configurada en `~/.openclaw/.env`.
- Configuración en `openclaw.json`:
  ```json5
  {
    skills: {
      entries: {
        "discover-career-paths": {
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
