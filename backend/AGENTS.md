# AGENTS.md — Backend (ReciclaTru)

API REST del proyecto: NestJS + PostgreSQL + TypeORM (CommonJS). Es la **única fuente de
verdad** de reglas de negocio, permisos y privacidad. Lee primero `../AGENTS.md` y la
constitución en `../.specify/memory/constitution.md`.

## Arquitectura

Un **módulo NestJS por dominio**, con el mismo nombre que las features de los clientes:

```text
backend/
├── src/
│   ├── modules/
│   │   ├── auth/          # registro, login, JWT, roles
│   │   ├── users/         # perfil y datos de contacto
│   │   ├── orders/        # pedidos, materiales y pesos
│   │   ├── acceptances/   # aceptación de pedidos por reciclador
│   │   └── photos/        # fotos de evidencia
│   ├── config/            # configuración tipada y validada desde variables de entorno
│   ├── database/          # conexión TypeORM, entidades base y data source de migraciones
│   ├── common/            # guards, decoradores, filtros, interceptores, paginación
│   ├── app.module.ts
│   └── main.ts
├── migrations/            # migraciones TypeORM versionadas
├── test/                  # tests e2e de la API
└── package.json
```

Cada módulo de dominio contiene su `*.module.ts`, controlador(es), servicio(s), DTOs de
entrada/salida, entidad(es) y tests. La lógica de negocio vive en el servicio, no en el
controlador (el controlador solo valida el transporte y delega).

## Reglas

1. **Reglas de negocio y permisos por rol siempre en el backend.** No confiar en que el
   cliente envíe el rol o el estado correcto. Usar guards (`JwtAuthGuard`, guard de rol),
   verificar propiedad del recurso y transiciones de estado válidas en el servicio.
   Toda regla debe tener test unitario y, si es un flujo principal, test e2e.
2. **Privacidad por diseño**:
   - El teléfono, la dirección y las coordenadas exactas se incluyen **solo** en los DTOs
     del rol y del estado que la regla permite (p. ej. el reciclador ve el contacto
     cuando el pedido está aceptado; el vecino no ve datos innecesarios del reciclador).
   - DTOs de respuesta distintos según rol y estado; nunca devolver la entidad completa ni
     campos sensibles "por comodidad".
   - **Nunca almacenar la ubicación del reciclador.** Si se usa para calcular cercanía, se
     procesa en memoria y no se persiste.
   - Contraseñas con **bcrypt** (nunca texto plano), autenticación **JWT** y secretos solo
     en variables de entorno (`JWT_SECRET`, credenciales de base de datos). `.env` no se
     versiona.
3. **Migraciones de TypeORM para cualquier cambio de esquema.** Toda modificación de
   entidades (tabla, columna, índice, relación, enum) va acompañada de su migración en
   `migrations/` y se incluye en el PR de la HU. No usar `synchronize: true` en entornos
   compartidos.
4. **Cancelación lógica**: los registros de negocio no se borran físicamente cuando la
   regla indica cancelación; se marca el estado correspondiente (y la fecha si aplica).
5. **Contrato único para ambos clientes**: endpoints REST documentados con Swagger
   (`/api/docs`), sin endpoints exclusivos de un cliente salvo justificación en el plan de
   la HU. Versionar/actualizar el contrato en el mismo PR.
6. **Sin servicios de pago ni API keys**: nada de Firebase, Google Maps API ni servicios
   que requieran tarjeta (Principio VIII de la constitución).
7. **Validación de entrada** con DTOs y `ValidationPipe` (whitelist, transform); mensajes
   de error claros para el usuario final y sin filtrar detalles internos.
8. **Español** en documentación, Swagger y mensajes de error visibles; código en inglés.

## Correr y probar

Desde `backend/`:

```bash
npm install            # dependencias (si no están instaladas)
npm run start:dev      # API en modo desarrollo con recarga
npm run start:prod     # API compilada
npm run build          # compilar a dist/
npm run lint           # oxlint
npm test               # tests unitarios (Jest)
npm run test:watch     # tests en modo watch
npm run test:cov       # cobertura
npm run test:e2e       # tests e2e de la API
```

- `npm test` y `npm run test:e2e` deben pasar antes de un PR, junto con `npm run lint` y
  `npm run build`.
- Los tests unitarios no dependen de una base real; los e2e levantan la app con una base
  de prueba o mocks según lo definido en la HU.
- Variables de entorno en un `.env` local (nunca commiteado). Para las migraciones se usa
  el data source de `src/database`; los comandos concretos de TypeORM se documentan al
  configurarlo.

## Convenciones

- Un módulo por dominio; no crear un "módulo general" con lógica mezclada.
- Nombres de entidades y columnas en inglés, `snake_case` en la base de datos y
  `PascalCase`/`camelCase` en TypeScript.
- DTOs separados por caso de uso y por rol cuando la privacidad lo exija; usar mappers
  explícitos, no exponer entidades.
- Sin secretos ni credenciales en el código ni en los tests.
- No dejar código muerto ni TODOs sin dueño ni clave de Jira.
