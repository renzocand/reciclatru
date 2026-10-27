# organisms

Organismos: bloques completos de pantalla que agrupan moléculas y átomos.

Ejemplos: `RtOrderCard` (tarjeta de pedido), `RtOrderItemsForm` (formulario de materiales y
pesos), `RtPaginatedOrderList` (lista paginada con "cargar más"), `RtPhotoGallery` (fotos de
evidencia), `RtEmptyState` (estado vacío con acción).

No piden datos a la API ni navegan: reciben la lista ya formateada y callbacks. Un archivo por
componente, con entrada en Widgetbook para cada estado.
