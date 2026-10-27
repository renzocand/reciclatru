# migrations

Migraciones de TypeORM: la única forma de cambiar el esquema de PostgreSQL (Principio IX de
la constitución).

- Una migración por cambio de esquema, con `up` y `down` reversibles.
- Nombre con timestamp y descripción en inglés (`1712345678901-CreateOrders.ts`).
- Se incluye en el PR de la HU que la necesita, junto con la entidad y los tests.
- Una migración ya aplicada no se edita ni se borra: se corrige con una nueva.
- Prohibido `synchronize: true` en entornos compartidos y los borrados físicos de registros de
  negocio (se usa cancelación lógica).
