# modules/auth

Registro e inicio de sesión (HU de acceso). Valida credenciales y emite el JWT con el rol del
usuario.

- Hash de contraseñas con bcrypt (nunca texto plano) y comparación segura.
- `POST /auth/register` — crea vecino o reciclador con un rol permitido.
- `POST /auth/login` — devuelve el access token y los datos mínimos del usuario.
- `JwtAuthGuard` protege el resto de la API; los permisos por rol se verifican en cada
  servicio, no solo en el controlador.
- Tests: credenciales inválidas, rol inválido, correo duplicado y token vencido.
