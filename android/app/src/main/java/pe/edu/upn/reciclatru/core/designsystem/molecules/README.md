# molecules

Moléculas: dos o más átomos combinados para resolver una tarea pequeña con significado propio.

Ejemplos: `RtLabeledField` (etiqueta + campo + error), `RtMaterialWeightItem` (material con
peso en kg), `RtContactRow` (nombre + botón Llamar), `RtSearchField`.

Stateless: reciben textos, valores y callbacks; nunca ViewModel, repositorio ni modelo de
dominio. Un archivo por componente, con `@Preview`.
