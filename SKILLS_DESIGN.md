# SKILLS_DESIGN.md
**Diseño de Skills Personalizadas para Tink**

## Introducción

Este documento describe el diseño de las skills personalizadas desarrolladas para Tink, mi asistente personal basado en OpenClaw.

El objetivo no es únicamente automatizar tareas, sino crear un flujo de trabajo que me ayude a aprender de forma más eficiente, organizar mejor mi conocimiento y transformarlo en contenido útil para compartir con otras personas.

Las skills complementan la personalidad definida en los archivos `IDENTITY.md`, `SOUL.md`, `USER.md`, `AGENTS.md` y `TOOLS.md`, pero no la modifican. Tink sigue siendo el mismo asistente; simplemente activa comportamientos especializados cuando una tarea lo requiere.

---

## Filosofía

Mi forma de aprender sigue siempre el mismo proceso:

Información → Comprensión → Comunicación.

Primero necesito comprender realmente un tema para sentirme segura utilizándolo. Después, cuando soy capaz de explicarlo de forma sencilla, sé que realmente lo he aprendido.

Por ello las dos skills están diseñadas para trabajar juntas.

```text
            Tema de estudio
                   │
                   ▼
          📚 Tink Academy
                   │
          Comprensión profunda
                   │
                   ▼
          ✨ Tink StoryLab
                   │
          Publicación para LinkedIn
                   │
                   ▼
      Compartir conocimiento
```

---

## Skill 1 – 📚 Tink Academy

### Objetivo

Convertir cualquier tema de estudio en una guía de aprendizaje clara, organizada y fácil de repasar.

---

### ¿Qué hace esta skill?

A partir de un tema, documento o conjunto de apuntes proporcionados por Wendy, la skill investiga el contexto cuando sea necesario y genera una guía de estudio completa.

El objetivo no es únicamente resumir información, sino facilitar la comprensión profunda del tema.

---

### ¿Qué información necesita?

La skill puede recibir:

- Un tema.
- Un título.
- Un documento.
- Un conjunto de apuntes.
- Un enlace.
- Un texto libre.

También utiliza automáticamente el contexto definido en USER.md para adaptar el nivel de explicación a las preferencias de aprendizaje de Wendy.

---

### ¿Cómo es un buen resultado?

La guía generada debe incluir:

- Explicación paso a paso.
- Conceptos importantes.
- Analogías sencillas cuando ayuden a comprender.
- Ejemplos prácticos.
- Errores frecuentes.
- Preguntas habituales de entrevistas técnicas o exámenes relacionados con el tema.
- Un resumen final con las ideas clave.

El documento se guarda automáticamente en Google Docs y Tink envía una notificación por Telegram indicando que está listo.

---

### Servicios utilizados

- Google Docs
- Telegram

---

## Skill 2 – ✨ Tink StoryLab

### Objetivo

Transformar conocimientos técnicos en publicaciones atractivas para LinkedIn utilizando storytelling y analogías.

---

### ¿Qué hace esta skill?

A partir de un concepto técnico, una tecnología, una noticia o un tema de estudio, la skill investiga la información necesaria y redacta una publicación preparada para LinkedIn.

El objetivo es convertir conceptos complejos en contenido fácil de entender para cualquier persona.

---

### ¿Qué información necesita?

La skill puede recibir:

- Un tema.
- Un título.
- Un documento.
- Una noticia.
- Un enlace.
- Un texto libre.

Utiliza automáticamente la información de USER.md para respetar el estilo de comunicación de Wendy y su marca personal.

---

### ¿Cómo es un buen resultado?

La publicación debe:

- Tener un inicio atractivo que despierte curiosidad.
- Explicar el concepto mediante storytelling.
- Utilizar analogías con superhéroes, personajes de Disney u otros referentes populares cuando ayuden a comprender el tema.
- Mantener un tono cercano, profesional, empático y natural.
- Sonar como si hubiera sido escrita por una copywriter y storyteller senior.
- Invitar a la conversación mediante una pregunta final.

El resultado se entrega por Telegram listo para copiar y publicar desde el móvil.

---

### Servicios utilizados

- Telegram

---

## Beneficios esperados

Estas dos skills buscan reducir el tiempo que dedico a dos tareas que realizo constantemente:

- Estudiar nuevas tecnologías.
- Compartir conocimiento en LinkedIn.

Además, ambas skills se complementan entre sí: una ayuda a comprender y la otra ayuda a comunicar.

De esta forma, Tink se convierte no solo en un asistente personal, sino también en un compañero de aprendizaje y creación de contenido.
