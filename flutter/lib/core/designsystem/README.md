# Sistema de diseño — ReciclaTru Flutter

Atomic Design aplicado a ReciclaTru: la interfaz se construye desde valores (tokens) hasta
plantillas de pantalla, de modo que vecinos y recicladores vean la misma app coherente sin
duplicar estilos. Ruta base: `flutter/lib/core/designsystem/`.

El equivalente en Android es
`android/app/src/main/java/pe/edu/upn/reciclatru/core/designsystem/` y usa **los mismos
nombres** (`RtButton`, `RtOrderCard`, ...), cambiando solo la tecnología.

```text
core/designsystem/
├── tokens/       # colores, tipografía, espaciado y radios
├── atoms/        # RtButton, RtTextField, RtBadge...
├── molecules/    # campo con etiqueta y error, ítem de material con kg...
├── organisms/    # tarjeta de pedido, lista paginada, galería de fotos...
├── templates/    # estructura de una pantalla (barra superior, pestañas, estados)
└── README.md
```

No existe una carpeta `pages/` ni `screens/`: las pantallas viven en cada feature (ver más
abajo).

## Los cinco niveles

### 1. Tokens

Los valores únicos del sistema. No son widgets: son la fuente de verdad de color, tipografía,
espaciado y radios, expuesta como objetos de Dart y consumida desde `RtTheme` (envoltorio de
`ThemeData` de Material 3).

- **Colores**: primario/secundario de marca, fondos, textos y colores semánticos de estado
  del pedido (disponible, aceptado, en camino, recogido, cancelado).
- **Tipografía**: títulos, cuerpo, etiquetas y textos de apoyo (`TextTheme`).
- **Espaciado**: escala fija (4, 8, 12, 16, 24, 32 lógicos) para márgenes y separaciones.
- **Radios**: tarjetas, campos de texto, botones y hojas.

Archivos previstos en `tokens/`: `rt_colors.dart`, `rt_typography.dart`, `rt_spacing.dart`,
`rt_radii.dart` y `rt_theme.dart` (que arma el `ThemeData` y expone los tokens por
`ThemeExtension`). Mientras no exista `RtTheme`, el `ThemeData` de la plantilla no es la
fuente oficial: no se agregan colores sueltos en los widgets.

**Regla**: ningún componente usa valores literales (`Color(0xFF...)`, `EdgeInsets.all(16)`,
`fontSize: 14`). Si un valor se repite, se convierte en token.

### 2. Átomos

La unidad mínima con identidad visual propia; envuelven un widget de Material 3 y le aplican
los tokens y el comportamiento de la app. No se pueden dividir sin perder sentido.

Ejemplos de ReciclaTru:

- `RtButton` — botón principal, secundario y de texto, con estado de carga y deshabilitado.
- `RtTextField` — campo de texto con las variantes de teclado (texto, número, teléfono).
- `RtBadge` — píldora de **estado del pedido** (Disponible, Aceptado, En camino, Recogido,
  Cancelado) con su color semántico.
- `RtIcon`, `RtSectionTitle`, `RtProgressIndicator`.

### 3. Moléculas

Dos o más átomos combinados para resolver una tarea pequeña y concreta, con un significado
propio.

Ejemplos de ReciclaTru:

- `RtLabeledField` — etiqueta + campo + mensaje de error + texto de ayuda.
- `RtMaterialWeightItem` — nombre del material (plástico, cartón, vidrio...) con su peso en
  kg y acción para editarlo.
- `RtContactRow` — nombre de la persona, rol y botón **Llamar**.
- `RtSearchField` — campo de búsqueda con icono y acción de limpiar.

### 4. Organismos

Secciones completas de interfaz: agrupan moléculas y átomos y cubren un bloque funcional que
puede repetirse en varias pantallas.

Ejemplos de ReciclaTru:

- `RtOrderCard` — tarjeta con materiales, peso total, dirección aproximada, distancia y
  estado del pedido.
- `RtOrderItemsForm` — formulario de ítems del pedido (agregar, quitar, validar peso).
- `RtPaginatedOrderList` — lista paginada con "cargar más" y su estado vacío.
- `RtPhotoGallery` — galería de fotos de evidencia con vista ampliada.
- `RtEmptyState` — estado vacío con ilustración, mensaje y acción sugerida.

### 5. Plantillas

El esqueleto de una pantalla: define dónde se ubican la barra superior, las pestañas, el
contenido y los estados de carga/error, pero **no** contiene datos reales ni lógica. Las
pantallas de cada feature usan la plantilla y le pasan su contenido.

Ejemplos de ReciclaTru:

- `RtTopBarScaffold` — pantalla con barra superior, título, acción de volver y contenido.
- `RtOrdersTabsTemplate` — estructura con pestañas **Activos / Historial**.
- `RtAsyncContentTemplate` — decide qué mostrar según `loading`, `data`, `error` o lista
  vacía, con slots para cada caso.

## Dónde NO van las páginas

Las pantallas **no** pertenecen al sistema de diseño. Viven en cada feature:

```text
features/<dominio>/presentation/     # LoginScreen, CreateOrderScreen, ExploreScreen...
android: feature/<dominio>/ui/
```

Son las **únicas** que:

- conocen sus providers (`ref.watch(...)`, `ConsumerWidget`);
- observan el estado (`AsyncValue`, `Notifier`);
- disparan navegación (`context.go`, `context.push` de go_router);
- llaman casos de uso a través de su provider de estado.

Si un widget del sistema de diseño necesita un provider, un repositorio, un caso de uso o una
entidad de dominio, está en el lugar equivocado: o se convierte en pantalla de la feature, o
se le pasan datos simples y callbacks.

## Reglas del sistema de diseño

1. **Todo es stateless, del átomo a la plantilla.** Reciben datos ya listos (textos, números,
   booleanos, listas de ítems de UI) y callbacks (`onPressed`, `onChanged`). Nunca reciben
   `ref`, providers, repositorios, casos de uso, DTOs ni entidades de dominio.
2. **Los átomos envuelven Material 3, no lo reemplazan.** `RtButton` delega en
   `FilledButton`/`OutlinedButton` y `RtTextField` en `TextFormField`: así se conservan
   accesibilidad, ripple, estados y theming. No se reimplementa un botón desde cero con
   `GestureDetector` y `Container`.
3. **Prefijo `Rt` y mismos nombres en Android y Flutter.** `RtOrderCard` es `RtOrderCard` en
   ambos clientes; cambia el archivo y el lenguaje, no el nombre.
4. **Antes de crear un componente, revisar si ya existe.** Buscar por nombre y por variante
   en `core/designsystem/`; si existe, se amplía con parámetros en lugar de duplicarlo.
5. **Todo componente tiene una entrada en Widgetbook**, con al menos el estado normal y los
   estados relevantes (error, deshabilitado, texto largo, lista vacía). Las historias usan
   datos de ejemplo del dominio, no la API.
6. **Solo tokens.** Colores, tipografía, espaciado y radios salen de `tokens/`.
7. **Sin lógica de negocio.** El sistema de diseño no decide permisos, visibilidad de
   teléfono/dirección ni transiciones de estado: el backend es la fuente de verdad y la
   pantalla solo dibuja lo que recibe.
8. **Accesibilidad y pruebas**: `Semantics` para lectores de pantalla, objetivos táctiles de
   al menos 48 lógicos y `Key` en los componentes clave (lo aplica la pantalla con
   `const Key('btn_publicar_pedido')`).
9. **Iconografía de Material Icons**; no se agregan librerías de íconos ni de UI.

## Nombres acordados entre Android y Flutter

| Componente               | Nivel     | Flutter                                    | Android                              |
| ------------------------ | --------- | ------------------------------------------ | ------------------------------------ |
| `RtButton`               | átomo     | `atoms/rt_button.dart`                     | `atoms/RtButton.kt`                  |
| `RtTextField`            | átomo     | `atoms/rt_text_field.dart`                 | `atoms/RtTextField.kt`               |
| `RtBadge`                | átomo     | `atoms/rt_badge.dart`                      | `atoms/RtBadge.kt`                   |
| `RtLabeledField`         | molécula  | `molecules/rt_labeled_field.dart`          | `molecules/RtLabeledField.kt`        |
| `RtMaterialWeightItem`   | molécula  | `molecules/rt_material_weight_item.dart`   | `molecules/RtMaterialWeightItem.kt`  |
| `RtContactRow`           | molécula  | `molecules/rt_contact_row.dart`            | `molecules/RtContactRow.kt`          |
| `RtOrderCard`            | organismo | `organisms/rt_order_card.dart`             | `organisms/RtOrderCard.kt`           |
| `RtOrderItemsForm`       | organismo | `organisms/rt_order_items_form.dart`       | `organisms/RtOrderItemsForm.kt`      |
| `RtPaginatedOrderList`   | organismo | `organisms/rt_paginated_order_list.dart`   | `organisms/RtPaginatedOrderList.kt`  |
| `RtPhotoGallery`         | organismo | `organisms/rt_photo_gallery.dart`          | `organisms/RtPhotoGallery.kt`        |
| `RtEmptyState`           | organismo | `organisms/rt_empty_state.dart`            | `organisms/RtEmptyState.kt`          |
| `RtTopBarScaffold`       | plantilla | `templates/rt_top_bar_scaffold.dart`       | `templates/RtTopBarScaffold.kt`      |
| `RtOrdersTabsTemplate`   | plantilla | `templates/rt_orders_tabs_template.dart`   | `templates/RtOrdersTabsTemplate.kt`  |
| `RtAsyncContentTemplate` | plantilla | `templates/rt_async_content_template.dart` | `templates/RtAsyncContentTemplate.kt` |

## Cómo decidir el nivel de un componente nuevo

Responder en orden; la primera respuesta afirmativa decide:

1. ¿Es un **valor** reutilizable (color, tamaño, espaciado, radio, estilo de texto)?
   → **token**.
2. ¿Es **un solo control de Material 3** estilizado (botón, campo, badge, ícono)?
   → **átomo**.
3. ¿Combina **dos o más átomos** para una tarea pequeña con significado propio (campo con
   etiqueta y error, ítem de material con kg, fila de contacto con botón llamar)?
   → **molécula**.
4. ¿Es un **bloque completo** de pantalla que podría repetirse (tarjeta de pedido,
   formulario de ítems, lista paginada, galería, estado vacío)?
   → **organismo**.
5. ¿Define la **estructura de una pantalla entera** sin datos reales, con slots para barra
   superior, pestañas y estados cargando/vacío/error?
   → **plantilla**.
6. ¿Necesita `ref`, un provider, navegación o llamadas a la API? → **no es del sistema de
   diseño**: va a `features/<dominio>/presentation/` (Android: `feature/<dominio>/ui/`).

Casos dudosos frecuentes:

- Un botón con ícono y texto sigue siendo **átomo** (`RtButton` con parámetro `icon`), no una
  molécula.
- Una tarjeta con estado + materiales + acción es un **organismo**, porque reúne moléculas.
- Una lista con su paginación y su estado vacío es un **organismo**; la pantalla que la usa
  y decide qué pedir es de la **feature**.
- Repetir la estructura "barra superior + contenido + pull to refresh" es una **plantilla**;
  el contenido concreto lo pone la pantalla.

## Checklist para agregar un componente

1. Buscar uno equivalente en `core/designsystem/` (y en el README del nivel).
2. Definir el nombre `Rt...` y confirmar que será idéntico en Android.
3. Crear el archivo en el nivel que corresponda.
4. Hacerlo stateless: parámetros de datos + callbacks, aceptando `Key? key` y `Widget? child`
   cuando aplique.
5. Usar solo tokens; verificar contraste y tamaño táctil.
6. Agregar la historia en Widgetbook con los estados relevantes.
7. Anotar el ejemplo en el README del nivel si es un componente nuevo del catálogo.
8. En el PR: ¿hay duplicado?, ¿tiene historia?, ¿respeta tokens?, ¿lo usa alguna feature?
