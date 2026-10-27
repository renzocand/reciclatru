# tokens

Valores únicos del sistema de diseño: colores, tipografía, espaciado y radios. No son widgets:
se exponen desde `RtTheme` (`ThemeData` de Material 3, con `ThemeExtension`) y los consume el
resto del sistema.

- `rt_colors.dart` — colores de marca y colores semánticos del estado del pedido.
- `rt_typography.dart` — estilos de texto.
- `rt_spacing.dart` — escala de espaciado (4, 8, 12, 16, 24, 32 lógicos).
- `rt_radii.dart` — radios de tarjetas, campos y botones.

Ningún componente usa valores literales: si un valor se repite, se convierte en token.
