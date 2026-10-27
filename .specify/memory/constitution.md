<!--
SYNC IMPACT REPORT (temporal; eliminar antes de hacer commit)
- Cambio de versión: (plantilla sin ratificar) → 1.0.0
- Principios modificados: ninguno renombrado (adopción inicial de I–X).
- Secciones añadidas: Core Principles (I–X), "Stack y Restricciones Técnicas",
  "Flujo de Desarrollo y Puertas de Calidad", Governance.
- Secciones eliminadas: ninguna (se reemplazaron todos los placeholders de la plantilla).
- TODOs diferidos: ninguno. Fecha de ratificación = fecha de adopción (2026-10-26).
-->
# ReciclaTru Constitution

ReciclaTru conecta a vecinos de Trujillo (Perú) con recicladores. Es desarrollada por un equipo
universitario de 7 personas; el Release 1 se entrega el 26 de noviembre de 2026.

## Core Principles

### I. Backend como única fuente de verdad (NO NEGOCIABLE)
Toda regla de negocio, permiso por rol y regla de privacidad MUST validarse en la API NestJS.
Los clientes (Android y Flutter) MUST NOT decidir qué datos puede ver un usuario: la API responde
con DTOs distintos según el rol y el estado. Ninguna regla MUST vivir solo en un cliente; un
cliente puede replicar una validación para mejorar la experiencia, pero nunca sustituye la de la API.

### II. Un solo backend, dos clientes
Android (Kotlin, principal y primero) y Flutter (iOS y multiplataforma, después) MUST consumir la
misma API REST documentada en Swagger. No se crean endpoints exclusivos de un cliente salvo
justificación documentada en el plan de la HU.

### III. Historia de usuario como corte vertical
Cada HU MUST entregarse de punta a punta (pantalla + API + base de datos) y ser demostrable al
usuario. No existen HU "solo de backend" ni "solo de UI". Cada HU se trabaja en su propia rama y
se integra con un único Pull Request.

### IV. Arquitectura por feature y MVVM
Ambos clientes MUST organizarse por dominio (auth, profile, orders, explore, tracking) con capas
ui/presentation, domain y data. Android usa MVVM con Hilt; Flutter usa Riverpod. El backend tiene
un módulo NestJS por dominio. La misma feature MUST tener el mismo nombre en los tres proyectos.

### V. Atomic Design en ambos clientes
El sistema de diseño es compartido en concepto: tokens, átomos, moléculas, organismos y
plantillas, con prefijo "Rt" y los mismos nombres en Android y Flutter.
- Del átomo a la plantilla todo MUST ser stateless (datos y callbacks; nunca ViewModels,
  repositorios ni modelos de dominio).
- Solo las páginas de cada feature conocen estado y navegación.
- Los átomos envuelven Material 3, no lo reemplazan.
- Antes de crear un componente MUST revisarse si ya existe.
- Todo componente MUST tener preview (Compose) o entrada en Widgetbook (Flutter).

### VI. Privacidad por diseño (NO NEGOCIABLE)
Los datos personales (teléfono, dirección, coordenadas exactas) MUST exponerse solo a quien la
regla de negocio lo permite y en el estado que corresponde. La ubicación del reciclador MUST NOT
almacenarse. Las contraseñas MUST guardarse con bcrypt, la autenticación MUST usar JWT y los
secretos MUST vivir solo en variables de entorno (nunca en el repositorio).

### VII. Pruebas con propósito
- Toda regla de negocio del backend MUST tener test unitario; los flujos principales MUST tener
  test e2e de API.
- En los clientes: tests unitarios de ViewModels/providers y tests de UI con repositorios falsos
  inyectados (Hilt / overrides de Riverpod).
- Los componentes clave MUST usar testTag (Android) o Key (Flutter) para pruebas estables.
- Un PR MUST NOT integrarse si la CI falla.

### VIII. Simplicidad y alcance
Solo se construye lo que está en el release actual. Se prohíben servicios de pago y dependencias
que requieran tarjeta o API key (no Firebase, no Google Maps API). Se MUST preferir la solución
más simple que cumpla los criterios de aceptación; toda complejidad extra MUST justificarse en el
plan.

### IX. Base de datos versionada
El esquema de PostgreSQL MUST cambiar solo mediante migraciones de TypeORM incluidas en el PR de
la HU. Los registros de negocio MUST NOT borrarse físicamente cuando la regla indica cancelación
(se marca el estado correspondiente).

### X. Flujo de trabajo y trazabilidad
- Cada HU de Jira tiene su propia rama, nombrada con la clave de Jira:
  `feature/SCRUM-XX-descripcion-corta` (ej. `feature/SCRUM-12-registro`). La rama es por HU, no
  por persona.
- La clave SCRUM-XX MUST aparecer también en el título del PR y en los commits, para que Jira
  los vincule automáticamente (integración GitHub for Jira).
- Nadie sube cambios directamente a `main`: está protegida y todo entra por Pull Request.
- Cada PR MUST ser revisado y aprobado por un responsable técnico del equipo antes de
  integrarse, y solo si la CI pasa.
- Los commits MUST ser convencionales (feat, fix, chore, docs, test).
- Las herramientas de IA y Spec Kit son opcionales para implementar; lo obligatorio para todo el
  equipo es el flujo de Git anterior (desde terminal o GitHub Desktop).
- La documentación de arquitectura (C4 en Mermaid) y CLAUDE.md MUST actualizarse cuando una
  decisión los afecta.

## Stack y Restricciones Técnicas

- Backend: NestJS + TypeORM + PostgreSQL, API REST con Swagger, autenticación JWT.
- Android: Kotlin, Jetpack Compose, MVVM con Hilt (cliente principal, se entrega primero).
- Flutter: Riverpod y Widgetbook (iOS y multiplataforma, se entrega después).
- Prohibido: Firebase, Google Maps API y cualquier servicio que requiera pago, tarjeta o API key
  (Principio VIII).
- Idioma de la documentación y de los artefactos de Spec Kit: español.

## Flujo de Desarrollo y Puertas de Calidad

- Una HU solo se considera terminada cuando es demostrable de punta a punta y su PR fue
  aprobado con la CI en verde (Principios III, VII y X).
- Cada PR MUST incluir las migraciones de TypeORM, los tests y la actualización de Swagger que
  la HU requiera.
- La revisión del PR MUST verificar el cumplimiento de los Principios I a X.
- El alcance del Release 1 (26 de noviembre de 2026) se limita a lo definido en sus HU; lo demás
  se difiere a releases posteriores.

## Governance

La constitución prevalece sobre las preferencias individuales y sobre cualquier otra práctica del
equipo. Cualquier cambio se propone por Pull Request, se aprueba en equipo y actualiza la versión
semántica: MAJOR para eliminar o redefinir principios de forma incompatible, MINOR para añadir un
principio o ampliar materialmente su guía, PATCH para aclaraciones y correcciones de redacción.
Cada plan de implementación MUST pasar la verificación de estos principios o documentar la
excepción y su justificación. La revisión de cada PR comprueba el cumplimiento. La guía operativa
para el trabajo diario con IA se mantiene en CLAUDE.md.

**Version**: 1.0.0 | **Ratified**: 2026-10-26 | **Last Amended**: 2026-10-26
