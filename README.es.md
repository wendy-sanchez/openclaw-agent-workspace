# OpenClaw Agent Workspace

Un entorno de trabajo para un agente de inteligencia artificial personalizado, desarrollado con [OpenClaw](https://openclaw.ai/), centrado en skills personalizadas, integraciones con APIs, identidad del agente y flujos de trabajo especializados.

## Descripción general

Este proyecto explora cómo ampliar las capacidades de un asistente de IA más allá de la conversación general mediante el diseño de skills especializadas con responsabilidades claramente definidas.

El entorno combina dos áreas principales:

- **Integraciones con APIs:** Skills personalizadas que conectan OpenClaw con la API de estudiantes de 4Geeks Academy.
- **Flujos de trabajo personalizados:** Configuración y diseño de skills para facilitar el aprendizaje, organizar conocimiento y crear contenido técnico.

El objetivo es construir un asistente modular capaz de recuperar información relevante, apoyar procesos de aprendizaje y transformar conocimientos técnicos en contenido útil.

## Funcionalidades principales

### 1. Integración con la API de 4Geeks Academy

Conjunto de skills especializadas diseñadas para interactuar con la API de estudiantes de 4Geeks Academy.

Cada skill sigue el principio de responsabilidad única (Single Responsibility Principle), manteniendo su propósito delimitado y facilitando su mantenimiento.

Las skills documentadas incluyen:

| Skill | Objetivo |
|---|---|
| `authenticate-4geeks` | Verificar la autenticación con la API. |
| `get-4geeks-projects` | Recuperar información sobre los proyectos del estudiante. |
| `get-4geeks-pending` | Identificar los ejercicios pendientes agrupados por módulo. |
| `get-4geeks-progress` | Calcular el progreso global del bootcamp. |
| `recommend-next-task` | Recomendar la siguiente tarea prioritaria. |
| `discover-career-paths` | Explorar posibles perfiles profesionales tecnológicos según el progreso del estudiante. |

### 2. Skills personalizadas de aprendizaje y creación de contenido

El entorno también documenta dos skills complementarias:

- **Tink Academy:** Diseñada para transformar temas de estudio, documentos y apuntes en guías de aprendizaje estructuradas, con explicaciones, ejemplos, analogías y preguntas de repaso.
- **Tink StoryLab:** Diseñada para transformar conceptos técnicos en publicaciones accesibles para LinkedIn mediante storytelling y analogías.

Estas skills siguen un flujo de aprendizaje:

**Información → Comprensión → Comunicación**

### 3. Configuración del agente

El entorno utiliza archivos de configuración específicos para definir diferentes aspectos del agente:

- Comportamiento y directrices de funcionamiento.
- Identidad y estilo de comunicación.
- Contexto y preferencias del usuario.
- Convenciones para utilizar herramientas.
- Organización de la memoria.
- Diseño y documentación de skills.

### 4. Seguridad y modularidad

La integración con la API sigue varios principios de desarrollo:

- Uso de variables de entorno para credenciales sensibles.
- Separación de las credenciales y las instrucciones de las skills.
- Configuración de autenticación reutilizable.
- Una responsabilidad claramente definida por skill.
- Diseño y revisión iterativos antes de la implementación.

## Principios de diseño

- **Principio de responsabilidad única:** Cada skill resuelve un problema específico.
- **Modularidad:** Las skills pueden desarrollarse y mantenerse de forma independiente.
- **Seguridad:** Las credenciales no deben incluirse directamente en el código ni subirse al control de versiones.
- **Supervisión humana:** Las acciones externas importantes requieren confirmación del usuario.
- **Desarrollo iterativo:** Diseñar, revisar, implementar y probar cada skill progresivamente.

## Documentación del repositorio

| Archivo | Descripción |
|---|---|
| `SKILLS_DESIGN.md` | Documentación del diseño de las skills de aprendizaje y creación de contenido. |
| `SKILL_LOG.md` | Registro del desarrollo de la integración con la API de 4Geeks y de la creación de skills. |
| `AGENTS.md` | Directrices del entorno y principios de funcionamiento del agente. |

## Tecnologías y herramientas

- OpenClaw
- Agentes de IA
- Skills personalizadas para agentes
- APIs REST
- Variables de entorno
- JSON
- Markdown
- Google Docs
- Telegram

## Aprendizajes

Este proyecto explora enfoques prácticos para:

- Diseñar capacidades modulares para agentes de IA.
- Integrar APIs externas en un asistente de inteligencia artificial.
- Estructurar skills reutilizables con responsabilidades claras.
- Gestionar credenciales mediante variables de entorno.
- Diseñar flujos de trabajo que combinan recuperación de información, aprendizaje y creación de contenido.
- Documentar y mejorar iterativamente el comportamiento de un agente de IA.

## Estado del proyecto

Entorno personal de trabajo para un agente de IA y proyecto de integración de skills en evolución.

---

**Developer Wendy Sanchez**
