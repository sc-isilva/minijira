# Especificación de prototipo: Mini Jira (V1)

| Campo | Valor |
|---|---|
| Fuentes | `docs/specs.md` v1.1 y `architecture/er_diagram.md` |
| Consumidores | Claude Code (implementación en React) y Google Stitch (generación de pantallas) |
| Fecha | 23-09-2026 |
| Autora | Isis Silva |

## Cómo usar este documento

- **Claude Code:** los tokens de la sección A.3 se implementan tal cual como variables CSS. Los nombres de componentes de la sección C son los nombres de los componentes de React.
- **Google Stitch:** cada funcionalidad de la sección C trae un bloque **"Prompt para Stitch"** listo para pegar, que referencia los tokens de A.
- **Trazabilidad:** cada funcionalidad cita los RF de specs.md que la originan. Lo que specs.md deja abierto aparece como `[PENDIENTE RF-xx]` y **no se diseña un comportamiento para ello**.
- **Contenido de ejemplo:** todo el texto de las pantallas es placeholder (ver A.9). No se usan nombres ni datos reales.

---

# A. Sistema de diseño global

## A.1 Principios

| Principio | Qué implica | Fuente |
|---|---|---|
| Se usa sin manual | Una acción principal por pantalla, etiquetas en lenguaje llano y ningún flujo con más de un nivel de modal. | RNF-01, T-L9 |
| Limpio, "tipo Apple" | Superficies blancas, mucho espacio en blanco, sombras suaves, sin bordes pesados ni decoración. | RNF-02, SUP-06 |
| Claro y oscuro por igual | Cada token de color tiene valor claro y oscuro, validados por separado. El modo oscuro no se genera invirtiendo colores. | RF-52 |
| Pocas columnas, bien espaciadas | El tablero debe verse ordenado aunque el número de estados sea configurable. | T-L30, RF-30 |

## A.2 Tecnología de referencia

- React (SUP-03). No hay librería de componentes elegida [PENDIENTE sección 3 de specs.md]. Los tokens son independientes de la librería.
- El tema se controla con el atributo `data-theme="light|dark"` en `<html>`. Si el usuario no eligió, se sigue `prefers-color-scheme`.

## A.3 Design Tokens

Los nombres siguen el patrón `categoria.rol.variante`. En CSS se escriben como `--color-surface-default`, etc.

### A.3.1 Color (semánticos)

Contrastes medidos con la fórmula WCAG 2.1 sobre la superficie indicada. Todo el texto cumple 4.5:1 o más.

| Token | Claro | Oscuro | Uso | Contraste (claro / oscuro) |
|---|---|---|---|---|
| `color.bg.canvas` | `#F5F5F7` | `#000000` | Fondo de la página | n/a |
| `color.surface.default` | `#FFFFFF` | `#1C1C1E` | Tarjetas, paneles, modales | n/a |
| `color.surface.sunken` | `#EDEDF0` | `#151517` | Fondo de columnas del tablero, campos de solo lectura | n/a |
| `color.text.primary` | `#1D1D1F` | `#F5F5F7` | Texto principal | 16.8 / 15.6 sobre superficie |
| `color.text.secondary` | `#48484A` | `#D1D1D6` | Metadatos, subtítulos | 9.1 / 11.2 |
| `color.text.muted` | `#636366` | `#AEAEB2` | Ayudas, placeholders | 6.0 / 7.7 (5.1 / 8.3 en `sunken`) |
| `color.text.onAccent` | `#FFFFFF` | `#000000` | Texto sobre botón primario | 5.6 / 8.0 |
| `color.accent.default` | `#0066CC` | `#4DA3FF` | Botón primario, enlaces, selección | 5.6 / 6.5 |
| `color.accent.hover` | `#0052A3` | `#7AB8FF` | Hover y presionado del primario | 7.7 / 10.1 con `onAccent` |
| `color.focus.ring` | `#0066CC` | `#4DA3FF` | Anillo de foco de teclado | 5.6 / 6.5 |
| `color.border.subtle` | `#C7C7CC` | `#636366` | Divisores decorativos (no identifican controles) | 1.7 / 2.8 |
| `color.border.control` | `#8A8A8E` | `#8E8E93` | Borde de inputs, selects, checkbox | 3.4 / 5.2 (cumple 3:1) |
| `color.feedback.danger` | `#C4262E` | `#FF6B6B` | Errores, acción de eliminar | 5.7 / 6.1 |
| `color.feedback.success` | `#1E7B34` | `#4CD07D` | Confirmaciones | 5.3 / 8.6 |
| `color.feedback.warning` | `#8A5300` | `#FFB340` | Avisos | 6.3 / 9.5 |
| `color.overlay.scrim` | `rgba(0,0,0,0.40)` | `rgba(0,0,0,0.60)` | Fondo detrás de modales | n/a |

> Los colores de feedback **nunca van solos**: siempre se acompañan de un ícono y un texto (WCAG 1.4.1).
> **Prioridad:** sus valores están [PENDIENTE RF-13], así que no se les asigna color. El badge de prioridad usa estilo neutro con texto (ver A.8).

### A.3.2 Color de gráficas (dashboard, RF-48)

Paleta categorical para distinguir **proyectos**, asignada en orden fijo (el proyecto 1 siempre usa `chart.series.1`) y nunca cíclica. Se validó con el script de la skill de visualización contra las superficies de este sistema.

| Token | Claro | Oscuro |
|---|---|---|
| `chart.series.1` | `#2A78D6` | `#3987E5` |
| `chart.series.2` | `#EB6834` | `#D95926` |
| `chart.series.3` | `#1BAF7A` | `#199E70` |
| `chart.series.4` | `#EDA100` | `#C98500` |
| `chart.series.5` | `#E87BA4` | `#D55181` |
| `chart.series.6` | `#008300` | `#008300` |
| `chart.series.7` | `#4A3AA7` | `#9085E9` |
| `chart.series.8` | `#E34948` | `#E66767` |
| `chart.grid` | `#E5E5EA` | `#2C2C2E` |
| `chart.axis` | `#C7C7CC` | `#3A3A3C` |

**Resultado de la validación:** pasa separación para daltonismo (ΔE ≥ 8.4) y visión normal (ΔE ≥ 19.3) en ambos modos. En modo claro, las series 3, 4 y 5 quedan por debajo de 3:1 contra el blanco. Por eso el dashboard **siempre** muestra una leyenda con texto y una vista de tabla (ver C.10).
Con más de 8 proyectos, los adicionales se agrupan en "Otros"; nunca se genera un color nuevo.

### A.3.3 Tipografía

Familia: `-apple-system, BlinkMacSystemFont, "Segoe UI", system-ui, sans-serif` (fuente del sistema, coherente con "tipo Apple"). Las columnas numéricas usan `font-variant-numeric: tabular-nums`.

| Token | Tamaño / interlineado | Peso | Uso |
|---|---|---|---|
| `font.display` | 28 / 34 px | 600 | Título de página |
| `font.title` | 20 / 26 px | 600 | Título de modal, nombre del proyecto |
| `font.heading` | 17 / 22 px | 600 | Título de columna, título de tarjeta |
| `font.body` | 15 / 22 px | 400 | Texto general, inputs |
| `font.body.strong` | 15 / 22 px | 600 | Énfasis |
| `font.caption` | 13 / 18 px | 400 | Metadatos, ayudas, etiquetas de campo |
| `font.micro` | 12 / 16 px | 500 | Badges |

Tamaño mínimo de texto: 12 px, y solo en badges. Todo se define en `rem` (16 px base) para respetar el zoom del navegador.

### A.3.4 Espaciado (escala de 4 px)

| Token | Valor | Token | Valor |
|---|---|---|---|
| `space.1` | 4 px | `space.6` | 24 px |
| `space.2` | 8 px | `space.8` | 32 px |
| `space.3` | 12 px | `space.10` | 40 px |
| `space.4` | 16 px | `space.12` | 48 px |
| `space.5` | 20 px | `space.16` | 64 px |

### A.3.5 Radios, sombras, movimiento y capas

| Token | Claro | Oscuro | Uso |
|---|---|---|---|
| `radius.sm` | 6 px | = | Badges, checkbox |
| `radius.md` | 10 px | = | Inputs, botones |
| `radius.lg` | 14 px | = | Tarjetas de ticket, columnas |
| `radius.xl` | 20 px | = | Modales, paneles laterales |
| `radius.full` | 9999 px | = | Avatares, chips |
| `shadow.sm` | `0 1px 2px rgba(0,0,0,0.06)` | `0 0 0 1px rgba(255,255,255,0.08)` | Tarjeta en reposo |
| `shadow.md` | `0 4px 12px rgba(0,0,0,0.08)` | `0 0 0 1px rgba(255,255,255,0.10), 0 4px 12px rgba(0,0,0,0.5)` | Tarjeta en hover o arrastre, menús |
| `shadow.lg` | `0 12px 32px rgba(0,0,0,0.12)` | `0 0 0 1px rgba(255,255,255,0.12), 0 12px 32px rgba(0,0,0,0.6)` | Modales |
| `motion.fast` | 120 ms `ease-out` | = | Hover, foco |
| `motion.base` | 200 ms `ease-out` | = | Abrir menús y paneles |
| `motion.slow` | 280 ms `cubic-bezier(0.2,0,0,1)` | = | Modales, mover tarjetas |
| `z.dropdown` / `z.modal` / `z.toast` | 100 / 200 / 300 | = | Capas |

> En modo oscuro las sombras casi no se ven. Por eso se sustituyen por un anillo sutil de 1 px, que es lo que distingue una superficie elevada.
> Con `prefers-reduced-motion: reduce`, todas las duraciones pasan a 0 ms, salvo los cambios de opacidad.

### A.3.6 Breakpoints y rejilla

| Token | Rango | Rejilla | Margen lateral |
|---|---|---|---|
| `bp.sm` | < 640 px | 4 columnas | 16 px |
| `bp.md` | 640 a 1023 px | 8 columnas | 24 px |
| `bp.lg` | 1024 a 1439 px | 12 columnas | 32 px |
| `bp.xl` | ≥ 1440 px | 12 columnas, contenido máx. 1280 px | Automático |

### A.3.7 Tokens en formato intercambiable

```json
{
  "color": {
    "bg":      { "canvas":  { "light": "#F5F5F7", "dark": "#000000" } },
    "surface": { "default": { "light": "#FFFFFF", "dark": "#1C1C1E" },
                 "sunken":  { "light": "#EDEDF0", "dark": "#151517" } },
    "text":    { "primary":   { "light": "#1D1D1F", "dark": "#F5F5F7" },
                 "secondary": { "light": "#48484A", "dark": "#D1D1D6" },
                 "muted":     { "light": "#636366", "dark": "#AEAEB2" },
                 "onAccent":  { "light": "#FFFFFF", "dark": "#000000" } },
    "accent":  { "default": { "light": "#0066CC", "dark": "#4DA3FF" },
                 "hover":   { "light": "#0052A3", "dark": "#7AB8FF" } },
    "focus":   { "ring":    { "light": "#0066CC", "dark": "#4DA3FF" } },
    "border":  { "subtle":  { "light": "#C7C7CC", "dark": "#636366" },
                 "control": { "light": "#8A8A8E", "dark": "#8E8E93" } },
    "feedback":{ "danger":  { "light": "#C4262E", "dark": "#FF6B6B" },
                 "success": { "light": "#1E7B34", "dark": "#4CD07D" },
                 "warning": { "light": "#8A5300", "dark": "#FFB340" } }
  },
  "radius":  { "sm": "6px", "md": "10px", "lg": "14px", "xl": "20px", "full": "9999px" },
  "space":   { "1": "4px", "2": "8px", "3": "12px", "4": "16px", "5": "20px", "6": "24px", "8": "32px", "10": "40px", "12": "48px", "16": "64px" },
  "font":    { "family": "-apple-system, BlinkMacSystemFont, 'Segoe UI', system-ui, sans-serif" }
}
```

## A.4 Componentes base

| Componente | Variantes | Especificación clave |
|---|---|---|
| `Button` | `primary`, `secondary`, `ghost`, `danger` | Alto 40 px (44 px en táctil), `radius.md`, `font.body.strong`. Solo un `primary` visible por vista. |
| `IconButton` | `ghost` | Área táctil de 44×44 px aunque el ícono mida 20 px. `aria-label` obligatorio. |
| `TextField` / `TextArea` | `default`, `error`, `disabled` | Etiqueta visible siempre encima (nunca solo placeholder), borde `color.border.control`, mensaje de error debajo con ícono. |
| `Select` / `Combobox` | Simple, con búsqueda | Navegable con flechas, `Enter` y `Esc`. |
| `DatePicker` | Simple | También permite escribir la fecha a mano. |
| `Badge` | `neutral`, `status`, `archived` | `font.micro`, `radius.sm`. El texto siempre visible. |
| `Chip` | Etiqueta, filtro activo | Con botón de quitar accesible ("Quitar filtro [valor]"). |
| `Avatar` | 24, 32 px | Iniciales del placeholder. Siempre acompañado del nombre visible o en `aria-label`. |
| `Card` | Ticket, proyecto | `surface.default`, `shadow.sm`, `radius.lg`. |
| `Modal` / `Dialog` | Formulario, confirmación | Retiene el foco, se cierra con `Esc` y devuelve el foco al disparador. |
| `SidePanel` | Detalle de ticket | 480 px en `lg` o más, pantalla completa en `sm`. |
| `Toast` | `success`, `danger`, `info` | `role="status"` (o `alert` si es error), 5 s, se pausa al pasar el mouse o con foco. |
| `EmptyState` | Genérico | Ilustración opcional, título, texto y una acción. |
| `Skeleton` | Tarjeta, fila, gráfica | Solo si la carga supera 300 ms. |
| `ThemeToggle` | Claro, Oscuro, Sistema | Ver C.11. |

## A.5 Estados de interacción (todos los controles)

| Estado | Tratamiento |
|---|---|
| Hover | Fondo con `accent` al 8% o `accent.hover` en el primario; `motion.fast`. |
| Foco de teclado | Anillo de 2 px `color.focus.ring` con 2 px de separación; **nunca se quita**. Solo con `:focus-visible`. |
| Presionado | `accent.hover`, escala 0.98. |
| Deshabilitado | Opacidad 0.4, sin eventos, más un texto o `title` que explique por qué. |
| Cargando | Spinner dentro del botón y el botón deshabilitado; el texto cambia a "Guardando…". |
| Error | Borde `feedback.danger`, ícono y mensaje debajo, enlazado con `aria-describedby`. |

## A.6 Íconos

Trazo de 1.5 px, 20 px por defecto, un solo estilo (línea). Los íconos decorativos llevan `aria-hidden="true"`; los que funcionan solos como botón llevan `aria-label`.

## A.7 Plantilla de página (App Shell)

```
┌──────────────────────────────────────────────────────────────┐
│ Barra superior: [Logo placeholder]  [Selector de proyecto ▾] │
│                         [ThemeToggle] [Avatar ▾ Cerrar sesión]│
├───────────┬──────────────────────────────────────────────────┤
│ Nav lat.  │  Título de página            [Acción primaria]   │
│ • Tablero │  ──────────────────────────────────────────────  │
│ • Proyect.│  Contenido                                        │
│ • Dashbrd │                                                   │
│ • Estados*│                                                   │
└───────────┴──────────────────────────────────────────────────┘
* Visible solo para quien tenga permiso de configurar estados [PENDIENTE RF-35]
```

- En `sm` la navegación lateral pasa a un menú desplegable desde la barra superior.
- La navegación tiene `nav` con `aria-label="Principal"`, y la página activa usa `aria-current="page"`.
- Hay un enlace "Saltar al contenido" como primer elemento enfocable.

## A.8 Badges de dominio

| Badge | Estilo | Fuente |
|---|---|---|
| Estado del ticket | `neutral` con el nombre del estado | RF-29 |
| Prioridad | `neutral` con el texto de la prioridad; sin color hasta que se definan sus valores | RF-11, [PENDIENTE RF-13] |
| Etiqueta | `Chip` neutro | RF-11 |
| Archivado | `archived`: fondo `surface.sunken`, ícono de archivo y el texto "Archivado" | RF-20 |
| Estado final | Marca "Final" junto al nombre del estado en la configuración | RF-36 |

## A.9 Contenido placeholder

| Tipo | Valores de ejemplo |
|---|---|
| Usuarios | "Usuario Uno", "Usuario Dos", "Usuario Tres"; `usuario.uno` / `usuario1@ejemplo.test` |
| Admin | "Admin Ejemplo" |
| Proyectos | "Proyecto Alfa", "Proyecto Beta", "Proyecto Gamma" |
| Tickets | "Ticket de ejemplo 1", "Ticket de ejemplo 2"… |
| Descripción | "Descripción de ejemplo del ticket. Texto de relleno para mostrar un párrafo largo." |
| Etiquetas | "etiqueta-a", "etiqueta-b", "etiqueta-c" |
| Prioridad | "Prioridad [valor]" (valores [PENDIENTE RF-13]) |
| Estados | "Estado 1", "Estado 2", "Estado 3" (los estados por defecto están [PENDIENTE RF-34]) |
| Fechas | "01/01/2026" |
| Comentarios | "Comentario de ejemplo." |

---

# B. Estándares: WCAG 2.1 AA y usabilidad

## B.1 Checklist WCAG 2.1 AA aplicada a Mini Jira

| Criterio | Requisito | Aplicación concreta |
|---|---|---|
| 1.1.1 Contenido no textual | Alternativa textual | Íconos con `aria-label` o `aria-hidden`; gráficas con resumen textual y tabla (C.10). |
| 1.3.1 Información y relaciones | Estructura semántica | Encabezados jerárquicos; formularios con `<label>`; tablero como regiones con `aria-labelledby` = nombre de la columna. |
| 1.3.2 Secuencia significativa | Orden lógico | El orden del DOM sigue el orden visual: columnas de izquierda a derecha y tarjetas de arriba abajo. |
| 1.3.4 Orientación | Sin bloquear la orientación | El tablero funciona en vertical, con columnas apiladas o desplazamiento horizontal. |
| 1.3.5 Propósito de los campos | `autocomplete` | Login: `username` y `current-password`. |
| 1.4.1 Uso del color | El color no es el único medio | Estado, prioridad, archivado, errores y series de gráficas siempre llevan texto o ícono. |
| 1.4.3 Contraste mínimo | 4.5:1 texto, 3:1 texto grande | Medido en A.3.1: el mínimo es 5.1:1. |
| 1.4.4 Cambio de tamaño | Zoom al 200% | Unidades en `rem` y layout fluido. |
| 1.4.10 Reflow | Sin scroll horizontal a 320 px | Salvo el tablero, que es contenido bidimensional (excepción permitida), con columnas desplazables. |
| 1.4.11 Contraste no textual | 3:1 en controles y foco | `border.control` 3.4 / 5.2; foco 5.6 / 6.5. |
| 1.4.12 Espaciado de texto | Tolerar espaciado aumentado | Sin alturas fijas en contenedores de texto. |
| 1.4.13 Contenido al pasar el mouse o con foco | Descartable y persistente | Los tooltips se cierran con `Esc` y no desaparecen al mover el puntero sobre ellos. |
| 2.1.1 Teclado | Todo operable con teclado | **Mover tickets entre estados sin arrastrar** (ver C.3). |
| 2.1.2 Sin trampas de teclado | | Los modales retienen el foco pero se cierran con `Esc`. |
| 2.4.1 Evitar bloques | Saltar navegación | Enlace "Saltar al contenido". |
| 2.4.3 Orden del foco | Lógico | Al abrir un modal el foco va al primer campo; al cerrarlo vuelve al disparador. |
| 2.4.6 Encabezados y etiquetas | Descriptivos | "Nuevo ticket", no "Nuevo". |
| 2.4.7 Foco visible | Siempre | A.5. |
| 2.5.3 Etiqueta en el nombre | El nombre accesible contiene el texto visible | El botón "Eliminar" tiene nombre accesible "Eliminar ticket [título]". |
| 3.2.2 Al recibir entradas | Sin cambios de contexto inesperados | Los filtros se aplican al elegir, sin navegar a otra página. |
| 3.3.1 Identificación de errores | El error se describe en texto | Mensaje bajo el campo y resumen al inicio del formulario. |
| 3.3.2 Etiquetas o instrucciones | Etiquetas visibles | Obligatoriedad marcada cuando se defina [PENDIENTE RF-15]. |
| 3.3.4 Prevención de errores | Confirmar acciones importantes | Confirmación antes de archivar (C.7). |
| 4.1.2 Nombre, función, valor | ARIA correcto | Toggle de tema como `radiogroup`; menús con `aria-expanded`. |
| 4.1.3 Mensajes de estado | Anunciar sin mover el foco | Toasts con `role="status"`; al mover un ticket se anuncia "Ticket movido a [estado]". |

## B.2 Reglas de usabilidad

1. **Una acción primaria por vista.** Tablero: "Nuevo ticket". Proyectos: "Nuevo proyecto".
2. **Siempre se muestra el estado del sistema:** carga, guardado, error y archivado.
3. **Lenguaje del usuario:** "Proyecto", "Estado", "Responsable", sin jerga técnica.
4. **Prevenir antes que corregir:** los controles que el rol no permite usar se ocultan. Si el usuario llega a una acción no permitida (por ejemplo, desde un enlace), se le explica por qué.
5. **Reconocer antes que recordar:** los filtros activos se ven como chips y los selectores muestran opciones, no IDs.
6. **Consistencia:** un solo patrón para crear y editar, que es el mismo formulario (C.4).
7. **Mensajes de error útiles:** dicen qué pasó y qué hacer ("No se pudo guardar. Revisa tu conexión y vuelve a intentarlo.").
8. **Mínimo de clics para la tarea más frecuente:** crear un ticket desde el tablero sin cambiar de pantalla.

## B.3 Estados universales de una vista

Toda vista de la sección C debe diseñar estos estados; cada subsección solo agrega los propios.

| Estado | Patrón |
|---|---|
| Cargando | `Skeleton` con la forma del contenido final. |
| Vacío | `EmptyState` con una acción siguiente. |
| Error de carga | Mensaje, botón "Reintentar" y contenido anterior si lo hay. |
| Sin permiso | Texto: "No tienes permiso para esta acción." Sin exponer datos. |
| Sesión expirada | Redirigir al login y conservar la ruta destino. |

---

# C. Funcionalidades del MVP

Mapa de funcionalidades a partir de specs.md:

| # | Funcionalidad | RF de origen |
|---|---|---|
| C.1 | Inicio de sesión | RF-01, RF-02, RF-03 |
| C.2 | Proyectos | RF-05, RF-06, RF-07, RF-08 |
| C.3 | Tablero de tareas | RF-29, RF-31, RF-32, RF-33 |
| C.4 | Crear y editar ticket | RF-10 a RF-18, RF-53 |
| C.5 | Asignación de personas | RF-27, RF-28 |
| C.6 | Filtros | RF-37, RF-38 |
| C.7 | Eliminar (archivar) ticket | RF-20 a RF-26 |
| C.8 | Comentarios | RF-40 a RF-45 |
| C.9 | Configuración de estados | RF-30, RF-35, RF-36 |
| C.10 | Dashboard de métricas | RF-48 a RF-51 |
| C.11 | Modo oscuro | RF-52 |

**Sin pantalla en el MVP:**

- Notificaciones por email (RF-46): se envían desde el servidor y no tienen interfaz en la aplicación.
- Gestión de usuarios (RF-04): está [PENDIENTE] y no se diseña hasta que se defina.

---

## C.1 Inicio de sesión

**Propósito:** que una persona con cuenta entre con su usuario y contraseña (RF-01). El sistema la reconoce con su rol (RF-03).

**Componentes:** `LoginCard`, `TextField` (Usuario), `TextField` tipo contraseña con botón "Mostrar u ocultar", `Button primary` ("Iniciar sesión"), `InlineAlert` de error.

**Layout**

```
┌────────────────────────── bg.canvas ─────────────────────────┐
│                                                              │
│                ┌──────── surface, radius.xl ───────┐         │
│                │  [Logo placeholder]                │         │
│                │  Iniciar sesión          font.title│         │
│                │  Usuario    [________________]     │         │
│                │  Contraseña [____________] [👁]    │         │
│                │  [ Iniciar sesión ]  (primary, 100%)│        │
│                └────────────────────────────────────┘         │
└──────────────────────────────────────────────────────────────┘
```

Tarjeta centrada de 400 px de ancho (100% con 16 px de margen en `sm`), `shadow.lg` y separación `space.4` entre campos.

**Estados**

| Estado | Comportamiento |
|---|---|
| Por defecto | Foco inicial en "Usuario". |
| Enviando | Botón en carga ("Iniciando…") y campos deshabilitados. |
| Credenciales incorrectas | `InlineAlert danger`: "Usuario o contraseña incorrectos." No se indica cuál de los dos falló. El foco va a la alerta. (EC-01) |
| Campo vacío | Error bajo el campo: "Escribe tu usuario." / "Escribe tu contraseña." |
| Bloqueo por intentos | [PENDIENTE RNF-04] |
| "Olvidé mi contraseña" | No se diseña: la gestión de usuarios está [PENDIENTE RF-04]. |

**Prompt para Stitch**
> Pantalla de inicio de sesión minimalista estilo Apple. Fondo #F5F5F7, tarjeta blanca centrada de 400 px con radio 20 px y sombra suave. Logo placeholder, título "Iniciar sesión", campos "Usuario" y "Contraseña" con etiqueta visible encima y botón azul #0066CC de ancho completo "Iniciar sesión". Tipografía del sistema. Incluir la variante oscura: fondo #000000, tarjeta #1C1C1E y botón #4DA3FF con texto negro.

---

## C.2 Proyectos

**Propósito:** crear, editar y listar los proyectos que agrupan tickets (RF-05, RF-06).

**Componentes:** `ProjectList` (lista de `Card`), `ProjectCard` (nombre y conteo de tickets), `ProjectFormModal` (campo "Nombre"), `ProjectSwitcher` (selector en la barra superior), `EmptyState`.

**Layout**

```
Proyectos                                   [ Nuevo proyecto ]
───────────────────────────────────────────────────────────────
┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
│ Proyecto Alfa   │ │ Proyecto Beta   │ │ Proyecto Gamma  │
│ 12 tickets   ⋯  │ │ 5 tickets    ⋯  │ │ 0 tickets    ⋯  │
└─────────────────┘ └─────────────────┘ └─────────────────┘
```

Rejilla de 3 columnas en `lg`, 2 en `md` y 1 en `sm`. Al hacer clic en una tarjeta se abre el tablero de ese proyecto. El menú `⋯` contiene "Editar".

**Campos del formulario:** solo "Nombre". Es el único atributo de proyecto que justifica el modelo (er_diagram.md). Otros atributos están [PENDIENTE].

**Estados**

| Estado | Comportamiento |
|---|---|
| Vacío | "Aún no hay proyectos." + botón "Nuevo proyecto". |
| Creando o editando | Modal con "Nombre", "Cancelar" y "Guardar". |
| Nombre vacío | Error: "Escribe un nombre para el proyecto." |
| Guardado | Toast "Proyecto guardado." y la tarjeta aparece o se actualiza. |
| Rol Admin | Si puede crear o editar proyectos está [PENDIENTE RF-07]. Mientras tanto se muestran las mismas acciones que al rol Usuario. |
| Visibilidad | Qué proyectos ve cada usuario está [PENDIENTE RF-08]. El prototipo muestra la lista completa. |
| Archivar proyecto | No se diseña: no está en specs.md. |

**Prompt para Stitch**
> Vista "Proyectos" de una app de gestión de tareas, estilo Apple limpio. Barra superior con logo placeholder, selector de proyecto, selector de tema y avatar. Navegación lateral: Tablero, Proyectos (activo), Dashboard. Título "Proyectos" y botón azul "Nuevo proyecto" a la derecha. Rejilla de 3 tarjetas blancas (radio 14 px, sombra sutil) con "Proyecto Alfa · 12 tickets", "Proyecto Beta · 5 tickets" y "Proyecto Gamma · 0 tickets", cada una con menú de tres puntos. Incluir estado vacío y variante oscura.

---

## C.3 Tablero de tareas

**Propósito:** ver los tickets de un proyecto en columnas por estado y moverlos entre estados (RF-29, RF-31 a RF-33).

**Componentes:** `BoardHeader` (proyecto actual, `FilterBar` de C.6 y "Nuevo ticket"), `BoardColumn` (título del estado, contador y lista), `TicketCard`, `MoveToMenu` (alternativa de teclado al arrastre), `LiveRegion` para anuncios.

**Layout**

```
Proyecto Alfa                                       [ Nuevo ticket ]
[Filtros: Fecha ▾ Prioridad ▾ Responsable ▾ Etiquetas ▾]  [chips…]
────────────────────────────────────────────────────────────────────
┌ Estado 1 · 3 ────┐ ┌ Estado 2 · 2 ────┐ ┌ Estado 3 · 1 ────┐
│┌────────────────┐│ │┌────────────────┐│ │┌────────────────┐│
││Ticket de ej. 1 ││ ││Ticket de ej. 4 ││ ││Ticket de ej. 6 ││
││[Prioridad] 01/01││ ││[Prioridad]     ││ ││                ││
││etiqueta-a  (UU)││ ││           (UD) ││ ││           (UT) ││
│└────────────────┘│ │└────────────────┘│ │└────────────────┘│
└──────────────────┘ └──────────────────┘ └──────────────────┘
```

- Columnas de 300 px de ancho fijo con fondo `surface.sunken`, `radius.lg` y separación `space.4`. Si no caben, el tablero se desplaza horizontalmente; el número de columnas depende de los estados configurados (RF-30).
- La **TicketCard** muestra título (`font.heading`, máximo 2 líneas), badge de prioridad, fecha, hasta 2 etiquetas ("+N" si hay más) y el avatar del responsable con su nombre en `aria-label`.
- En `sm` las columnas se apilan verticalmente y se pueden colapsar.

**Mover tickets (RF-31, RF-32)**

- Con mouse o táctil: arrastrar la tarjeta a otra columna. Se admite pasar a cualquier estado.
- Con teclado (obligatorio, WCAG 2.1.1): enfocar la tarjeta y abrir el menú "Mover a…" con la lista de estados. Al elegir, el `LiveRegion` anuncia "Ticket de ejemplo 1 movido a Estado 2".
- Solo se puede mover un ticket propio, o cualquiera si el rol es Admin (RF-18, RF-32). Qué cuenta como "propio" está [PENDIENTE RF-19]. Las tarjetas que el usuario no puede mover no se pueden arrastrar y no muestran "Mover a…".

**Estados**

| Estado | Comportamiento |
|---|---|
| Cargando | 3 columnas `Skeleton` con 2 tarjetas cada una. |
| Proyecto sin tickets | Columnas vacías y, en la primera, "Aún no hay tickets." + "Nuevo ticket". |
| Columna vacía | Texto `muted` "Sin tickets" y zona de destino visible durante el arrastre. |
| Arrastrando | Tarjeta con `shadow.md` y rotación de 2°; la columna destino se resalta con borde `accent`. |
| Error al mover | La tarjeta vuelve a su columna y aparece el toast "No se pudo mover el ticket. Inténtalo de nuevo." |
| Sin permiso para mover | Tarjeta no arrastrable. Si se intenta: "Solo puedes mover tus propios tickets." (EC-02) |
| Sin filtros coincidentes | "Ningún ticket coincide con los filtros." + "Limpiar filtros". |
| Sin estados configurados | [PENDIENTE RF-34] |
| Tickets archivados | No aparecen en el tablero. Su consulta está [PENDIENTE RF-25]. |

**Prompt para Stitch**
> Tablero kanban estilo Apple limpio. Encabezado "Proyecto Alfa" con botón azul "Nuevo ticket" y una fila de filtros desplegables (Fecha, Prioridad, Responsable, Etiquetas). Tres columnas grises claras (#EDEDF0, radio 14 px) tituladas "Estado 1 · 3", "Estado 2 · 2" y "Estado 3 · 1". Tarjetas blancas con sombra sutil que muestran título, badge neutro "Prioridad [valor]", fecha 01/01/2026, chip "etiqueta-a" y avatar con iniciales. Incluir una tarjeta en arrastre elevada y la variante oscura (columnas #151517, tarjetas #1C1C1E).

---

## C.4 Crear y editar ticket

**Propósito:** registrar una tarea en un formulario sencillo (RF-10, RF-53) y editarla (RF-17, RF-18) con todos sus campos (RF-11, RF-12).

**Componentes:** `TicketFormModal` (crear), `TicketDetailPanel` (ver y editar en `SidePanel`), `TextField` (Título), `TextArea` autoajustable (Descripción), `Select` (Prioridad, Proyecto, Estado), `DatePicker` (Fecha), `AssigneePicker` (C.5), `TagInput` (Etiquetas).

**Layout del formulario (crear, modal de 560 px)**

```
Nuevo ticket                                              [✕]
Título *        [_____________________________________]
Descripción     [                                     ]
                [          (área de texto amplia)     ]
Proyecto  [Proyecto Alfa ▾]      Prioridad [Prioridad ▾]
Fecha     [01/01/2026 📅]        Responsable [Usuario Uno ▾]
Etiquetas [etiqueta-a ✕] [etiqueta-b ✕] [+ Agregar]
                                    [Cancelar] [ Crear ticket ]
```

\* La marca de obligatorio se aplica cuando se defina qué campos lo son [PENDIENTE RF-15]. En el prototipo solo "Título" se muestra con asterisco como ejemplo visual.

**Layout del detalle (panel lateral de 480 px)**

```
[Badge estado] [Badge prioridad]                 [⋯] [✕]
Ticket de ejemplo 1                   (título editable)
Descripción de ejemplo…               (editable)
──────────────────────────────────────────────
Proyecto     Proyecto Alfa
Responsable  (UU) Usuario Uno
Fecha        01/01/2026
Etiquetas    etiqueta-a  etiqueta-b
──────────────────────────────────────────────
Comentarios (C.8)
```

El menú `⋯` contiene "Eliminar" (C.7). Edición en línea: cada campo cambia a modo edición al hacer clic, y el cambio se confirma con `Enter` o al perder el foco y se cancela con `Esc`.

**Campos y valores**

| Campo | Control | Nota |
|---|---|---|
| Título | `TextField` | RF-11 |
| Descripción | `TextArea` | Texto largo (RF-12) |
| Prioridad | `Select` | Valores [PENDIENTE RF-13]. Opciones placeholder: "Prioridad [valor]". |
| Fecha | `DatePicker` | Etiqueta genérica "Fecha": su significado está [PENDIENTE RF-14]. |
| Responsable | `AssigneePicker` | C.5 |
| Proyecto | `Select` | Preseleccionado con el proyecto actual (RF-11) |
| Etiquetas | `TagInput` | Si son libres o de un catálogo está [PENDIENTE RF-16]. El prototipo muestra un input con sugerencias. |

**Estados**

| Estado | Comportamiento |
|---|---|
| Crear, por defecto | Foco en "Título". |
| Validación | Errores bajo cada campo y resumen al inicio: "Revisa los campos marcados." |
| Guardando | Botón "Guardando…" y formulario deshabilitado. |
| Creado | El modal se cierra, aparece el toast "Ticket creado." y la tarjeta en su columna, con el foco en ella. |
| Editado | Confirmación discreta junto al campo ("Guardado") y `LiveRegion`. |
| Solo lectura | Si el usuario no puede editar (EC-02), los campos se muestran como texto sin controles, con la nota "Solo lectura: no eres el dueño de este ticket." |
| Error al guardar | `InlineAlert` con "Reintentar". Los cambios del usuario se conservan. |
| Edición simultánea | [PENDIENTE RF-39]. No se diseña el aviso de conflicto hasta que se decida la regla. |

**Prompt para Stitch**
> Modal "Nuevo ticket" estilo Apple, blanco, 560 px, radio 20 px, sombra amplia. Campos con etiqueta visible encima: Título, Descripción (área grande), Proyecto y Prioridad en una fila, Fecha y Responsable en otra, y Etiquetas como chips removibles con "+ Agregar". Botones "Cancelar" (secundario) y "Crear ticket" (azul #0066CC). Agregar una segunda pantalla: panel lateral derecho de 480 px con el detalle de "Ticket de ejemplo 1", badges neutros de estado y prioridad, metadatos en lista de dos columnas y sección de comentarios. Variante oscura incluida.

---

## C.5 Asignación de personas

**Propósito:** asignar un único responsable a un ticket, sea uno mismo u otra persona (RF-27, RF-28).

**Componentes:** `AssigneePicker` (`Combobox` con búsqueda: avatar y nombre por opción), la opción fija "Asignarme a mí" arriba, y `Avatar`.

**Layout**

```
Responsable [ (UU) Usuario Uno        ▾ ]
            ┌──────────────────────────────┐
            │ 🔍 Buscar persona…            │
            │ ➜ Asignarme a mí              │
            │ ─────────────────────────────│
            │ (UU) Usuario Uno        ✓     │
            │ (UD) Usuario Dos              │
            │ (UT) Usuario Tres             │
            └──────────────────────────────┘
```

Es de **selección única** (RF-27): elegir otra persona reemplaza la anterior y no hay casillas múltiples. Se usa en el formulario (C.4), en el detalle y como acción rápida desde la tarjeta del tablero (menú contextual "Asignar a…").

**Estados**

| Estado | Comportamiento |
|---|---|
| Sin responsable | Texto `muted` "Sin asignar". Si puede quedar vacío está [PENDIENTE RF-15]. |
| Buscando | Filtra por nombre mientras se escribe; sin resultados: "No se encontró a nadie." |
| Asignado | Toast "Asignado a Usuario Dos." y avatar actualizado en la tarjeta. Por detrás se envía el email (RF-46); en la interfaz no se anuncia el envío. |
| Reasignación | Reemplaza al responsable anterior (EC-04). |
| Sin permiso | El selector se muestra como texto si el usuario no puede editar el ticket (RF-17). |
| Error | El valor anterior vuelve y aparece el toast de error. |

**Prompt para Stitch**
> Selector desplegable de responsable, estilo Apple. Campo "Responsable" con avatar circular de iniciales y nombre "Usuario Uno". El menú abierto tiene un buscador arriba, la opción destacada "Asignarme a mí", un divisor y una lista de personas (Usuario Uno con marca de verificación, Usuario Dos, Usuario Tres). Selección única. Variante oscura.

---

## C.6 Filtros

**Propósito:** encontrar tickets por fecha, prioridad, responsable, proyecto y etiquetas (RF-37).

**Componentes:** `FilterBar`, `FilterDropdown` (uno por criterio), `DateRangeFilter`, `ActiveFilterChips`, `Button ghost` ("Limpiar filtros").

**Layout**

```
[Fecha ▾] [Prioridad ▾] [Responsable ▾] [Proyecto ▾] [Etiquetas ▾]   Limpiar filtros
Filtros activos: [Responsable: Usuario Uno ✕] [etiqueta-a ✕]
```

- Una sola fila encima del contenido; en `sm` se agrupa en un botón "Filtros (n)" que abre un panel.
- El filtro "Proyecto" solo aparece en vistas que muestran varios proyectos. En el tablero el proyecto ya está fijo.
- El resultado se anuncia con el `LiveRegion`: "6 tickets".

**Estados**

| Estado | Comportamiento |
|---|---|
| Sin filtros | No hay chips ni "Limpiar filtros". |
| Un filtro activo | Chip visible y el botón del criterio marcado con un punto `accent` y el texto del valor. |
| Varios criterios a la vez | [PENDIENTE RF-38]. El prototipo muestra la barra permitiendo seleccionar más de uno, pero la lógica de combinación no se define. |
| Sin resultados | "Ningún ticket coincide con los filtros." + "Limpiar filtros" (EC-07). |
| Guardar filtros | No se diseña: [PENDIENTE RF-38]. |

**Prompt para Stitch**
> Barra de filtros horizontal estilo Apple, sobre un tablero kanban. Botones desplegables tipo píldora: Fecha, Prioridad, Responsable, Proyecto y Etiquetas; uno está activo con un punto azul. Debajo, chips removibles "Responsable: Usuario Uno ✕" y "etiqueta-a ✕", y a la derecha un enlace "Limpiar filtros". Incluir el estado vacío "Ningún ticket coincide con los filtros". Variante oscura.

---

## C.7 Eliminar (archivar) ticket

**Propósito:** retirar un ticket sin borrarlo físicamente, dejando trazabilidad (RF-20, RF-23). La acción se llama "Eliminar" (RF-24).

**Componentes:** opción "Eliminar" (`danger`) en el menú `⋯` del detalle y de la tarjeta, `ConfirmDialog`, `Toast`.

**Layout del diálogo de confirmación**

```
¿Eliminar "Ticket de ejemplo 1"?
El ticket dejará de aparecer en el tablero.
Quedará registrado quién lo eliminó y cuándo.
                                  [Cancelar] [ Eliminar ]
```

El foco inicial va a "Cancelar", que es la opción segura, y el botón de confirmar usa la variante `danger`. El texto no dice que el ticket "se borra para siempre", porque no es así (RF-20).

**Estados**

| Estado | Comportamiento |
|---|---|
| Permitido | Rol Usuario sobre un ticket propio (RF-21) o rol Admin sobre cualquiera (RF-22). |
| No permitido | La opción "Eliminar" no aparece (EC-03). |
| Confirmando | Botón en carga "Eliminando…". |
| Eliminado | Se cierra el panel, la tarjeta sale con animación `motion.base` y aparece el toast "Ticket eliminado.". El foco pasa a la siguiente tarjeta o a la columna. |
| Error | Toast de error y la tarjeta se mantiene. |
| Deshacer desde el toast | No se diseña: la restauración está [PENDIENTE RF-25]. |
| Ticket en estado final | Si se permite eliminarlo está [PENDIENTE RF-26]. El prototipo no aplica ninguna restricción. |
| Vista de archivados | No se diseña: [PENDIENTE RF-25]. |

> El texto del botón ("Eliminar" en lugar de "Archivar") está por validar [PENDIENTE RF-24]. Los textos del diálogo explican el efecto real para no engañar al usuario.

**Prompt para Stitch**
> Diálogo de confirmación estilo Apple, centrado, blanco, radio 20 px, sobre un fondo oscurecido. Título "¿Eliminar "Ticket de ejemplo 1"?", texto de dos líneas explicando que dejará de aparecer en el tablero y que quedará registrado quién lo eliminó y cuándo. Botones "Cancelar" (secundario, con foco) y "Eliminar" (rojo #C4262E, texto blanco). Variante oscura con botón #FF6B6B y texto negro.

---

## C.8 Comentarios

**Propósito:** conversar dentro del ticket (RF-40). Los comentarios se pueden editar y borrar, y queda visible quién lo hizo (RF-41 a RF-43).

**Componentes:** `CommentList`, `CommentItem` (avatar, autor, contenido, menú `⋯` con "Editar" y "Borrar"), `CommentComposer` (`TextArea` + "Comentar"), `EditedNote`, `DeletedPlaceholder`.

**Layout (dentro de `TicketDetailPanel`)**

```
Comentarios (3)
(UU) Usuario Uno                                        ⋯
     Comentario de ejemplo.
(UD) Usuario Dos                                        ⋯
     Comentario de ejemplo editado.
     Editado por Usuario Dos
     ⊘ Comentario borrado por Admin Ejemplo
──────────────────────────────────────────────
[ Escribe un comentario…                    ]
                                   [ Comentar ]
```

**Estados**

| Estado | Comportamiento |
|---|---|
| Sin comentarios | "Aún no hay comentarios." |
| Escribiendo | "Comentar" se habilita cuando hay texto. `Ctrl/Cmd + Enter` envía. |
| Publicado | El comentario aparece al final y el `LiveRegion` anuncia "Comentario publicado". |
| Editando | El `CommentItem` pasa a `TextArea` con "Cancelar" y "Guardar". |
| Editado | Se muestra la nota "Editado por [usuario]" (RF-43). |
| Borrado | El contenido se sustituye por "Comentario borrado por [usuario]" en `muted` con ícono (RF-43, EC-08). No desaparece del hilo. |
| Quién puede editar o borrar | [PENDIENTE RF-44]. El prototipo muestra el menú `⋯` solo en los comentarios del propio usuario. |
| Menciones | Formato [PENDIENTE RF-45]. No se diseña autocompletado de menciones. |
| Error al publicar | El texto se conserva y aparece un aviso con "Reintentar". |

**Prompt para Stitch**
> Sección de comentarios dentro de un panel lateral, estilo Apple. Lista con avatar circular, nombre en negrita y texto. Un comentario muestra debajo "Editado por Usuario Dos" en gris, y otro aparece como "Comentario borrado por Admin Ejemplo" en gris con un ícono. Abajo, un área de texto "Escribe un comentario…" y el botón azul "Comentar". Variante oscura.

---

## C.9 Configuración de estados

**Propósito:** definir los estados del flujo que se convierten en columnas del tablero (RF-30), y marcar cuál cuenta como finalizado para el dashboard (RF-36, RF-49).

**Componentes:** `StatusList` (filas reordenables), `StatusRow` (nombre, marca "Final" y menú), `StatusFormModal` (campo "Nombre" y casilla "Cuenta como finalizado"), botón "Nuevo estado".

**Layout**

```
Estados del flujo                                   [ Nuevo estado ]
Los estados son las columnas del tablero.
┌──────────────────────────────────────────────────────────────┐
│ Estado 1                                                  ⋯  │
│ Estado 2                                                  ⋯  │
│ Estado 3                                  [Final]         ⋯  │
└──────────────────────────────────────────────────────────────┘
```

**Estados**

| Estado | Comportamiento |
|---|---|
| Acceso | Quién configura y si los estados son globales o por proyecto está [PENDIENTE RF-35]. En el prototipo la vista aparece en la navegación con el marcador "requiere permiso". |
| Crear o editar | Modal con "Nombre" y la casilla "Cuenta como finalizado". |
| Marcar final | La etiqueta "Final" aparece junto al nombre. El mecanismo definitivo está [PENDIENTE RF-36]. |
| Orden de columnas | No se diseña el reordenamiento: no está en specs.md. |
| Quitar estado con tickets | [PENDIENTE RF-35] (EC-05). La opción "Quitar" se muestra deshabilitada con la nota "Pendiente de definir". |
| Sin estado final | Aviso `warning` con ícono: "Ningún estado cuenta como finalizado. El dashboard no podrá mostrar tickets cerrados." |
| Estados por defecto | [PENDIENTE RF-34]. Se usan los placeholders "Estado 1/2/3". |

**Prompt para Stitch**
> Vista de configuración "Estados del flujo" estilo Apple. Subtítulo "Los estados son las columnas del tablero.", botón azul "Nuevo estado" y una lista en una tarjeta blanca con filas "Estado 1", "Estado 2" y "Estado 3" (esta última con un badge neutro "Final"), cada una con menú de tres puntos. Incluir un aviso amarillo con ícono para el caso sin estado final. Variante oscura.

---

## C.10 Dashboard de métricas

**Propósito:** mostrar cuántos tickets se cierran al mes por proyecto (RF-48). "Cerrado" significa llegar al estado finalizado (RF-49). Lo ven todos los roles (RF-50).

**Componentes:** `DashboardHeader` (título y `DateRangeFilter` de meses), `StatTile` (total de cerrados en el período), `ClosedByMonthChart` (barras apiladas por mes con un segmento por proyecto), `ChartLegend`, `ChartTableToggle` ("Ver como tabla"), `ChartTooltip`.

**Por qué este tipo de gráfica:** la pregunta es cuántos tickets se cierran por mes (magnitud a lo largo del tiempo) desglosados por proyecto (identidad). Por eso se usan barras apiladas con un solo eje, un color por proyecto (A.3.2) y una separación de 2 px entre segmentos.

**Layout**

```
Dashboard                                   [ Últimos 6 meses ▾ ]
┌────────────────────┐
│ Tickets cerrados    │
│ 48                  │  (StatTile, font.display)
└────────────────────┘
┌──────────────────────────────────────────────────────────────┐
│ Tickets cerrados por mes y proyecto        [Ver como tabla]   │
│ ■ Proyecto Alfa  ■ Proyecto Beta  ■ Proyecto Gamma  (leyenda) │
│  12 ┤        ▆▆                                               │
│   8 ┤  ▆▆    ▆▆    ▆▆                                         │
│   4 ┤  ▆▆    ▆▆    ▆▆    ▆▆                                   │
│   0 ┼──────────────────────────                               │
│      Ene   Feb   Mar   Abr   May   Jun                        │
└──────────────────────────────────────────────────────────────┘
```

**Especificación de la gráfica**

- Un solo eje Y (conteo, en enteros desde 0). Líneas de guía finas con `chart.grid` y etiquetas en `color.text.muted`.
- Barras con esquinas superiores redondeadas de 4 px y 2 px de separación entre segmentos, del color `surface.default`.
- Leyenda siempre visible, con el texto en color de texto (no en el color de la serie).
- Tooltip al pasar el mouse o al enfocar una barra con el teclado: mes, total y desglose por proyecto.
- "Ver como tabla": una fila por mes, una columna por proyecto y el total. Es obligatoria porque tres colores del modo claro quedan por debajo de 3:1 (A.3.2).
- Resumen textual para lectores de pantalla (`aria-describedby`): "En el período, se cerraron 48 tickets. El mes con más cierres fue Feb."

**Estados**

| Estado | Comportamiento |
|---|---|
| Cargando | `Skeleton` del indicador y de la gráfica. |
| Sin cierres en el período | "No hay tickets cerrados en este período." |
| Sin estado final configurado | Aviso `warning`: "Configura qué estado cuenta como finalizado para ver métricas." [PENDIENTE RF-36] (EC-05) |
| Más de 8 proyectos | Los adicionales se agrupan como "Otros" (A.3.2). |
| Métricas adicionales | [PENDIENTE RF-51]. No se diseñan. |
| Tickets archivados o reabiertos | Si siguen contando como cerrados está [PENDIENTE RF-25, RF-49] (EC-03, EC-05). |

**Prompt para Stitch**
> Dashboard estilo Apple. Título "Dashboard" con selector "Últimos 6 meses" a la derecha. Una tarjeta indicadora blanca: "Tickets cerrados · 48" en número grande. Debajo, una tarjeta con la gráfica de barras apiladas "Tickets cerrados por mes y proyecto" (Ene a Jun), con segmentos azul #2A78D6, naranja #EB6834 y verde agua #1BAF7A para "Proyecto Alfa", "Proyecto Beta" y "Proyecto Gamma", separaciones blancas de 2 px, leyenda con texto oscuro, líneas de guía muy finas y un botón "Ver como tabla". Variante oscura con fondo #1C1C1E y colores #3987E5, #D95926 y #199E70.

---

## C.11 Modo oscuro

**Propósito:** permitir trabajar con apariencia oscura. Es obligatorio en el MVP (RF-52).

**Componentes:** `ThemeToggle`, un control segmentado con tres opciones: "Claro", "Oscuro" y "Sistema", en la barra superior (A.7).

**Comportamiento**

- Por defecto se usa "Sistema", que sigue `prefers-color-scheme`.
- Al elegir "Claro" u "Oscuro" se establece `data-theme` en `<html>` y la preferencia se guarda en el navegador. Si se debe guardar por usuario en el servidor no está definido; el modelo de datos no lo contempla.
- El cambio aplica los valores de A.3 en toda la interfaz: tablero, formularios, modales, comentarios y gráficas (EC-11).
- La transición de color dura `motion.base` y es instantánea con `prefers-reduced-motion`.

**Accesibilidad:** el control es un `radiogroup` con `aria-label="Tema"`, cada opción tiene texto visible y la opción seleccionada lleva `aria-checked="true"`.

**Estados**

| Estado | Comportamiento |
|---|---|
| Claro | Tokens claros. |
| Oscuro | Tokens oscuros; las sombras se sustituyen por anillos (A.3.5). |
| Sistema | Cambia en vivo si el sistema operativo cambia de tema. |
| Almacenamiento no disponible | Funciona durante la sesión y vuelve a "Sistema" en la siguiente visita. |

**Prompt para Stitch**
> Generar cada pantalla anterior en dos variantes: clara (fondo #F5F5F7, superficies #FFFFFF, texto #1D1D1F, acento #0066CC) y oscura (fondo #000000, superficies #1C1C1E, texto #F5F5F7, acento #4DA3FF con texto negro en botones). En la barra superior, un control segmentado de tres opciones: "Claro", "Oscuro" y "Sistema".

---

## Anexo: pendientes que afectan al prototipo

| Pendiente | Pantallas afectadas |
|---|---|
| RF-13 valores de prioridad | C.3, C.4 (badge sin color) |
| RF-14 significado de "fecha" | C.3, C.4, C.6 |
| RF-15 campos obligatorios | C.4, C.5 |
| RF-16 catálogo de etiquetas | C.4 |
| RF-19 qué es un "ticket propio" | C.3, C.4, C.7 |
| RF-25 / RF-26 consulta y restauración de archivados | C.7, C.10 |
| RF-34 / RF-35 / RF-36 estados | C.3, C.9, C.10 |
| RF-38 combinación de filtros | C.6 |
| RF-39 concurrencia | C.4 |
| RF-44 / RF-45 permisos de comentarios y menciones | C.8 |
| RF-07 / RF-08 proyectos | C.2 |
| RF-04 gestión de usuarios | Sin pantalla |
