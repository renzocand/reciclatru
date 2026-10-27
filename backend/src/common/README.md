# src/common

Piezas transversales de la API, sin lógica de un dominio.

- Guards: `JwtAuthGuard` y guard de roles (`@Roles(...)`).
- Decoradores: `@CurrentUser()`, `@Public()`.
- Filtros e interceptores: formato único de error y de respuesta, sin filtrar detalles
  internos.
- Pipes: validación y transformación con `ValidationPipe` (whitelist).
- Paginación y utilidades compartidas.

Si una pieza solo la usa un módulo, va en ese módulo.
