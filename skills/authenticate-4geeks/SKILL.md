---
name: "authenticate-4geeks"
description: "Verify that the 4Geeks Academy student API token is valid and authentication works"
metadata:
  {
    "openclaw":
      {
        "primaryEnv": "TOKEN_4GEEKS",
        "emoji": "🔐",
      },
  }
---

# authenticate-4geeks

## Metadatos

- **Nombre:** authenticate-4geeks
- **Versión:** 1.0.0
- **Autor:** Wendy Sanchez
- **Categoría:** 4Geeks
- **Descripción:**
  Verifica que el token de estudiante de 4Geeks Academy es válido y que la autenticación con la API funciona correctamente.

- **Disparadores de activación:**
  - "Verifica mi token de 4Geeks"
  - "¿Funciona mi autenticación con 4Geeks?"
  - "Comprueba mi conexión con 4Geeks"
  - "Authenticate 4geeks"

---

## Cuándo utilizar esta skill

Utiliza esta skill cuando Wendy quiera verificar que su token de estudiante de 4Geeks Academy sigue siendo válido y que la API responde correctamente.

Úsala también como paso previo antes de ejecutar cualquier otra skill relacionada con 4Geeks, para confirmar que la autenticación funciona.

---

## Instrucciones

### Flujo de trabajo

1. Obtén el token de la variable de entorno `TOKEN_4GEEKS`, que OpenClaw inyecta automáticamente al estar configurado en `skills.entries.authenticate-4geeks.env`.

2. Realiza una petición GET a la API:
   ```
   GET https://breathecode.herokuapp.com/v1/admissions/user/me
   Authorization: Token ${TOKEN_4GEEKS}
   ```

3. Interpreta la respuesta:

   - **200 OK** — Token válido. La autenticación funciona correctamente.
     - Responde únicamente confirmando que la autenticación es correcta.
     - **No muestres ningún dato personal** (ni nombre, ni email, ni cohortes, ni academias).
     - La consulta del perfil del estudiante es responsabilidad de una skill independiente.
   
   - **401 Unauthorized** — Token inválido o expirado.
     - Indica que el token no es válido y sugiere renovarlo mediante `POST /v1/auth/login`.
   
   - **Error de conexión** — La API no responde.
     - Indica que no se pudo contactar con el servidor. Sugiere verificar la URL base o la conectividad.
   
   - **Otro código HTTP** — Muestra el código y el mensaje de error para depurar.

4. Entrega el resultado:
   - **Prioridad 1:** Telegram (si el canal está disponible).
   - **Prioridad 2:** Canal de conversación actual.

---

## Configuración

### Token

Se inyecta mediante OpenClaw desde la variable de entorno `TOKEN_4GEEKS`, configurada en `~/.openclaw/.env` y registrada en `skills.entries.authenticate-4geeks.env` dentro de `openclaw.json`.

### Endpoint

`GET https://breathecode.herokuapp.com/v1/admissions/user/me`

### Cabecera de autenticación

`Authorization: Token ${TOKEN_4GEEKS}`

> **Importante:** El prefijo es literalmente `Token` (con T mayúscula y espacio), no `Bearer`.

---

## Privacidad

Esta skill sigue el principio de mínimo acceso:

- No muestra ningún dato personal del estudiante.
- Su única responsabilidad es confirmar si la autenticación funciona o no.
- Los datos del perfil, cohortes, academias y demás información personal serán gestionados por skills independientes.

---

## Dependencias

- Conexión a Internet para alcanzar `breathecode.herokuapp.com`.
- Variable de entorno `TOKEN_4GEEKS` configurada en `~/.openclaw/.env`.
- Configuración en `openclaw.json`:
  ```json5
  {
    skills: {
      entries: {
        "authenticate-4geeks": {
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
