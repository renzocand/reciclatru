# tokens

Valores únicos del sistema de diseño: colores, tipografía, espaciado y radios. No son
componentes: se exponen desde `RtTheme` (Material 3) y los consume el resto del sistema.

- `RtColors.kt` — colores de marca y colores semánticos del estado del pedido.
- `RtTypography.kt` — estilos de texto.
- `RtSpacing.kt` — escala de espaciado (4, 8, 12, 16, 24, 32 dp).
- `RtShapes.kt` — radios de tarjetas, campos y botones.

Ningún componente usa valores literales: si un valor se repite, se convierte en token.
