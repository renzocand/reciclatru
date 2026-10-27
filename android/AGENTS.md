# AGENTS.md — Android (ReciclaTru)

Cliente principal del proyecto. Kotlin + Jetpack Compose + Material 3, arquitectura MVVM
por feature e inyección de dependencias con Hilt. Paquete base:
`pe.edu.upn.reciclatru`.

Lee primero `../AGENTS.md` y la constitución en `../.specify/memory/constitution.md`.

## Arquitectura

Una **feature = un dominio de negocio**, con el mismo nombre que en Flutter y en el
backend: `auth`, `profile`, `orders`, `explore`, `tracking`. Cada feature se divide en
tres capas:

```text
android/app/src/main/java/pe/edu/upn/reciclatru/
├── core/                      # transversal, sin lógica de un dominio
│   ├── network/               # cliente HTTP, interceptores, errores de red
│   ├── session/               # token JWT, sesión y rol activo
│   ├── navigation/            # rutas, NavHost y guardas de navegación
│   ├── di/                    # módulos Hilt globales
│   └── designsystem/          # Atomic Design (tokens → plantillas)
├── feature/
│   ├── auth/                  # ui/ domain/ data/
│   ├── profile/
│   ├── orders/
│   ├── explore/
│   └── tracking/
└── MainActivity.kt
```

### Qué va en cada capa

- **`ui/`**: pantallas Compose (`*Screen.kt`), ViewModels (`*ViewModel.kt`), estados de
  UI y navegación de la feature. Es la **única** capa que conoce el ViewModel.
  Los componentes visuales reutilizables **no** van acá: van al sistema de diseño.
- **`domain/`**: modelos de dominio, casos de uso y contratos de repositorio
  (`interface`). Kotlin puro, sin Android, sin Retrofit y sin anotaciones de Hilt.
- **`data/`**: implementación de los repositorios, DTOs de la API, mappers DTO ↔ dominio,
  fuentes de datos (Retrofit/DataStore) y el módulo Hilt que une las interfaces de
  `domain` con sus implementaciones.

Flujo de dependencias: `ui → domain ← data`. La capa `ui` nunca importa DTOs ni clases de
`data`; `domain` no depende de Android.

## Reglas

1. **MVVM**: cada pantalla tiene un `ViewModel` que expone un `StateFlow<UiState>`
   inmutable, recibe eventos y llama casos de uso. La pantalla solo dibuja estado y envía
   eventos; no hace llamadas de red ni cálculos de negocio.
2. **Hilt**: `@HiltAndroidApp` en la clase `Application`, `@AndroidEntryPoint` en
   actividades/fragmentos y `@HiltViewModel` en los ViewModels. Módulos globales en
   `core/di`; el binding repositorio ↔ implementación de cada feature en su `data/`.
   No crear instancias manuales de repositorios ni de Retrofit dentro de Compose.
3. **Nunca validar una regla de negocio solo en la app.** El backend es la fuente de
   verdad: permisos por rol, estados del pedido, privacidad de teléfono/dirección y
   cualquier restricción se validan en la API. La app puede deshabilitar botones o mostrar
   mensajes para mejorar la UX, pero siempre debe manejar el error del servidor.
4. **Atomic Design** (detalle en `core/designsystem/README.md`):
   - `core/designsystem` contiene **solo** componentes stateless con prefijo `Rt` y los
     mismos nombres que en Flutter.
   - Del átomo a la plantilla: reciben datos y callbacks; nunca ViewModels, repositorios
     ni modelos de dominio.
   - Los átomos envuelven Material 3, no lo reemplazan.
   - Antes de crear un componente, buscar si ya existe uno equivalente.
   - Todo componente lleva `@Preview`.
   - Las pantallas viven en `feature/*/ui`, no en el sistema de diseño.
5. **Privacidad**: no persistir la ubicación del reciclador; no mostrar teléfono ni
   dirección si el DTO del backend no los incluye para el rol/estado actual.
6. **Componentes clave con `testTag`** (p. ej. `RtButton` recibe `modifier` y la pantalla
   aplica `Modifier.testTag("btn_publicar_pedido")`) para tests de UI estables.
7. **Sin dependencias nuevas** sin justificación en el plan de la HU; prohibido Firebase,
   Google Maps API y servicios de pago o con API key.
8. **Español** en nombres de pantallas visibles, mensajes y documentación. El código
   (clases, funciones, columnas) en inglés, salvo los nombres de dominio ya acordados.

## Compilar y probar

Desde `android/` (en Windows usar `.\gradlew.bat`):

```bash
./gradlew assembleDebug        # compilar APK de debug
./gradlew test                 # tests unitarios JVM (ViewModels, mappers)
./gradlew connectedAndroidTest # tests instrumentados de UI (requiere emulador/dispositivo)
./gradlew lint                 # Android Lint
./gradlew :app:compileDebugKotlin  # verificación rápida de compilación Kotlin
```

Antes de un PR: `test`, `lint` y `assembleDebug` en verde. Los tests de UI usan
repositorios falsos inyectados con Hilt (`@TestInstallIn` / `HiltTestApplication`), nunca
la API real.

## Convenciones de código

- Compose con Material 3; colores, tipografía, espaciado y radios **solo** desde los
  tokens del sistema de diseño, nunca valores sueltos.
- Un archivo por pantalla, ViewModel y caso de uso relevante; nombres en PascalCase.
- `modifier: Modifier = Modifier` como parámetro de todo composable público.
- Estados de UI explícitos (`Loading`, `Success`, `Empty`, `Error`) y textos de error
  entendibles para el vecino/reciclador.
- No dejar código muerto ni TODOs sin dueño ni clave de Jira.
