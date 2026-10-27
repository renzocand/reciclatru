# modules/photos

Fotos de evidencia del pedido (HU de seguimiento y cierre).

- `POST /orders/:id/photos` y `GET /orders/:id/photos` — solo para los participantes del
  pedido (vecino dueño o reciclador que lo aceptó).
- Guarda el archivo en el almacenamiento local del servidor y sus metadatos en PostgreSQL
  (autor, pedido, tipo y fecha); nada de servicios de pago ni API keys.
- Valida tipo y tamaño del archivo y limita la cantidad por pedido.
- Tests: subida por un tercero, archivo inválido y listado según el estado del pedido.
