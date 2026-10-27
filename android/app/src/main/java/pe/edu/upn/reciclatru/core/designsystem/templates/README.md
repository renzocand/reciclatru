# templates

Plantillas: el esqueleto de una pantalla completa sin datos reales. Definen barra superior,
pestañas, áreas de contenido y los slots de cargando, vacío y error.

Ejemplos: `RtTopBarScaffold` (barra superior + contenido), `RtOrdersTabsTemplate` (pestañas
Activos/Historial), `RtAsyncContentTemplate` (cargando, vacío, error, éxito).

La pantalla de la feature arma la plantilla con su `ViewModel`; la plantilla no conoce estado
de negocio ni navegación.
