# modules (backend)

Un módulo NestJS por dominio, con el mismo nombre que las features de los clientes: `auth`,
`users`, `orders`, `acceptances` y `photos`. Cada módulo es dueño de sus controladores,
servicios, DTOs, entidades y tests.

```text
modules/<dominio>/
├── <dominio>.module.ts
├── <dominio>.controller.ts
├── <dominio>.service.ts
├── dto/
├── entities/
└── <dominio>.service.spec.ts
```

- La lógica de negocio y los permisos viven en el servicio; el controlador solo valida el
  transporte y delega.
- Un módulo no toca las entidades de otro directamente: importa su módulo y usa su servicio o
  su repositorio.
- Los DTOs de respuesta cambian según rol y estado (privacidad de teléfono y dirección).
