# src/config

Configuración de la aplicación tipada y validada al arrancar: puerto, URL de la base de datos,
secreto y expiración del JWT, entorno y límites de subida.

- Lee solo de variables de entorno (`.env` local, nunca versionado).
- Falla al iniciar si falta una variable obligatoria o tiene formato inválido.
- No contiene lógica de negocio ni valores de producción hardcodeados.
