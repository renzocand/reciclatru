# modules/orders

Pedidos del vecino: publicación con materiales y pesos, consulta y cancelación (HU de
publicación y gestión).

- `POST /orders`, `GET /orders/mine`, `GET /orders/:id` y `PATCH /orders/:id/cancel`.
- Verifica la propiedad del pedido y las transiciones de estado válidas antes de escribir.
- La cancelación es lógica (estado `cancelled`), nunca un borrado físico.
- La lista de pedidos disponibles filtra por estado y aplica los DTOs de privacidad del rol
  que consulta.
- Tests: publicar sin materiales, cancelar un pedido ajeno y cancelar uno ya aceptado.
