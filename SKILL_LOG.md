# SKILL_LOG

## Integración de OpenClaw con la API de Estudiantes de 4Geeks Academy

**Autor:** Wendy Sanchez
**Bootcamp:** AI Engineering - 4Geeks Academy 
**Proyecto:** Integración de OpenClaw con la API de estudiantes de 4Geeks Academy

---

## Introducción

El objetivo de este proyecto fue ampliar las capacidades de OpenClaw mediante el desarrollo de un conjunto de skills capaces de interactuar con la API de estudiantes de 4Geeks Academy.

En lugar de desarrollar una única skill con múltiples responsabilidades, el proyecto siguió el **Principio de Responsabilidad Única (Single Responsibility Principle)**, creando varias skills independientes, cada una encargada de resolver un único problema específico.

Cada skill fue diseñada utilizando **Skill Workshop**, revisada antes de su implementación, configurada mediante variables de entorno para evitar exponer credenciales sensibles, probada individualmente y finalmente incorporada al repositorio mediante un commit independiente.

Durante el desarrollo también se realizaron mejoras de arquitectura, entre ellas:

- Migración de credenciales desde `openclaw.json` hacia variables de entorno (`TOKEN_4GEEKS`).
- Configuración segura mediante el archivo `.env`.
- Reutilización de una única credencial para todas las skills.
- Diseño modular y reutilizable.
- Desarrollo iterativo mediante conversación con el agente OpenClaw (Tink).

---

## Resumen de Skills

| Skill | Objetivo |
|--------|----------|
| 🔐 authenticate-4geeks | Verificar que el token de acceso del estudiante sea válido. |
| 📋 get-4geeks-projects | Mostrar el estado de los proyectos del estudiante. |
| 💻 get-4geeks-pending | Mostrar únicamente los ejercicios pendientes agrupados por módulo. |
| 📈 get-4geeks-progress | Calcular el progreso global del bootcamp mediante indicadores visuales. |
| 🎯 recommend-next-task | Recomendar la siguiente tarea prioritaria según el estado del estudiante. |
| 🚀 discover-career-paths | Estimar la afinidad del estudiante hacia distintos perfiles tecnológicos según su progreso. |

---
## Skill 1 — Authenticate-4Geeks
Prompt inicial
*WENDY:* Hola, Tink. 😊
Quiero enseñarte una nueva capacidad: conectarte a mi cuenta de estudiante de 4Geeks Academy utilizando la API oficial.
Ya he preparado previamente el entorno siguiendo buenas prácticas de seguridad:
El token de estudiante no está hardcodeado.
Está almacenado de forma segura en ~/.openclaw/.env mediante la variable TOKEN_4GEEKS.
OpenClaw ya está configurado para utilizar variables de entorno mediante ${TOKEN_4GEEKS}.
Quiero construir esta integración exactamente como recomienda OpenClaw: mediante conversación y creando una skill pequeña con una única responsabilidad.
Aquí tienes la documentación oficial de la API de estudiantes de 4Geeks:
https://github.com/4GeeksAcademy/ai-engineering-syllabus/blob/main/content/projects/openclaw-integration/STUDENT_API_CALLS_REFERENCE.es.md
Me gustaría que primero analizaras esa documentación y me propusieras cómo diseñar la primera skill authenticate-4geeks.
Esta primera skill debe tener una única responsabilidad:
Verificar que el token de estudiante es válido y que la autenticación con la API funciona correctamente.
Antes de implementarla, explícame:
- Qué endpoint utilizarías y por qué.
- Si necesitas modificar openclaw.json para registrar la configuración de la skill y acceder a TOKEN_4GEEKS.
- Qué estructura propones para la skill siguiendo las buenas prácticas de OpenClaw.
- Si consideras que hay que crear una propuesta mediante Skill Workshop antes de aplicarla.
Quiero que vayamos paso a paso y que me expliques las decisiones antes de realizar cambios.

Respuesta y análisis
**AGENTE:** ¡Hola, Wen! Qué ilusión verte de nuevo. Me encanta que ya estemos pensando en ampliar mis poderes. 🧚🏻‍♀️✨
Vamos paso a paso, como siempre. Lo primero es analizar la documentación de la API que me has pasado¡Excelente documentación! La he leído completa. Ahora
déjame verificar cómo está configurado tu entorno para acceder al token:Perfecto, Wen. Ahora tengo toda la información que necesito. Déjame presentarte el
análisis completo antes de tocar nada. 🧚🏻‍♀️
────────────────────────────────────────────────────────────────────────────────
📋 Análisis de la API de 4Geeks
He leído la documentación completa. Aquí están los puntos clave que identifico:
### 🔐 Autenticación
- El token se envía en la cabecera Authorization con el prefijo Token (no Bearer, ojo con eso)
- Tu token está en ~/.openclaw/.env como TOKEN_4GEEKS ✅
- OpenClaw ya lo inyecta en el entorno del proceso ✅
────────────────────────────────────────────────────────────────────────────────
🎯 ¿Qué endpoint usar para authenticate-4geeks?
Propongo usar GET /v1/admissions/user/me
¿Por qué?
┌───────────────────────────┬──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┐
│ Razón                     │ Explicación                                                                                                          │
├───────────────────────────┼──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┤
│ Solo lectura              │ Es un GET, no modifica nada. Seguro para pruebas.                                                                    │
├───────────────────────────┼──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┤
│ No requiere parámetros    │ Funciona solo con el token. No necesita academy_id, cohort_id ni nada.                                               │
├───────────────────────────┼──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┤
│ Requiere autenticación    │ Si el token es inválido, devolverá 401. Si es válido, devuelve 200 + datos del usuario.                              │
├───────────────────────────┼──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┤
│ Devuelve información útil │ Al autenticarte, podemos mostrar tu nombre, email y academias vinculadas — confirmación visual de que todo funciona. │
└───────────────────────────┴──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┘
Alternativa descartada: Podría usar GET /v1/admissions/public/syllabus (que es público y no necesita token), pero no serviría para verificar autenticación —
cualquier llamada funcionaría aunque el token fuera basura.
────────────────────────────────────────────────────────────────────────────────
⚙️ ¿Necesitamos modificar openclaw.json?
Sí, pero poco. Siguiendo las mejores prácticas de OpenClaw:
1. Registrar la skill en skills.entries para que OpenClaw le inyecte explícitamente la variable TOKEN_4GEEKS en cada ejecución.
2. Declarar primaryEnv en el frontmatter de la skill para que OpenClaw sepa qué variable de entorno necesita.
Actualmente no hay ningún skills.entries configurado, así que añadiríamos:
```json5
"skills": {
"entries": {
"authenticate-4geeks": {
"enabled": true,
"env": {
"TOKEN_4GEEKS": "${TOKEN_4GEEKS}"
}
}
}
}
```
────────────────────────────────────────────────────────────────────────────────
🏗️ Estructura propuesta para la skill
Siguiendo las mejores prácticas de OpenClaw (skills pequeñas, una responsabilidad, frontmatter limpio):
```markdown
---
name: authenticate-4geeks
description: "Verify that the 4Geeks student API token is valid and authentication works"
metadata:
{
"openclaw":
{
"always": true,
"primaryEnv": "TOKEN_4GEEKS",
"emoji": "🔐",
},
}
---
Usa esta skill cuando Wendy quiera verificar que su token de 4Geeks Academy funciona.
## Instrucciones
1. Toma el token de la variable `${TOKEN_4GEEKS}`.
2. Haz una petición GET a `https://breathecode.herokuapp.com/v1/admissions/user/me` con la cabecera `Authorization: Token ${TOKEN_4GEEKS}`.
3. Interpreta la respuesta:
- **200 OK** → el token es válido. Muestra los datos del usuario (nombre, email, academias vinculadas).
- **401 Unauthorized** → el token no es válido o ha expirado.
- **Otro error** → muestra el código y mensaje para depurar.
4. Entrega el resultado por Telegram si está disponible, o en el canal actual.
```
────────────────────────────────────────────────────────────────────────────────
📝 ¿Skill Workshop primero?
Sí, definitivamente. Por varios motivos:
1. La skill requiere modificar openclaw.json (no es solo crear SKILL.md)
2. Queremos validar que el token funciona antes de hacer la skill más compleja
3. Esto nos permite iterar contigo: primero authenticate-4geeks (verificar token), luego podríamos hacer 4geeks-tasks (consultar tareas), etc.
────────────────────────────────────────────────────────────────────────────────
💡 Mi propuesta de plan
1. ✅ Tú apruebas este diseño (o me pides cambios)
2. 📝 Creo la skill mediante Skill Workshop como propuesta
3. ⚙️ Modifico openclaw.json para registrar la variable de entorno
4. ✅ Probamos que el token funciona
5. ✅ Aplicamos la skill
¿Qué te parece el diseño? ¿Cambiarías algo del endpoint, la estructura o el plan antes de que empecemos a implementar? 😊

Prompt siguiente
*WENDY:* Me gusta mucho el diseño que propones y estoy de acuerdo con el endpoint y con mantener una única responsabilidad para la skill.
Antes de implementarla, me gustaría hacer algunos ajustes:
1. Prefiero que esta skill no utilice always: true, ya que solo debe cargarse cuando sea necesaria.
2. Me gustaría confirmar que https://breathecode.herokuapp.com sigue siendo el endpoint oficial recomendado y que no existe una URL más reciente.
3. Propongo crear primero la propuesta mediante Skill Workshop, revisarla y solo después actualizar openclaw.json y aplicar la skill.
4. Me gustaría añadir metadatos como versión, autor y categoría para mantener la misma organización que estamos utilizando en nuestras otras skills.
**Propuesta aprobada**

CREACIÓN DE SKILL: authenticate-4geeks
 
## Skill 2 — Get-4Geeks-Projects
Prompt inicial
*WENDY:* Tink 😊
Ya tenemos implementada y funcionando la skill authenticate-4geeks, que servirá como plantilla para las siguientes integraciones con la API de estudiantes.
Me gustaría construir ahora la segunda skill siguiendo exactamente la misma metodología:
- una única responsabilidad
- uso del Skill Workshop
- revisión antes de aplicar
- configuración segura mediante TOKEN_4GEEKS
- sin always: true
- manteniendo la misma estructura y estilo que nuestras skills anteriores.
Según la documentación oficial de la API de estudiantes de 4Geeks, quiero que me propongas el diseño de la skill get-4geeks-projects.
Su única responsabilidad debe ser recuperar la lista de proyectos (assignments) del estudiante con su estado actual (pendiente, entregado, aprobado).
Antes de crear la propuesta, explícame:
1. Qué endpoint utilizarás.
2. Qué parámetros son necesarios.
3. Qué información debería mostrar por defecto.
4. Qué información no debería mostrar para mantener una única responsabilidad.
5. Si existe alguna mejora respecto a la primera skill.
Después generaremos la propuesta mediante Skill Workshop y la revisaremos antes de aplicarla.

Respuesta y análisis
*AGENTE:* ¡Vamos a ello! Primero, voy a consultar el endpoint de tareas/proyectos para entender exactamente qué devuelve y poder diseñar la skill con
datos reales. 🧚🏻‍♀️¡Datos en mano! He explorado la API y tengo toda la información necesaria para diseñar la skill. Aquí tienes el análisis
completo. 🧚🏻‍♀️
────────────────────────────────────────────────────────────────────────────────
📊 Lo que devuelve la API para proyectos
He hecho pruebas reales con tu token y estos son los resultados:
┌───────────────────┬────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┐
│ Dato              │ Valor                                                                                                                  │
├───────────────────┼────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┤
│ Total proyectos   │ 29                                                                                                                     │
├───────────────────┼────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┤
│ PENDIENTES        │ 5                                                                                                                      │
├───────────────────┼────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┤
│ APROBADOS         │ 24                                                                                                                     │
├───────────────────┼────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┤
│ Campos            │ id, title, task_status, revision_status, associated_slug, description, github_url, live_url, task_type, cohort (id,    │
│ disponibles       │ name, slug), fechas                                                                                                    │
└───────────────────┴────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┘
────────────────────────────────────────────────────────────────────────────────
1️⃣ ¿Qué endpoint?
GET /v1/assignment/user/me/task?task_type=PROJECT&limit=50
┌─────────────────────────┬─────────────────────────────────────────────────────────────────────────────────────┐
│ Razón                   │ Explicación                                                                         │
├─────────────────────────┼─────────────────────────────────────────────────────────────────────────────────────┤
│ Filtro por proyecto     │ task_type=PROJECT excluye lecciones y ejercicios — solo proyectos.                  │
├─────────────────────────┼─────────────────────────────────────────────────────────────────────────────────────┤
│ Sin autenticación extra │ Usa el mismo Authorization: Token ${TOKEN_4GEEKS} que authenticate-4geeks.          │
├─────────────────────────┼─────────────────────────────────────────────────────────────────────────────────────┤
│ Ya filtra por usuario   │ /user/me asegura que solo devuelve tus tareas.                                      │
├─────────────────────────┼─────────────────────────────────────────────────────────────────────────────────────┤
│ Limit suficiente        │ Con limit=50 obtenemos todos tus proyectos (tienes 29) sin necesidad de paginación. │
└─────────────────────────┴─────────────────────────────────────────────────────────────────────────────────────┘
────────────────────────────────────────────────────────────────────────────────
2️⃣ ¿Qué parámetros?
Solo uno obligatorio: el token (igual que en authenticate-4geeks).
Como query parameters opcionales usaríamos:
- task_type=PROJECT — para filtrar solo proyectos
- limit=50 — para obtener todos sin paginar
No necesitamos cohort_id — el endpoint ya filtra por los cohortes en los que estás inscrita.
────────────────────────────────────────────────────────────────────────────────
3️⃣ ¿Qué información mostrar por defecto?
Propongo agrupar por estado para que sea visual y útil de un vistazo:
```
📊 Tus proyectos de 4Geeks
✅ APROBADOS (24)
• Code an Excuse Generator in JavaScript — AI Engineering Introduction
• Build a Digital Postcard with HTML/CSS — AI Engineering Introduction
• ...
⏳ PENDIENTES (5)
• Error Handling — Error handling, debugging and testing
• My 4Geeks Assistant — Advanced personal assistants with Openclaw
• ...
```
Cada proyecto mostraría:
- Título del proyecto
- Cohorte al que pertenece (para saber de qué módulo es)
────────────────────────────────────────────────────────────────────────────────
4️⃣ ¿Qué información NO mostrar?
Para mantener la única responsabilidad (listar proyectos con su estado):
┌───────────────────────────────────────────┬─────────────────────────────────────────────────┐
│ No mostrar                                │ Motivo                                          │
├───────────────────────────────────────────┼─────────────────────────────────────────────────┤
│ ❌ id numérico interno                    │ No aporta valor al listado                      │
├───────────────────────────────────────────┼─────────────────────────────────────────────────┤
│ ❌ description completa                   │ Demasiado detalle para un listado               │
├───────────────────────────────────────────┼─────────────────────────────────────────────────┤
│ ❌ github_url / live_url                  │ URLs de entrega — responsabilidad de otra skill │
├───────────────────────────────────────────┼─────────────────────────────────────────────────┤
│ ❌ Fechas (opened_at, delivered_at, etc.) │ Son metadatos de entrega, no de estado          │
├───────────────────────────────────────────┼─────────────────────────────────────────────────┤
│ ❌ Datos del perfil del usuario           │ Ya los protege authenticate-4geeks              │
├───────────────────────────────────────────┼─────────────────────────────────────────────────┤
│ ❌ associated_slug                        │ Identificador interno, irrelevante              │
└───────────────────────────────────────────┴─────────────────────────────────────────────────┘
Si Wendy quiere los detalles de un proyecto concreto (descripción, URLs, fechas), eso sería responsabilidad de una tercera skill (tipo
get-4geeks-task-detail).
────────────────────────────────────────────────────────────────────────────────
5️⃣ Mejoras respecto a authenticate-4geeks
Basado en lo que he aprendido implementando la primera:
┌───────────────────────────────────┬────────────────────────────────────────────────────────────────────────────────────────────────────────┐
│ Mejora                            │ Por qué                                                                                                │
├───────────────────────────────────┼────────────────────────────────────────────────────────────────────────────────────────────────────────┤
│ Usar python3 -c en lugar de       │ authenticate-4geeks usó curl + python3 internamente. Lo mantendré como patrón.                         │
│ parseo manual                     │                                                                                                        │
├───────────────────────────────────┼────────────────────────────────────────────────────────────────────────────────────────────────────────┤
│ Pre-filtrar cohortes activas      │ Algunos proyectos son de cohortes ya graduados. Podemos filtrar solo los ACTIVE si tiene sentido, o    │
│                                   │ mostrar todos pero indicar el cohorte.                                                                 │
├───────────────────────────────────┼────────────────────────────────────────────────────────────────────────────────────────────────────────┤
│ Agrupar por estado                │ Más legible que una lista plana.                                                                       │
├───────────────────────────────────┼────────────────────────────────────────────────────────────────────────────────────────────────────────┤
│ Mostrar totales                   │ "5 pendientes, 24 aprobados" da contexto inmediato.                                                    │
└───────────────────────────────────┴────────────────────────────────────────────────────────────────────────────────────────────────────────┘
────────────────────────────────────────────────────────────────────────────────
Si te parece bien el diseño, creo la propuesta mediante Skill Workshop con esta estructura:
```
Frontmatter → name, description, metadata (primaryEnv, emoji)
Metadatos → versión 1.0.0, autor, categoría 4Geeks
Disparadores → "Lista mis proyectos", "¿Qué proyectos tengo?", etc.
Instrucciones → flujo: GET → parsear → agrupar → entregar
Privacidad → no muestra datos personales
Dependencias → TOKEN_4GEEKS + openclaw.json
```
¿Le damos? 😊

Prompt siguiente
*WENDY:* He revisado el diseño y estoy de acuerdo con prácticamente todo.
Después de analizar mi caso de uso, quiero mantener el listado tanto de proyectos aprobados como pendientes, así como el módulo o cohorte al que pertenece cada proyecto, porque me ayuda a ubicar rápidamente cada uno y a visualizar mi progreso en el bootcamp.
El único aspecto que me gustaría revisar es el uso de limit=50. Actualmente el bootcamp contiene más proyectos que ese límite, por lo que me gustaría que investigaras si la API soporta paginación para recuperar automáticamente todos los proyectos o si existe otra forma de obtener el listado completo sin establecer un límite fijo.

Respuesta y análisis
*AGENTE:* ¡Excelente observación, Wen! Vamos a investigar cómo se comporta la paginación en la API. 🧚🏻‍♀️¡Ajá! Con limit la API devuelve paginación con una
estructura completa. Vamos a ver los detalles:¡Descubrimiento importante! La API de 4Geeks tiene un comportamiento clave según cómo se use el
parámetro limit. 🧚🏻‍♀️
────────────────────────────────────────────────────────────────────────────────
📊 Comportamiento de la paginación
┌────────────────────────┬────────────────────┬───────────────────────────────────────────────────────┐
│ Escenario              │ Tipo de respuesta  │ ¿Qué devuelve?                                        │
├────────────────────────┼────────────────────┼───────────────────────────────────────────────────────┤
│ Sin limit              │ ✅ Lista plana     │ Los 29 proyectos de golpe                             │
├────────────────────────┼────────────────────┼───────────────────────────────────────────────────────┤
│ Con limit=N            │ 🔄 Objeto paginado │ count, next, previous, results                        │
├────────────────────────┼────────────────────┼───────────────────────────────────────────────────────┤
│ Sin limit ni task_type │ ✅ Lista plana     │ 139 items (todos: proyectos + lecciones + ejercicios) │
└────────────────────────┴────────────────────┴───────────────────────────────────────────────────────┘
Con limit=5, la API devuelve una estructura con paginación completa:
```json
{
"count": 29,
"next": "https://...&limit=5&offset=5",
"previous": null,
"first": null,
"last": "https://...&limit=5&offset=24",
"results": [...]
}
```
Pero sin limit, la API devuelve directamente el array plano con todos los elementos. ✨
────────────────────────────────────────────────────────────────────────────────
✅ Mi propuesta: no usar limit
En lugar de limit=50 o implementar paginación compleja, la solución más limpia es:
Simplemente omitir limit → la API devuelve el listado completo en una sola llamada.
```
GET /v1/assignment/user/me/task?task_type=PROJECT
```
┌────────────────────┬──────────────────────────────────────────────────────────────┐
│ Ventaja            │ Explicación                                                  │
├────────────────────┼──────────────────────────────────────────────────────────────┤
│ 🎯 Sin límite fijo │ No importa si mañana tienes 29 o 129 proyectos               │
├────────────────────┼──────────────────────────────────────────────────────────────┤
│ 🧹 Sin paginación  │ Una sola llamada, una sola respuesta                         │
├────────────────────┼──────────────────────────────────────────────────────────────┤
│ 🔧 Más robusto     │ Menos lógica que mantener en la skill                        │
├────────────────────┼──────────────────────────────────────────────────────────────┤
│ 📈 Escalable       │ Si crece el bootcamp, la skill sigue funcionando sin cambios │
└────────────────────┴──────────────────────────────────────────────────────────────┘
En tu caso, con 29 proyectos, funciona perfectamente. Y si en el futuro tienes muchos más (por ejemplo 200+), ya evaluaremos si añadir
paginación.
¿Qué te parece? Si estás de acuerdo, procedo a crear la propuesta de get-4geeks-projects mediante Skill Workshop. 😊
**Propuesta aprobada**

CREACIÓN DE SKILL: get-4geeks-projects
 
## Skill 3 — Get-4Geeks-Pending
Prompt inicial
*WENDY:* Tink! Ya tenemos implementadas y funcionando las siguientes skills de integración con la API de estudiantes de 4Geeks:
🔐 authenticate-4geeks → verifica que el token de estudiante es válido.
📋 get-4geeks-projects → obtiene la lista de proyectos del estudiante con su estado actual.
Me gustaría diseñar ahora la tercera skill llamada get-4geeks-pending siguiendo exactamente la misma metodología y arquitectura:
- una única responsabilidad
- uso de Skill Workshop
- revisión antes de aplicar
- configuración segura mediante TOKEN_4GEEKS
- sin always: true
- reutilizando el mismo estilo y organización de las skills anteriores.
Después de analizar cómo está organizada la plataforma de 4Geeks, he decidido que esta tercera skill no debe incluir proyectos, ya que esa responsabilidad ya está cubierta por get-4geeks-projects.
Su objetivo será centrarse exclusivamente en los ejercicios pendientes, que forman parte del trabajo diario del estudiante y ayudan a preparar los proyectos.
Antes de crear la propuesta, me gustaría que analizaras la documentación oficial de la API y me explicaras:
1. ¿Qué endpoint utilizarías para obtener únicamente los ejercicios del estudiante?
2. ¿Existe un task_type específico para ejercicios o habría que filtrar la información de otra manera?
3. ¿Qué información debería mostrar por defecto para que sea realmente útil durante el estudio?
4. ¿Qué información debería omitirse para mantener una única responsabilidad y evitar duplicar funcionalidades de otras skills?
5. ¿Cómo diferenciarías claramente esta skill de get-4geeks-projects para que ambas se complementen sin solaparse?
Antes de implementar nada, explícame tu propuesta de diseño. Después generaremos la propuesta mediante Skill Workshop para revisarla antes de aplicarla.

Respuesta y análisis
*AGENTE:* ¡Me encanta la idea, Wen! Vamos a investigar cómo se comportan los ejercicios en la API para diseñar esta skill con datos reales. 🧚🏻‍♀¡Datos
completos! Ahora tengo una imagen muy clara. Déjame analizar todo y presentarte el diseño. 🧚🏻‍♀️
────────────────────────────────────────────────────────────────────────────────
📊 Lo que he descubierto de los ejercicios en la API
┌───────────────────────────────┬───────┐
│ Dato                          │ Valor │
├───────────────────────────────┼───────┤
│ Total ejercicios              │ 96    │
├───────────────────────────────┼───────┤
│ Pendientes (PENDING)          │ 30    │
├───────────────────────────────┼───────┤
│ Completados (DONE)            │ 66    │
├───────────────────────────────┼───────┤
│ De ellos aprobados (APPROVED) │ 28    │
└───────────────────────────────┴───────┘
### Distribución de pendientes por cohorte
┌────────────────────────────────────────────┬────────────┐
│ Cohorte                                    │ Pendientes │
├────────────────────────────────────────────┼────────────┤
│ Web UI fundamentals with Tailwind          │ 9          │
├────────────────────────────────────────────┼────────────┤
│ Frontend development with Coding Agents    │ 7          │
├────────────────────────────────────────────┼────────────┤
│ Coding Fundamentals with Typescript        │ 6          │
├────────────────────────────────────────────┼────────────┤
│ Authentication in web applications         │ 3          │
├────────────────────────────────────────────┼────────────┤
│ Managing relational databases with FastAPI │ 3          │
├────────────────────────────────────────────┼────────────┤
│ Coding fundamentals with Python            │ 1          │
├────────────────────────────────────────────┼────────────┤
│ Error handling, debugging and testing      │ 1          │
└────────────────────────────────────────────┴────────────┘
────────────────────────────────────────────────────────────────────────────────
Mi propuesta de diseño para get-4geeks-pending
### 1️⃣ Endpoint
GET /v1/assignment/user/me/task?task_type=EXERCISE
Mismo patrón que get-4geeks-projects, pero filtrando por EXERCISE en lugar de PROJECT. Sin limit para obtener el listado completo en una sola
llamada.
### 2️⃣ ¿Hay un task_type específico para ejercicios?
Sí. La API tiene task_type=EXERCISE y es el filtro correcto. Los valores disponibles son:
┌───────────┬──────────┬───────────────────────────┐
│ task_type │ Cantidad │ ¿Para qué skill?          │
├───────────┼──────────┼───────────────────────────┤
│ PROJECT   │ 29       │ get-4geeks-projects ✅    │
├───────────┼──────────┼───────────────────────────┤
│ EXERCISE  │ 96       │ get-4geeks-pending ← esta │
├───────────┼──────────┼───────────────────────────┤
│ LESSON    │ 14       │ Futura skill (lecciones)  │
├───────────┼──────────┼───────────────────────────┤
│ QUIZ      │ 0        │ No aplica                 │
└───────────┴──────────┴───────────────────────────┘
No hay ambigüedad — cada tipo está bien diferenciado.
### 3️⃣ ¿Qué información mostrar?
A diferencia de get-4geeks-projects que agrupa por estado (pendientes/aprobados), esta skill muestra solo los pendientes agrupados por
cohorte, para que sea útil durante el estudio:
```
📋 Ejercicios pendientes de 4Geeks
Total: 30 pendientes
📚 Web UI fundamentals with Tailwind (9)
• Styling Forms with Tailwind CSS
• Building Effective Forms: Keep It Simple
• Dynamic Forms with Vanilla JavaScript
...
📚 Frontend development with Coding Agents (7)
• Getting data from APIs for Beginners
• API and Complex JSON Navigation
...
```
Esto permite a Wendy ver qué módulos tienen más ejercicios pendientes y priorizar su estudio.
### 4️⃣ ¿Qué información omitir?
┌────────────────────────────────┬──────────────────────────────────────────────────────┐
│ Omitir                         │ Motivo                                               │
├────────────────────────────────┼──────────────────────────────────────────────────────┤
│ ❌ Ejercicios ya completados   │ No son "pendientes" — mantener responsabilidad única │
├────────────────────────────────┼──────────────────────────────────────────────────────┤
│ ❌ IDs internos                │ No aportan valor                                     │
├────────────────────────────────┼──────────────────────────────────────────────────────┤
│ ❌ Descripciones completas     │ Demasiado detalle para un listado                    │
├────────────────────────────────┼──────────────────────────────────────────────────────┤
│ ❌ Fechas                      │ No relevantes para saber qué hacer                   │
├────────────────────────────────┼──────────────────────────────────────────────────────┤
│ ❌ associated_slug             │ Identificador interno                                │
├────────────────────────────────┼──────────────────────────────────────────────────────┤
│ ❌ URLs (github_url, live_url) │ Los ejercicios no tienen URLs de entrega             │
└────────────────────────────────┴──────────────────────────────────────────────────────┘
### 5️⃣ Diferenciación clara con get-4geeks-projects
┌─────────────┬───────────────────────────────┬─────────────────────────────┐
│ Aspecto     │ get-4geeks-projects           │ get-4geeks-pending          │
├─────────────┼───────────────────────────────┼─────────────────────────────┤
│ task_type   │ PROJECT                       │ EXERCISE                    │
├─────────────┼───────────────────────────────┼─────────────────────────────┤
│ Qué muestra │ Pendientes + Aprobados        │ Solo pendientes             │
├─────────────┼───────────────────────────────┼─────────────────────────────┤
│ Agrupación  │ Por estado                    │ Por cohorte/módulo          │
├─────────────┼───────────────────────────────┼─────────────────────────────┤
│ Utilidad    │ Progreso general del bootcamp │ Qué hacer hoy / esta semana │
├─────────────┼───────────────────────────────┼─────────────────────────────┤
│ Complemento │ Visión estratégica            │ Visión táctica y de estudio │
└─────────────┴───────────────────────────────┴─────────────────────────────┘
Son complementarias: una te dice dónde estás, la otra te dice qué te falta por hacer.
────────────────────────────────────────────────────────────────────────────────
Si te parece bien el diseño, creo la propuesta mediante Skill Workshop. ¿Qué opinas? 😊
**Propuesta aprobada**

CREACIÓN DE SKILL 3: get-4geeks-pending
 
## Skill 4 — Get-4Geeks-Progress
Prompt inicial
*WENDY:* Tink 😊
Ya tenemos implementadas y funcionando las siguientes skills de integración con la API de estudiantes de 4Geeks:
🔐 authenticate-4geeks → verifica que el token de estudiante es válido.
📋 get-4geeks-projects → obtiene la lista de proyectos del estudiante con su estado actual.
💻 get-4geeks-pending → muestra únicamente los ejercicios pendientes agrupados por módulo.
Me gustaría diseñar ahora la cuarta skill, que se llamará get-4geeks-progress, siguiendo exactamente la misma metodología que hemos utilizado hasta ahora:
una única responsabilidad;
uso de Skill Workshop;
revisión de la propuesta antes de aplicarla;
configuración segura mediante TOKEN_4GEEKS;
sin always: true;
reutilizando la misma arquitectura, estilo y convenciones de las skills anteriores.
El objetivo de esta skill será ofrecer un resumen del progreso general del estudiante dentro del bootcamp, actuando como un pequeño panel de control que permita conocer de un vistazo el estado global del curso.
Antes de crear la propuesta, me gustaría que analizaras la documentación oficial de la API y me explicaras:
1. ¿Qué endpoint o endpoints utilizarías para calcular el progreso general del estudiante?
2. ¿Qué métricas pueden obtenerse realmente desde la API y cuáles habría que calcular?
3. ¿Qué información debería mostrarse por defecto para ofrecer un resumen útil sin duplicar la información detallada que ya proporcionan get-4geeks-projects y get-4geeks-pending?
4. ¿Cómo representarías el progreso general utilizando únicamente los datos disponibles en la API?
5. Si la API no proporciona directamente un porcentaje de progreso, ¿cuál sería la forma más correcta y consistente de calcularlo?
Además, me gustaría proponerte una mejora de presentación para esta skill.
Si es técnicamente posible dentro del formato habitual de las skills de OpenClaw (texto/Markdown), me gustaría que el resultado mostrara un indicador visual de progreso mediante una barra de 10 círculos, donde cada círculo represente aproximadamente un 10% del progreso total, acompañada del porcentaje correspondiente.
Por ejemplo:

📈 Progreso general del Bootcamp
🟢🟢🟢🟢🟢🟢🟢⚪⚪⚪ 72%
Después de la barra de progreso, me gustaría mostrar un pequeño resumen con las principales métricas del bootcamp, por ejemplo:
📋 Proyectos (completados / pendientes)
💻 Ejercicios (completados / pendientes)
📖 Lecciones (si la API las permite incluir)
La idea es que esta skill ofrezca una visión global, mientras que las demás continúan siendo las encargadas de mostrar el detalle específico de proyectos y ejercicios.
Si durante el análisis descubres limitaciones en la API o consideras que existe una forma más adecuada de calcular o representar el progreso, explícamela antes de generar la propuesta y justifica tu decisión manteniendo siempre el principio de una única responsabilidad.

Respuesta y análisis
*AGENTE:* ¡Me encanta la idea, Wen! Un panel de control general. Déjame investigar todos los datos disponibles para calcular el progreso correctamente.
🧚🏻‍♀️¡Datos completos! Ahora tengo todo lo que necesito para diseñar la propuesta. 🧚🏻‍♀️
────────────────────────────────────────────────────────────────────────────────
📊 Datos reales de tu progreso en el bootcamp
┌───────────────┬─────────────┬────────────┬───────┬──────────┐
│ Categoría     │ Completados │ Pendientes │ Total │ Progreso │
├───────────────┼─────────────┼────────────┼───────┼──────────┤
│ 📋 Proyectos  │ 24          │ 5          │ 29    │ 83%      │
├───────────────┼─────────────┼────────────┼───────┼──────────┤
│ 💻 Ejercicios │ 66          │ 30         │ 96    │ 69%      │
├───────────────┼─────────────┼────────────┼───────┼──────────┤
│ 📖 Lecciones  │ 12          │ 2          │ 14    │ 86%      │
├───────────────┼─────────────┼────────────┼───────┼──────────┤
│ 🌐 Global     │ 102         │ 37         │ 139   │ ~73%     │
└───────────────┴─────────────┴────────────┴───────┴──────────┘
────────────────────────────────────────────────────────────────────────────────
Mi propuesta de diseño para get-4geeks-progress
### 1️⃣ ¿Qué endpoint?
Un solo endpoint: GET /v1/assignment/user/me/task (sin task_type)
Esto devuelve todos los items (139 en tu caso) en una sola llamada, y cada uno lleva su campo task_type para que podamos categorizarlos
internamente.
Ventaja: Una sola llamada a la API → más rápido, menos riesgo de rate limiting.
### 2️⃣ Métricas disponibles vs calculadas
┌───────────────────────────┬─────────────────────┬─────────────────────────────────────────────────────────┐
│ Métrica                   │ ¿Directa de la API? │ Nota                                                    │
├───────────────────────────┼─────────────────────┼─────────────────────────────────────────────────────────┤
│ Total items por categoría │ ✅ Sí               │ Cada item tiene task_type (PROJECT / EXERCISE / LESSON) │
├───────────────────────────┼─────────────────────┼─────────────────────────────────────────────────────────┤
│ Estado de cada item       │ ✅ Sí               │ task_status (PENDING / DONE)                            │
├───────────────────────────┼─────────────────────┼─────────────────────────────────────────────────────────┤
│ Progreso % por categoría  │ 🔄 Calculado        │ completados / total * 100                               │
├───────────────────────────┼─────────────────────┼─────────────────────────────────────────────────────────┤
│ Progreso global %         │ 🔄 Calculado        │ (suma completados) / (suma total) * 100                 │
├───────────────────────────┼─────────────────────┼─────────────────────────────────────────────────────────┤
│ Barra visual ⓾ círculos   │ 🔄 Calculada        │ floor(progreso / 10) círculos rellenos                  │
└───────────────────────────┴─────────────────────┴─────────────────────────────────────────────────────────┘
### 3️⃣ ¿Qué mostrar por defecto?
Para mantener la única responsabilidad (resumen global) y no duplicar lo que ya hacen get-4geeks-projects y get-4geeks-pending:
```
📈 Progreso general del Bootcamp
🟢🟢🟢🟢🟢🟢🟢⚪⚪⚪  73%
📋 Proyectos:   24/29  (83%)
💻 Ejercicios:  66/96  (69%)
📖 Lecciones:   12/14  (86%)
📊 Total: 102 completados · 37 pendientes
```
Lo que NO debe mostrar (responsabilidad de otras skills):
- ❌ Listado individual de proyectos
- ❌ Listado individual de ejercicios pendientes
- ❌ Nombres de cohortes
- ❌ Títulos de cada tarea
### 4️⃣ Representación del progreso
La barra de 10 círculos funciona así:
```python
progreso = 73
circulos_rellenos = progreso // 10   # 7
circulos_vacios = 10 - circulos_rellenos  # 3
# 🟢🟢🟢🟢🟢🟢🟢⚪⚪⚪  73%
```
Círculo = ~10%. Con floor division, un 73% se muestra como 7 verdes + 3 blancos.
### 5️⃣ Cálculo del progreso — criterio consistente
Para todas las categorías uso el mismo criterio:
┌──────────────────────────┬───────────────────────────────────────┐
│ Estado                   │ ¿Se considera completado?             │
├──────────────────────────┼───────────────────────────────────────┤
│ task_status == "DONE"    │ ✅ Sí — el estudiante hizo el trabajo │
├──────────────────────────┼───────────────────────────────────────┤
│ task_status == "PENDING" │ ❌ No — pendiente                     │
└──────────────────────────┴───────────────────────────────────────┘
Es el criterio más consistente y transparente. La diferencia entre "aprobado" y "pendiente de revisión" es un detalle que ya cubren las otras
skills.
────────────────────────────────────────────────────────────────────────────────
🎨 La barra de círculos: ¿es posible?
Sí, totalmente posible. El formato de salida de las skills de OpenClaw es texto plano / Markdown, y los emojis funcionan en Telegram y en
webchat sin problema.
```
🟢🟢🟢🟢🟢🟢🟢⚪⚪⚪  73%
```
┌───────┬─────────────────┐
│ Emoji │ Significado     │
├───────┼─────────────────┤
│ 🟢    │ ~10% completado │
├───────┼─────────────────┤
│ ⚪    │ ~10% pendiente  │
└───────┴─────────────────┘
────────────────────────────────────────────────────────────────────────────────
### 📋 Resumen del diseño
┌────────────────────────┬────────────────────────────────────────────────────────────────────┐
│ Aspecto                │ Decisión                                                           │
├────────────────────────┼────────────────────────────────────────────────────────────────────┤
│ Endpoint               │ GET /v1/assignment/user/me/task (sin task_type) — una sola llamada │
├────────────────────────┼────────────────────────────────────────────────────────────────────┤
│ Categorización         │ Por task_type en cada item (PROJECT / EXERCISE / LESSON)           │
├────────────────────────┼────────────────────────────────────────────────────────────────────┤
│ Criterio de completado │ task_status == "DONE" (consistente para todas)                     │
├────────────────────────┼────────────────────────────────────────────────────────────────────┤
│ Barra visual           │ 10 círculos 🟢 / ⚪                                                │
├────────────────────────┼────────────────────────────────────────────────────────────────────┤
│ Info mostrada          │ Barra + % global + resumen por categoría (solo números)            │
├────────────────────────┼────────────────────────────────────────────────────────────────────┤
│ Info omitida           │ Títulos, cohortes, listados individuales                           │
└────────────────────────┴────────────────────────────────────────────────────────────────────┘
Si te parece bien, creo la propuesta mediante Skill Workshop con todo esto. ¿Qué opinas? 😊
**Propuesta aprobada**

CREACIÓN DE SKILL 4: get-4geeks-progress
 
## Skill 5 — Recommend-next-task
Prompt inicial
*WENDY:* Tink!
Ya hemos completado e implementado correctamente las cuatro skills principales de integración con la API de estudiantes de 4Geeks:
🔐 authenticate-4geeks → verifica la autenticación del estudiante.
📋 get-4geeks-projects → muestra los proyectos con su estado.
💻 get-4geeks-pending → muestra los ejercicios pendientes agrupados por módulo.
📈 get-4geeks-progress → ofrece un resumen global del progreso del bootcamp.
Ahora me gustaría crear otra adicional, que se llamará recommend-next-task.
A diferencia de las anteriores, esta skill no pretende mostrar información nueva, sino interpretar la información obtenida desde la API para ayudar al estudiante a decidir cuál debería ser su siguiente paso.
Quiero mantener exactamente la misma metodología que hemos utilizado hasta ahora:
- una única responsabilidad
- uso de Skill Workshop
- revisión de la propuesta antes de aplicarla
- configuración segura mediante TOKEN_4GEEKS
- sin always: true
- reutilizando la misma arquitectura y estilo de las skills anteriores.
Antes de generar la propuesta, me gustaría que analizaras la documentación de la API y me explicaras cuál sería la mejor estrategia para recomendar la siguiente tarea del estudiante utilizando únicamente la información disponible.
Me gustaría que respondieras especialmente a estas preguntas:
1. ¿Qué endpoint o endpoints necesitarías consultar para generar una recomendación útil?
2. ¿Qué criterios utilizarías para decidir cuál debería ser la siguiente tarea recomendada?
3. ¿Cómo evitarías que esta skill duplique la información que ya muestran get-4geeks-projects, get-4geeks-pending y get-4geeks-progress?
4. ¿Qué información mostrarías al estudiante para justificar la recomendación?
5. ¿Cómo mantendrías el principio de una única responsabilidad si esta skill necesita interpretar información procedente de varias categorías?
Mi idea es que esta skill funcione como un mentor académico.
No quiero que simplemente liste tareas pendientes. Quiero que analice el estado del estudiante y recomiende una única acción prioritaria para continuar avanzando en el bootcamp.
Por ejemplo, podría tener en cuenta criterios como:
- módulos con ejercicios pendientes
- módulos casi completados que merezca la pena terminar
- cualquier otra estrategia que consideres más adecuada basándote en la documentación oficial y en la información que proporciona la API.
Me gustaría que la respuesta tuviera un formato similar a este:

🎯 Recomendación de hoy
Continúa con el módulo:
📚 Managing relational databases with FastAPI
Motivo:
• Tienes 3 ejercicios pendientes en este módulo.
• Al completarlos podrás comenzar el proyecto "EduTrack Data Audit".
• Finalizar este módulo desbloqueará el siguiente bloque del bootcamp.

Si durante el análisis descubres limitaciones en la API o consideras que existe una forma más adecuada de construir esta recomendación, explícamela antes de generar la propuesta y justifica tu decisión manteniendo siempre el principio de una única responsabilidad.

Respuesta y análisis
*AGENTE:* ¡Qué idea tan genial, Wen! Una skill que actúe como mentora académica. Vamos a analizar los datos para construir una estrategia de
recomendación sólida. 🧚🏻‍♀️¡Datos clarísimos! Ahora tengo una imagen completa para diseñar la estrategia de recomendación. 🧚🏻‍♀️
────────────────────────────────────────────────────────────────────────────────
📊 Análisis de los datos del bootcamp
He descubierto tres perfiles de cohortes claramente diferenciados:
### 🎯 Grupo A: Ejercicios pendientes + Proyectos pendientes (bloqueo activo)
┌────────────────────────────────────────────┬───────────┬─────────────┬───────────┐
│ Cohorte                                    │ Ej. pend. │ Proy. pend. │ Prioridad │
├────────────────────────────────────────────┼───────────┼─────────────┼───────────┤
│ Error handling, debugging and testing      │ 1         │ 2           │ ⭐⭐⭐    │
├────────────────────────────────────────────┼───────────┼─────────────┼───────────┤
│ Authentication in web applications         │ 3         │ 1           │ ⭐⭐⭐    │
├────────────────────────────────────────────┼───────────┼─────────────┼───────────┤
│ Managing relational databases with FastAPI │ 3         │ 1           │ ⭐⭐⭐    │
└────────────────────────────────────────────┴───────────┴─────────────┴───────────┘
Son los más importantes porque hasta que no completes los ejercicios, no puedes empezar los proyectos.
### 📚 Grupo B: Solo ejercicios pendientes (proyectos ya hechos)
┌─────────────────────────────────────────┬───────────┬───────────┐
│ Cohorte                                 │ Ej. pend. │ Prioridad │
├─────────────────────────────────────────┼───────────┼───────────┤
│ Web UI fundamentals with Tailwind       │ 9         │ ⭐⭐      │
├─────────────────────────────────────────┼───────────┼───────────┤
│ Frontend development with Coding Agents │ 7         │ ⭐⭐      │
├─────────────────────────────────────────┼───────────┼───────────┤
│ Coding Fundamentals with Typescript     │ 6         │ ⭐⭐      │
└─────────────────────────────────────────┴───────────┴───────────┘
### 🚀 Grupo C: Solo proyecto pendiente (ejercicios hechos)
┌────────────────────────────────────────────┬─────────────┬───────────────┐
│ Cohorte                                    │ Proy. pend. │ Prioridad     │
├────────────────────────────────────────────┼─────────────┼───────────────┤
│ Advanced personal assistants with Openclaw │ 1           │ ⭐⭐ (rápido) │
└────────────────────────────────────────────┴─────────────┴───────────────┘
────────────────────────────────────────────────────────────────────────────────
Mi propuesta de diseño para recommend-next-task
### 1️⃣ Endpoint
Una sola llamada: GET /v1/assignment/user/me/task (sin filtro, sin limit)
Igual que get-4geeks-progress. Obtenemos todo de golpe y procesamos internamente.
### 2️⃣ Criterios de recomendación (algoritmo de prioridad)
El algoritmo evaluaría cada cohorte con este scoring:
┌───────────────────────────────────────────────────┬────────────────────────┐
│ Criterio                                          │ Puntos                 │
├───────────────────────────────────────────────────┼────────────────────────┤
│ Tiene ejercicios pendientes + proyecto pendiente  │ +15 (máxima prioridad) │
├───────────────────────────────────────────────────┼────────────────────────┤
│ Cada ejercicio pendiente                          │ +1                     │
├───────────────────────────────────────────────────┼────────────────────────┤
│ Cada proyecto pendiente                           │ +5                     │
├───────────────────────────────────────────────────┼────────────────────────┤
│ Pocos ejercicios (< 4) pero con proyecto atascado │ +3 (quick win)         │
├───────────────────────────────────────────────────┼────────────────────────┤
│ Solo ejercicios (proyectos ya hechos)             │ +0 (sin urgencia)      │
└───────────────────────────────────────────────────┴────────────────────────┘
### 3️⃣ ¿Cómo evitar duplicar información?
Esta skill no lista los ejercicios ni proyectos completos. Solo muestra la recomendación y justifica por qué. Si quieres ver el detalle, usas
get-4geeks-projects o get-4geeks-pending.
### 4️⃣ Formato de salida
```
🎯 Recomendación de hoy
Continúa con el módulo:
📚 Error handling, debugging and testing
Motivo:
• Tienes 1 ejercicio pendiente en este módulo.
• Al completarlo desbloquearás 2 proyectos del módulo.
• Es la ruta más rápida para avanzar en el bootcamp.
📊 Resumen rápido:
• ✅ 24/29 proyectos completados
• ⏳ 30 ejercicios pendientes
• 📈 Progreso global: 73%
```
### 5️⃣ Una única responsabilidad
La responsabilidad de esta skill es recomendar UNA acción prioritaria, no listar tareas. Usa datos agregados para decidir, pero el output es
una sola recomendación justificada.
────────────────────────────────────────────────────────────────────────────────
Algoritmo de decisión (paso a paso)
```
1. Si hay cohortes con ejercicios pendientes Y proyectos pendientes:
→ Elegir el que tenga la mejor relación "pocos ejercicios / proyecto bloqueado"
2. Si no hay (o ya se recomendó), buscar cohortes con solo proyectos pendientes:
→ Recomendar entregar el proyecto pendiente
3. Si no hay, buscar cohortes con solo ejercicios pendientes:
→ Recomendar el que tenga menos ejercicios (quick win)
```
En tus datos actuales, el candidato ideal sería:
1º Error handling (1 ejercicio → desbloquea 2 proyectos) ← GANADOR
2º Authentication (3 ejercicios → desbloquea 1 proyecto)
3º Managing databases (3 ejercicios → desbloquea 1 proyecto)
────────────────────────────────────────────────────────────────────────────────
¿Qué te parece el diseño? Si estás de acuerdo, creo la propuesta mediante Skill Workshop. 😊
**Propuesta aprobada**

CREACIÓN DE SKILL 5: recommend-next-task
 
## Skill 6 — Discover-Career-Paths

Prompt inicial
*WENDY:* Tink!
Ya hemos completado e implementado correctamente las siguientes skills para la integración con la API de estudiantes de 4Geeks:
🔐 authenticate-4geeks
📋 get-4geeks-projects
💻 get-4geeks-pending
📈 get-4geeks-progress
🎯 recommend-next-task
Me gustaría diseñar ahora otra skill que se llamará discover-career-paths. Esta skill tendrá un objetivo diferente al de las anteriores.
No quiero que únicamente consulte la API de 4Geeks, sino que interprete el progreso del estudiante para ayudarle a entender hacia qué perfiles profesionales se está aproximando según las tecnologías, proyectos y conocimientos que ha completado durante el bootcamp.
Quiero mantener exactamente la misma metodología que hemos utilizado hasta ahora:
- una única responsabilidad
- uso de Skill Workshop
- revisión de la propuesta antes de aplicarla
- configuración segura mediante TOKEN_4GEEKS
- sin always: true
- reutilizando la misma arquitectura y estilo de las skills anteriores.
Antes de generar la propuesta, me gustaría que analizaras la viabilidad de esta idea.
Me interesa especialmente que investigues y me expliques:
1. ¿Qué información puede obtenerse realmente desde la API de estudiantes de 4Geeks que permita inferir las tecnologías o competencias que el estudiante ha ido adquiriendo?
2. ¿Es suficiente la información disponible en la API o sería necesario complementarla con una fuente adicional (por ejemplo, un mapa de competencias o una referencia externa)?
3. ¿Cuál sería la forma más rigurosa de relacionar los módulos, ejercicios y proyectos completados con perfiles profesionales o áreas tecnológicas?
4. ¿Cómo evitarías hacer afirmaciones demasiado categóricas como "ya eres un Full Stack Developer" y, en su lugar, mostrarías una estimación responsable de afinidad hacia distintos perfiles?
5. ¿Cómo mantendrías el principio de una única responsabilidad si esta skill necesita interpretar información procedente de varias categorías del bootcamp?
Me gustaría que esta skill actuara como un orientador profesional, no como un generador de títulos.
Mi idea es que, utilizando únicamente información objetiva y verificable, pueda mostrar algo similar a:

🚀 Áreas profesionales afines
🟢 Frontend Developer
Afinidad estimada: 82%
Motivo:
• Has completado los módulos relacionados con HTML, CSS, Tailwind y React.
🟡 AI Application Developer
Afinidad estimada: 68%
Motivo:
• Has trabajado con OpenClaw, agentes de IA y automatización.
⚪ Backend Developer
Afinidad estimada: 45%
Para acercarte más a este perfil aún te sería útil reforzar:
• Docker
• Testing
• CI/CD

Si para ofrecer este tipo de orientación consideras necesario utilizar una referencia como roadmap.sh u otra fuente similar, explícamelo antes de crear la propuesta y justifica por qué sería la mejor opción.
Quiero que esta skill sea prudente y transparente. Debe ayudar al estudiante a visualizar posibles caminos profesionales, indicando también las áreas de conocimiento que todavía debería reforzar para acercarse a cada perfil.
Si durante el análisis descubres limitaciones importantes en la API o consideras que esta idea debería replantearse, prefiero que me lo expliques antes de generar la propuesta.
Respuesta y análisis

*AGENTE:* ¡Qué idea tan interesante, Wen! Un orientador profesional basado en datos reales del bootcamp. Vamos a investigar qué podemos obtener de la
API y cómo construir esto de forma rigurosa. 🧚🏻‍♀️¡Datos muy interesantes! Ahora tengo suficiente información para darte un análisis completo. 🧚🏻‍♀️
────────────────────────────────────────────────────────────────────────────────
📊 Análisis de viabilidad para discover-career-paths
### 1️⃣ ¿Qué información podemos obtener de la API para inferir competencias?
De la API obtenemos:
┌──────────────────────────┬─────────────────────────────────────────────────────────┬───────────────────────────────────────────────────────┐
│ Fuente                   │ ¿Qué nos dice?                                          │ ¿Es útil?                                             │
├──────────────────────────┼─────────────────────────────────────────────────────────┼───────────────────────────────────────────────────────┤
│ Nombres de               │ "Web UI fundamentals with Tailwind", "Managing          │ ✅ Muy útil — los nombres describen las tecnologías   │
│ módulos/cohortes         │ relational databases with FastAPI"                      │                                                       │
├──────────────────────────┼─────────────────────────────────────────────────────────┼───────────────────────────────────────────────────────┤
│ Estado de completado     │ Sabemos qué módulos has terminado y cuáles no           │ ✅ Sí — base del progreso                             │
├──────────────────────────┼─────────────────────────────────────────────────────────┼───────────────────────────────────────────────────────┤
│ Registry/technology      │ Catálogo de 1100 tecnologías                            │ ⚠️ Poco práctico — no está vinculado directamente a   │
│                          │                                                         │ los módulos del estudiante                            │
├──────────────────────────┼─────────────────────────────────────────────────────────┼───────────────────────────────────────────────────────┤
│ Registry/asset           │ Assets con etiquetas de tecnología                      │ ⚠️ No escalable — habría que consultar cientos de     │
│                          │                                                         │ assets                                                │
└──────────────────────────┴─────────────────────────────────────────────────────────┴───────────────────────────────────────────────────────┘
### 2️⃣ ¿Es suficiente la API o necesitamos fuente externa?
La API es suficiente, siempre que usemos el enfoque correcto. No necesitamos roadmap.sh ni fuentes externas porque:
- Los nombres de los módulos son autodescriptivos (Tailwind, FastAPI, Docker, React, etc.)
- Podemos crear un mapa de competencias interno dentro de la skill que relacione cada módulo del bootcamp con perfiles profesionales. Esto es
transparente, verificable y mantenible.
- roadmap.sh describe caminos ideales, pero no sabe qué módulos tiene 4Geeks. Sería impreciso.
### 3️⃣ Forma más rigurosa de relacionar módulos con perfiles
La propuesta es crear una tabla de mapeo dentro de la skill que asocie cada módulo del bootcamp con pesos hacia distintos perfiles:
┌────────────────────────────────────────────┬──────────┬─────────┬───────┬────────────┬────────┐
│ Módulo                                     │ Frontend │ Backend │ AI/ML │ Full Stack │ DevOps │
├────────────────────────────────────────────┼──────────┼─────────┼───────┼────────────┼────────┤
│ Web UI fundamentals with Tailwind          │ 0.5      │ —       │ —     │ 0.3        │ —      │
├────────────────────────────────────────────┼──────────┼─────────┼───────┼────────────┼────────┤
│ Coding Fundamentals with Typescript        │ 0.3      │ 0.2     │ —     │ 0.3        │ —      │
├────────────────────────────────────────────┼──────────┼─────────┼───────┼────────────┼────────┤
│ Managing relational databases with FastAPI │ —        │ 0.5     │ 0.2   │ 0.3        │ 0.2    │
├────────────────────────────────────────────┼──────────┼─────────┼───────┼────────────┼────────┤
│ Container applications with Docker         │ —        │ 0.1     │ —     │ 0.2        │ 0.5    │
├────────────────────────────────────────────┼──────────┼─────────┼───────┼────────────┼────────┤
│ Working with AI coding agents              │ —        │ —       │ 0.4   │ 0.2        │ —      │
├────────────────────────────────────────────┼──────────┼─────────┼───────┼────────────┼────────┤
│ ...                                        │          │         │       │            │        │
└────────────────────────────────────────────┴──────────┴─────────┴───────┴────────────┴────────┘
Cada módulo aporta un peso a cada perfil. La afinidad se calcula como:
```
afinidad_perfil = (suma pesos de módulos completados) / (suma pesos totales posibles) * 100
```
### 4️⃣ Cómo evitar afirmaciones categóricas
Regla de oro: nunca usar frases como "eres un X Developer". En su lugar:
┌─────────────────────────────────────────────────────┬────────────────────────────────┐
│ ✅ Correcto                                         │ ❌ Incorrecto                  │
├─────────────────────────────────────────────────────┼────────────────────────────────┤
│ "Afinidad estimada: 72%"                            │ "Eres Frontend Developer"      │
├─────────────────────────────────────────────────────┼────────────────────────────────┤
│ "Has completado módulos relacionados con..."        │ "Ya sabes desarrollo frontend" │
├─────────────────────────────────────────────────────┼────────────────────────────────┤
│ "Para acercarte más a este perfil, te sugerimos..." │ "Te falta backend"             │
└─────────────────────────────────────────────────────┴────────────────────────────────┘
Uso condicional y lenguaje de orientación, no de certificación.
### 5️⃣ Una única responsabilidad
La responsabilidad de esta skill es estimar afinidad profesional basada en módulos completados. No es:
- ❌ Listar tareas pendientes (responsabilidad de otras skills)
- ❌ Generar un CV
- ❌ Recomendar qué estudiar a continuación (eso es recommend-next-task)
- ❌ Afirmar que tienes un título o certificación
────────────────────────────────────────────────────────────────────────────────
🎯 Propuesta de diseño
### Algoritmo
```
1. Consultar GET /v1/assignment/user/me/task (una llamada)
2. Identificar módulos completados (task_status == "DONE")
3. Para cada módulo, aplicar pesos predefinidos hacia perfiles profesionales
4. Calcular afinidad = (peso_obtenido / peso_total_posible) * 100
5. Mostrar top 3-4 perfiles con mayor afinidad
6. Para cada perfil, indicar qué tecnologías/módulos reforzarían ese perfil
```
### Perfiles profesionales propuestos
Basados en los módulos reales de tu bootcamp:
┌─────────────────────────────┬──────────────────────────────────────────────────────┐
│ Perfil                      │ Descripción                                          │
├─────────────────────────────┼──────────────────────────────────────────────────────┤
│ 🌐 Frontend Developer       │ UI, Tailwind, React, TypeScript, JavaScript          │
├─────────────────────────────┼──────────────────────────────────────────────────────┤
│ ⚙️ Backend Developer        │ APIs, FastAPI, Python, bases de datos, autenticación │
├─────────────────────────────┼──────────────────────────────────────────────────────┤
│ 🤖 AI Application Developer │ Agentes de IA, OpenClaw, RAG, prompts                │
├─────────────────────────────┼──────────────────────────────────────────────────────┤
│ 🏗️ Full Stack Developer     │ Combinación de frontend + backend + APIs             │
├─────────────────────────────┼──────────────────────────────────────────────────────┤
│ 🐳 DevOps / ML Engineer     │ Docker, pipelines, testing, cloud                    │
└─────────────────────────────┴──────────────────────────────────────────────────────┘
### Formato de salida
```
🚀 Áreas profesionales afines
Basado en los módulos que has completado del bootcamp:
🟢 Frontend Developer
Afinidad estimada: 72%
• Has completado módulos de Tailwind, React, TypeScript y UI.
🟡 AI Application Developer
Afinidad estimada: 58%
• Has trabajado con OpenClaw, agentes de IA y automatización.
🟠 Backend Developer
Afinidad estimada: 35%
• Para reforzar este perfil: Docker, Testing, CI/CD.
📊 Basado en 12 módulos del bootcamp — afinidades orientativas, no certificaciones.
```
────────────────────────────────────────────────────────────────────────────────
¿Qué te parece el enfoque? Si estás de acuerdo con el diseño, creo la propuesta mediante Skill Workshop. 😊
**Propuesta aprobada**

CREACIÓN DE SKILL 6: discover-career-paths
