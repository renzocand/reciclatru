# atoms

Átomos: la unidad mínima de interfaz; envuelven un componente de Material 3 y le aplican los
tokens. Son stateless y reciben datos y callbacks.

Ejemplos: `RtButton` (principal, secundario, con carga), `RtTextField`, `RtBadge` (estado del
pedido: Disponible, Aceptado, En camino, Recogido, Cancelado), `RtIcon`, `RtSectionTitle`.

Un archivo por componente (`RtButton.kt`) y al menos un `@Preview` con sus estados.
