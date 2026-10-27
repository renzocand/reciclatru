# AGENTS.md — ReciclaTru (raíz)

## Qué es este proyecto

ReciclaTru conecta a vecinos de Trujillo (Perú) con recicladores. Un vecino publica un
pedido con materiales y pesos; un reciclador lo explora, lo acepta y lo recoge. Es
desarrollada por un equipo universitario de 7 personas y el Release 1 se entrega el
**26 de noviembre de 2026**.

Es un monorepo con un solo backend y dos clientes:

| Proyecto   | Tecnología                                                        | Rol                         |
| ---------- | ----------------------------------------------------------------- | --------------------------- |
| `backend/` | NestJS + PostgreSQL + TypeORM (CommonJS), API REST con Swagger     | Fuente de verdad            |
| `android/` | Kotlin + Jetpack Compose + Material 3, MVVM + Hilt                 | Cliente principal           |
| `flutter/` | Flutter + Material 3, Riverpod + go_router                         | Cliente iOS (se trabaja después) |

Ambos clientes consumen **la misma API**. Cada historia de usuario (HU) es un **corte
vertical**: pantalla + API + base de datos, demostrable de punta a punta.

## Regla superior

`.specify/memory/constitution.md` es la norma de mayor jerarquía del repositorio.
Este archivo y cualquier otro documento son guías operativas: si hay conflicto, gana la
constitución. Antes de implementar, leerla.

Principios que más afectan al código (resumen; la constitución manda):

1. **Backend como única fuente de verdad**: toda regla de negocio, permiso por rol y
   regla de privacidad se valida en la API. Un cliente puede replicar una validación
   para mejorar la UX, pero nunca sustituirla.
2. **Un solo backend, dos clientes**: no se crean endpoints exclusivos de un cliente sin
   justificación documentada en el plan de la HU.
3. **HU como corte vertical**: no hay HUs "solo de backend" ni "solo de UI"; cada HU se
   entrega completa, en su rama y con un único PR.
4. **Arquitectura por feature**: los dominios `auth`, `profile`, `orders`, `explore` y
   `tracking` se llaman igual en los tres proyectos, con capas `ui`/`presentation`,
   `domain` y `data`.
5. **Atomic Design en ambos clientes**: tokens, átomos, moléculas, organismos,
   plantillas; prefijo `Rt`, componentes stateless y los mismos nombres en Android y
   Flutter.
6. **Privacidad por diseño**: teléfono, dirección y coordenadas exactas solo se exponen
   según el rol y el estado del pedido. La ubicación del reciclador no se almacena.
   Contraseñas con bcrypt, JWT y secretos solo en variables de entorno.
7. **Pruebas con propósito**: toda regla de negocio del backend con test unitario, e2e
   para flujos principales; en clientes, tests de ViewModel/provider y de UI con
   dependencias falsas.
8. **Simplicidad**: solo lo que está en el release; prohibido Firebase, Google Maps API
   y cualquier servicio de pago o con API key.
9. **Base de datos versionada**: todo cambio de esquema va por migración de TypeORM en
   el PR de la HU; las cancelaciones son lógicas, no borrados físicos.
10. **Flujo y trazabilidad**: rama por HU, PR con la clave de Jira, CI en verde y
    aprobación de un responsable técnico.

## Estructura del monorepo

```text
.
├── AGENTS.md / CLAUDE.md        # instrucciones para agentes (raíz)
├── .specify/                    # Spec Kit: constitución, plantillas, specs (NO editar a mano)
├── .claude/                     # comandos de Claude Code (NO editar a mano)
├── docs/                        # arquitectura C4, ADRs, API y guías transversales
├── android/                     # app Android (Kotlin, Compose, MVVM + Hilt)
├── flutter/                     # app Flutter (Riverpod + go_router)
└── backend/                     # API NestJS + PostgreSQL + TypeORM
```

Cada uno de esos cuatro proyectos tiene su propio `AGENTS.md` con las reglas
específicas de su stack, y un `CLAUDE.md` que solo contiene `@AGENTS.md`.

## Flujo de trabajo

- **Rama por HU**: `feature/SCRUM-XX-descripcion-corta` (ej.
  `feature/SCRUM-12-registro`). La rama es de la HU, no de la persona.
- **Commits convencionales** (`feat`, `fix`, `chore`, `docs`, `test`) e incluir la clave
  de Jira en el mensaje, por ejemplo:
  `feat(orders): SCRUM-14 permitir publicar pedido`.
- **Un PR por HU**, con la clave `SCRUM-XX` en el título, las migraciones, los tests y la
  actualización de Swagger que correspondan. Nadie sube cambios directos a `main`.
- Antes de pedir revisión: compilar, pasar tests y lint del proyecto tocado (ver el
  `AGENTS.md` de cada carpeta). Un PR con CI en rojo no se integra.
- La documentación y los artefactos se escriben en **español**.

## Reglas para agentes de IA

- **Antes de modificar archivos dentro de `android/`, `flutter/` o `backend/`, lee el
  `AGENTS.md` de esa carpeta y sigue sus reglas.**
- No modificar `.specify/` ni `.claude/` salvo que la tarea sea explícitamente sobre
  esos artefactos.
- No agregar dependencias, plugins ni servicios que impliquen costo, tarjeta o API key.
- No inventar reglas de negocio: si un criterio de aceptación no está claro, preguntar o
  revisar la spec de la HU antes de implementar.
- Respetar la estructura y los nombres ya existentes; antes de crear un componente,
  verificar si ya existe uno equivalente.
- Nunca incluir secretos, credenciales ni archivos `.env` en el repositorio.
- No borrar trabajo existente sin entenderlo y sin confirmación.
- Si una decisión afecta la arquitectura, actualizar la documentación de `docs/`
  (C4 en Mermaid) y el `CLAUDE.md`/`AGENTS.md` que corresponda.

## Documentación

- `docs/`: diagramas C4, ADRs y documentación transversal de la API.
- `backend/`: Swagger publicado por la API en `/api/docs`.
- `android/AGENTS.md`, `flutter/AGENTS.md`, `backend/AGENTS.md`: reglas de cada stack.
- `*/core/designsystem/README.md` (Android y Flutter): guía de Atomic Design.
