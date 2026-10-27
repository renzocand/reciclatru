# modules/acceptances

Aceptación de pedidos por el reciclador (HU del reciclador: explorar y aceptar).

- `POST /orders/:id/acceptance` — acepta un pedido disponible; un pedido tiene una sola
  aceptación activa (restricción en base de datos y validación en el servicio).
- Cambia el estado del pedido y registra quién lo aceptó y cuándo.
- Desde la aceptación se habilita el contacto (teléfono y dirección) en los DTOs de los
  participantes; antes no se expone.
- Tests: doble aceptación, pedido no disponible y reciclador no autorizado.
