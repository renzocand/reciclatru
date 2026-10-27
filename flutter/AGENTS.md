# AGENTS.md — Flutter (ReciclaTru)

Cliente iOS/multiplataforma. Flutter + Material 3, **Riverpod** para estado e inyección y
**go_router** para navegación. Se trabaja después de Android, pero comparte la misma API
y los mismos nombres de dominio. Lee primero `../AGENTS.md` y la constitución en
`../.specify/memory/constitution.md`.

## Arquitectura

Una **feature = un dominio de negocio**, con el mismo nombre que en Android y en el
backend: `auth`, `profile`, `orders`, `explore`, `tracking`. Capas:

```text
flutter/lib/
├── main.dart
├── core/                      # transversal, sin lógica de un dominio
│   ├── network/               # cliente HTTP, interceptores, errores de red
│   ├── session/               # token JWT, sesión y rol activo
│   ├── router/                # go_router, rutas y guardas de sesión/rol
│   └── designsystem/          # Atomic Design (tokens → plantillas)
└── features/
    ├── auth/                  # presentation/ domain/ data/
    ├── profile/
    ├── orders/
    ├── explore/
    └── tracking/
```

### Qué va en cada capa

- **`presentation/`**: pantallas/widgets propios de la feature, `ConsumerWidget` /
  `ConsumerStatefulWidget`, providers de presentación (`Notifier`, `AsyncNotifier`) y
  navegación de la feature. Es la **única** capa que conoce providers de estado y
  `go_router`. Los componentes visuales reutilizables **no** van acá: van al sistema de
  diseño.
- **`domain/`**: entidades de dominio, casos de uso y contratos de repositorio
  (`abstract class`). Dart puro, sin Flutter, sin Dio y sin Riverpod.
- **`data/`**: implementación de repositorios, DTOs (`fromJson`/`toJson`), mappers
  DTO ↔ dominio, fuentes de datos (Dio/almacenamiento seguro) y los providers que
  inyectan las implementaciones.

Flujo de dependencias: `presentation → domain ← data`. `presentation` nunca importa DTOs
ni clases de `data`; `domain` no importa Flutter.

## Reglas

1. **Riverpod**:
   - Providers globales (cliente HTTP, sesión) en `core/`, con `Provider`/`Notifier`.
   - Cada feature expone sus providers en `data/` (repositorio) y `presentation/`
     (estado de pantalla), con `AsyncNotifier` para carga asíncrona.
   - En tests, sustituir dependencias con `ProviderScope(overrides: [...])`; nunca llamar
     a la API real.
   - No usar `setState` para estado de negocio ni `Provider.of` con `listen: false`
     esparcido por el árbol: el estado vive en el provider.
2. **go_router**: las rutas viven en `core/router`; las guardas de sesión/rol redirigen,
   pero **el backend sigue siendo quien autoriza**. La pantalla no decide permisos.
3. **Nunca validar una regla de negocio solo en la app.** Permisos por rol, estados del
   pedido, privacidad de teléfono/dirección y restricciones se validan en la API. La app
   puede deshabilitar acciones por UX, pero siempre maneja el error del servidor.
4. **Atomic Design** (detalle en `core/designsystem/README.md`):
   - Solo componentes stateless con prefijo `Rt`, mismos nombres que en Android.
   - Reciben datos y callbacks; nunca providers, repositorios ni modelos de dominio.
   - Los átomos envuelven Material 3, no lo reemplazan.
   - Antes de crear un componente, buscar si ya existe uno equivalente.
   - Todo componente tiene una entrada en **Widgetbook**.
   - Las pantallas viven en `features/*/presentation`, no en el sistema de diseño.
5. **Privacidad**: no persistir la ubicación del reciclador; no mostrar teléfono ni
   dirección si el DTO del backend no los incluye para el rol/estado actual.
6. **Componentes clave con `Key`** (p. ej. `const Key('btn_publicar_pedido')`) para tests
   de widget estables.
7. **Sin dependencias nuevas** sin justificación en el plan de la HU; prohibido Firebase,
   Google Maps API y servicios de pago o con API key.
8. **Español** en textos visibles, mensajes y documentación; código en inglés salvo los
   nombres de dominio acordados.

## Compilar y probar

Desde `flutter/`:

```bash
flutter pub get       # instalar dependencias (solo si ya están declaradas)
flutter run           # correr en emulador/dispositivo
flutter analyze       # análisis estático (debe salir sin issues)
flutter test          # tests unitarios y de widget
flutter test --coverage
flutter build ios     # build iOS (requiere macOS/Xcode)
flutter build apk     # build Android de verificación
```

Cuando esté configurado Widgetbook, el catálogo se levanta con
`flutter run -t widgetbook/main.dart` (o el entrypoint acordado en el repo).

Antes de un PR: `flutter analyze`, `flutter test` y build del target correspondiente en
verde. Los tests usan repositorios falsos vía overrides de Riverpod.

## Convenciones de código

- Material 3; colores, tipografía, espaciado y radios **solo** desde los tokens del
  sistema de diseño, nunca valores sueltos.
- Widgets con `const` cuando sea posible; nombres en PascalCase y archivos en
  `snake_case.dart`.
- Estados asíncronos con `AsyncValue` (`loading`, `data`, `error`) y un estado vacío
  explícito cuando la lista no tiene elementos.
- No dejar código muerto ni TODOs sin dueño ni clave de Jira.
