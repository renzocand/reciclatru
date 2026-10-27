# molecules

Moléculas: dos o más átomos combinados para resolver una tarea pequeña con significado propio.

Ejemplos: `RtLabeledField` (etiqueta + campo + error), `RtMaterialWeightItem` (material con
peso en kg), `RtContactRow` (nombre + botón Llamar), `RtSearchField`.

Stateless: reciben textos, valores y callbacks; nunca `ref`, un provider, un repositorio ni
una entidad de dominio. Un archivo por componente, con entrada en Widgetbook.
