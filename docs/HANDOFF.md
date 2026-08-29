# Handoff: In nova — app de captura y análisis de ideas

## Overview
"In nova" ayuda a capturar ideas innovadoras en el momento en que surgen (por voz o por escrito), las procesa y las organiza como un "proyecto": ficha con viabilidad, costo, timing, público, y varias secciones de análisis. Cada proyecto tiene nombre editable y carpetas donde el usuario sigue agregando información suelta (notas, referencias, preguntas abiertas) a medida que la idea madura.

## About the Design Files
Los archivos de este paquete (`prototype.html` + `android-frame.jsx`) son **referencias de diseño hechas en HTML** — un prototipo navegable que muestra look & behavior, no código de producción para copiar tal cual. La tarea es **recrear este diseño en el entorno real de la app** (recomendado: Kotlin + Jetpack Compose para Android nativo, o React Native si se busca cross-platform) usando los patrones y librerías propias de ese entorno — el HTML es la referencia visual y de flujo, no el artefacto final.

## Fidelity
**Alta fidelidad (hifi).** Colores, tipografía, espaciados y textos son finales — replicar con precisión. Las animaciones (waveform, halos, transiciones) describen la intención de movimiento; en Compose/RN se implementan con las primitivas de animación nativas, no se necesita igualar el CSS literal.

## Design Tokens (design system "Nocturne")

Colores:
- Fondo: `#161826`
- Superficie (cards, sheets): `#232532`
- Texto: `#e9e9ed`
- Acento (único, blurple): `#9184d9`
- Rampa de acento: 100 `#f5f4ff` · 300 `#d2cefd` · 500 `#968ae0` · 600 `#796cbf` · 700 `#5d5294` · 800 `#423a6a` · 900 `#2b2741`
- Rampa neutral: 500 `#9397ab` · 700 `#595d6c` · 800 `#3f424d` · 900 `#292b31`
- Divisor: `#e9e9ed` a 16% de opacidad sobre el fondo

Tipografía: Inter (heading y body). Heading peso 500, nunca bold. Tamaños vistos en el prototipo: 34/27/26/24/23/19/16px según jerarquía; cuerpo 14-15px; etiquetas/kickers 10-11px en mayúsculas con letter-spacing 0.08-0.12em.

Espaciado (escala compacta, 0.7×): 2.8 / 5.6 / 8.4 / 11.2 / 16.8 / 22.4 px.

Radios: sm 4px, md 8px, lg 14px.

Sombras: `--shadow-sm` = borde 1px `#3f424d`; `--shadow-lg` (para el bottom sheet) = borde 1px `#9397ab` + sombra ambiental `0 16px 40px rgba(0,0,0,.65)`.

Iconografía: Phosphor Icons (outline/regular), tamaños 12-30px según contexto.

Botones: primario = contorno de acento sobre transparente (nunca relleno sólido); secundario = contorno neutral; ghost = sin borde, texto atenuado.

## Screens / Views

### 1. Inicio (lista de proyectos)
- **Propósito**: ver todas las ideas guardadas y arrancar una nueva captura.
- **Layout**: columna completa. Header centrado con halo radial de acento detrás del título "Ideas innovadoras" (26px), subtítulo con el conteo de proyectos ("N proyectos"), línea degradada de acento debajo. Lista scrolleable de tarjetas. Al pie: botón circular de micrófono (68px) con halo pulsante (`glow`, 3.2s) y texto "Dicta la idea mientras la tengas fresca". Barra inferior de 2 tabs: Ideas / Ajustes.
- **Tarjeta de proyecto**: fila horizontal — anillo de progreso circular (46px, `conic-gradient` de acento sobre neutral-800) con el % de viabilidad en el centro; nombre del proyecto (16px, peso 500, hasta 2 líneas); meta-línea con ícono de fuente (micrófono o lápiz) + fecha + "N notas · N carpetas"; chevron a la derecha. Fondo con gradiente sutil surface→bg, sombra `shadow-sm`, hover: anillo de acento + sombra elevada.
- **Estado vacío**: no diseñado explícitamente — si no hay proyectos, mostrar solo el header y el CTA de micrófono.

### 2. Captura por voz (grabando)
- **Propósito**: dictar la idea; ver transcripción en vivo.
- **Layout**: header con "Cancelar" (X) a la izquierda y selector segmentado Voz/Escribir a la derecha. Waveform animado (18 barras verticales, alturas variables 20-72px, animación `wv` con duraciones y delays distintos por barra para efecto orgánico) centrado, atenuado (opacity 0.28) cuando no está grabando. Debajo: cronómetro grande (mm:ss, 38px, tabular-nums) y estado ("Listo para grabar" / "Grabando" / "En pausa") en acento, mayúsculas. Panel de transcripción (surface, radius-md, scrolleable) con label "Transcripción en vivo" y el texto reconocido + cursor parpadeante (▌) en acento mientras grava. Al pie: botón circular de grabar/pausar (70px, ícono micrófono/pausa, halo `glow` 2.4s) + botón secundario "Procesar idea →" (deshabilitado hasta que haya texto transcrito). Debajo del todo, un ghost button "o escribirla (opcional)" para saltar a texto.
- **Interacción**: tocar el micrófono alterna grabar/pausar. Tocar "Procesar idea" navega a Procesando. El botón de inicio ("Dictar") desde Inicio entra directo a esta pantalla y arranca la grabación automáticamente.

### 3. Captura escrita
- **Propósito**: escribir la idea a mano en vez de dictarla.
- **Layout**: mismo header con selector segmentado (Escribir activo). Título "Escribe la idea" (23px). Textarea grande ocupando el espacio disponible, placeholder "Se me ocurrió que…". Debajo, contador de palabras o mensaje "Mínimo una frase para poder procesar". Footer: ghost button "Usar ejemplo" (autocompleta un texto de muestra) + botón primario "Procesar idea →" (deshabilitado si el texto es muy corto, mínimo ~12 caracteres).

### 4. Procesando / análisis
- **Propósito**: feedback de que el sistema está interpretando la idea.
- **Layout**: centrado verticalmente. Kicker "Procesando" en acento + título "Ordenando tu idea en un proyecto" (26px). Lista de 5 pasos secuenciales, cada uno con ícono (waveform / lightbulb / calculator / target / folder-open) que cambia a check cuando se completa; opacidad decreciente para los pasos futuros (0.75 el actual, 0.32 los pendientes). Barra de progreso lineal (2px) + porcentaje numérico. Los pasos: "Transcribiendo el audio" → "Extrayendo la idea base" → "Estimando costo y viabilidad" → "Evaluando timing y público" → "Armando el proyecto". Al completarse los 5, navega automáticamente a la Ficha del proyecto.
- **Timing**: cada paso tarda ~700ms en el prototipo (configurable); en producción, el tiempo real depende de la latencia de transcripción + llamada al modelo de análisis.

### 5. Ficha del proyecto (informe)
- **Propósito**: vista principal de una idea ya procesada — panel de métricas arriba, secciones de análisis abajo, carpetas y preguntas abiertas.
- **Layout**: header con "← Ideas" para volver y acciones (compartir, más opciones). Debajo, scroll vertical con:
  1. **Nombre editable**: textarea de 2 líneas sin borde visible salvo un underline, con halo radial de acento detrás (kicker "Nombre editable" con ícono de lápiz). Debajo, tags: fuente (Voz/Escrita), fecha, estado ("Sin validar" / "En validación" / "Pausada").
  2. **Panel de métricas** (grid 2 columnas):
     - Card ancha (full width): "Viabilidad de implementación" — número grande en % (30px, acento) + barra de progreso + nota explicativa.
     - Card: "Costo de producción" — monto + nota.
     - Card: "Timing" — etiqueta ("Oportuno"/"Adelantado"/"A tiempo") + nota sobre si está bien planteada respecto a la fecha actual.
     - Card ancha: "Nivel de originalidad" — 5 puntos (dots) rellenos según nivel + etiqueta descriptiva ("Adaptación de algo existente", "Poco visto en la ciudad", etc).
  3. **Secciones de análisis** (icono + label en mayúsculas + cuerpo de texto), en este orden: Idea base, A quién va dirigido, Primer paso concreto (MVP), Riesgos principales, Competencia / qué existe ya, Modelo de ingresos, Recursos y personas necesarias.
  4. **Cronograma sugerido**: timeline vertical con línea de acento, puntos y pares "cuándo → qué" (ej. "Mes 1 — Acuerdo con la operadora...").
  5. **Carpetas**: lista expandible. Cada carpeta muestra ícono + nombre + contador de items + chevron; al expandir, lista sus notas y un botón "Añadir información" que abre un bottom sheet. Botón de lápiz junto a cada carpeta abre un sheet para **editar nombre e ícono** (grid de 10 íconos seleccionables). Botón "Nueva carpeta" al final de la lista crea una carpeta vacía.
  6. **Preguntas abiertas**: card con fondo degradado sutil de acento, ícono de pregunta por cada ítem, botón "Añadir pregunta" que abre el mismo tipo de bottom sheet en modo texto.
- **Bottom sheet** (usado para notas de carpeta, nueva pregunta, y edición de carpeta): fondo oscurecido, panel `surface` con radius-lg solo arriba, título dinámico según contexto, botón "Cerrar", contenido variable (textarea para notas/preguntas; campo de texto + grid de íconos para editar carpeta), botón primario "Guardar" (deshabilitado hasta cumplir validación mínima).

### 6. Ajustes
- **Propósito**: preferencias de captura y análisis, cuenta.
- **Layout**: título "Ajustes" (27px). Sección "Captura": 4 toggles (Transcripción en vivo, Procesar al terminar, Recordar ideas sin revisar, Guardar audio offline), cada uno con label + nota descriptiva + switch custom (track/knob en tonos de acento cuando está activo). Sección "Análisis": selector segmentado de 3 opciones para "Profundidad del informe" (Corto/Completo/Inversor) y otro de 3 para "Moneda para estimar costos" (USD/CLP/EUR). Sección "Cuenta": avatar circular con iniciales, nombre, estado de sincronización, versión de la app. Barra inferior de tabs (Ideas/Ajustes, con Ajustes resaltado en acento).

## Interactions & Behavior
- **Navegación**: Inicio → (micrófono) → Voz-grabando → Procesando → Ficha del proyecto. Inicio → (Escribirla) → Captura escrita → Procesando → Ficha. Desde la ficha, "← Ideas" vuelve a Inicio. Tab bar cambia entre Inicio y Ajustes.
- **Grabación de voz**: al tocar el micrófono alterna grabar/pausar; mientras grava, la transcripción se va completando palabra por palabra (en producción: streaming real de un servicio de speech-to-text). El botón "Procesar idea" se habilita en cuanto hay algo transcrito.
- **Escritura**: el botón "Procesar idea" se habilita con un mínimo de texto (evitar procesar una idea vacía o de una palabra).
- **Procesamiento**: secuencia de 5 pasos con progreso visual; al finalizar, genera el objeto "proyecto" con todos los campos de análisis y navega a la ficha.
- **Nombre del proyecto**: editable in-place en la ficha (textarea sin bordes, autoexpansible a 2 líneas).
- **Carpetas**: expandir/colapsar con animación de entrada de los items (`up`, translateY + fade, 250ms). Editar nombre/ícono vía bottom sheet. Los items no son editables individualmente en el prototipo — solo agregables.
- **Preguntas abiertas**: se agregan vía el mismo patrón de bottom sheet; no hay edición/borrado en el prototipo, solo alta.
- **Toggles y selectores de Ajustes**: puramente locales, sin persistencia real definida (definir en producción si viven en backend o solo local).
- **Animaciones**:
  - `wv` (waveform): `scaleY` de 0.16 a 1, alternada, duraciones 0.75-1.25s por barra con distintos delays.
  - `glow` (halos de botones): opacidad 0.28↔0.62 (fondo) o 0.35↔1 (grabando), 2.4-3.2s ease-in-out infinito.
  - `up` (aparición de sheets/items): translateY(8px)→0 + fade, 220-250ms ease.
  - `fade`: opacity 0→1, 180-300ms, usado en tarjetas y overlays.

## State Management
Estado principal por sesión (sugerido, no prescriptivo del backend):
- `screen`: pantalla activa (inicio | voz | escribir | procesando | proyecto | ajustes).
- `recording`, `elapsedTime`, `transcript`: estado de la captura de voz en curso.
- `typedText`: texto de la captura escrita en curso.
- `processingStep`: paso actual del análisis (0-5).
- `projects[]`: lista de proyectos, cada uno con: `id, name, createdAt, source (voz|escrita), stage, viability (0-100), viabilityNote, cost, costNote, timing, timingNote, originality (1-5), originalityLabel, sections[] {icon, label, body}, timeline[] {when, what}, questions[] {text}, folders[] {name, icon, items[] {text}}`.
- `activeProjectId`: proyecto abierto en la ficha.
- `openFolders`: qué carpetas están expandidas (por índice).
- `sheet`: qué bottom sheet está abierto y con qué contexto (nota de carpeta / nueva pregunta / edición de carpeta).
- `settings`: los 4 toggles + profundidad de informe + moneda.

**Requisitos de datos para producción** (no cubiertos por el prototipo, que usa datos de ejemplo fijos):
- Integración de speech-to-text para la transcripción real.
- Llamada a un modelo (LLM) que reciba el texto/transcripción y devuelva los campos estructurados: idea base, público, viabilidad %, costo, timing, MVP, riesgos, competencia, modelo de ingresos, recursos, cronograma, nivel de originalidad, preguntas abiertas — este es el corazón funcional de la app y debe implementarse con una llamada real a un modelo de lenguaje.
- Persistencia de proyectos y carpetas (base de datos local y/o remota).

## Assets
No hay imágenes ni fotografías. Toda la iconografía es Phosphor Icons (vía CDN `@phosphor-icons/web`, estilo "regular") referenciada por nombre de clase (`ph-microphone`, `ph-lightbulb`, etc.) — en producción, empaquetar el set de iconos elegido (Phosphor tiene librería nativa para Android/Compose, o usar SVGs vectoriales equivalentes).

## Files
- `prototype.html` — el prototipo HTML completo (todas las 6 pantallas + estados, en un solo archivo).
- `android-frame.jsx` — solo el marco de dispositivo usado para visualizar el prototipo en el navegador; no es parte del diseño de la app en sí, ignorar en la implementación.
- `nocturne-styles.css` — la hoja de tokens del design system Nocturne (colores, tipografía, espaciado, componentes base) referida en este README.
