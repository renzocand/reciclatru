# modules/users

Perfil del usuario y datos de contacto (HU de perfil).

- `GET /users/me` y `PATCH /users/me` — solo el usuario autenticado sobre su propio registro.
- DTOs distintos según rol y contexto: el teléfono y la dirección se incluyen solo cuando la
  regla lo permite (nunca en respuestas públicas).
- La actualización valida formato y campos permitidos; el rol no se cambia desde el perfil.
- Tests: acceso a un usuario ajeno, campo no permitido y contacto oculto para el rol que no
  corresponde.
