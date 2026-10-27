# src/database

Conexión a PostgreSQL con TypeORM: opciones de conexión, entidades base y el `DataSource` que
usan las migraciones.

- `synchronize: false`: el esquema cambia solo por migraciones versionadas en `migrations/`.
- Convenciones: tablas y columnas en `snake_case`, claves primarias UUID y `created_at` /
  `updated_at` en las entidades de negocio.
- Las entidades de cada dominio viven en su módulo (`modules/<dominio>/entities/`).
- Los tests unitarios no dependen de esta conexión.
