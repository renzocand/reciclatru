# Documentación — ReciclaTru

Documentación transversal del monorepo. Lo específico de cada stack vive en su propia
carpeta (`android/AGENTS.md`, `flutter/AGENTS.md`, `backend/AGENTS.md`).

## Qué va acá

- **Arquitectura C4 en Mermaid**: contexto, contenedores y componentes del sistema
  (app Android, app Flutter, API NestJS, PostgreSQL). Diagramas como código para que se
  revisen en el PR; se actualizan cuando una decisión cambia la arquitectura (constitución,
  Principio X).
- **ADRs** (Architecture Decision Records): una decisión por archivo
  (`adr/0001-titulo.md`), con contexto, alternativas y consecuencias.
- **API**: guías de uso, convenciones de errores y paginación, y enlace al Swagger que
  publica el backend en `/api/docs`.
- **Guías transversales**: flujo de Git y PRs, privacidad de datos, entornos de desarrollo
  y criterios de demo de una HU.

## Reglas

- Todo en **español** y en Markdown.
- Un documento por tema, con nombre en `kebab-case` y enlace desde este índice cuando se
  agregue.
- No duplicar la constitución ni los `AGENTS.md`: enlazarlos.
- No incluir secretos, credenciales, tokens ni datos personales reales.
- Toda decisión que afecte a más de un proyecto (Android, Flutter, backend) se documenta
  acá, no en el README de una feature.
