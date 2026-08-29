# Innovaia — In nova

App para capturar ideas innovadoras en el momento en que surgen (por voz o por escrito), procesarlas y organizarlas como proyectos con viabilidad, costo, timing, público y análisis completo.

Este repositorio contiene, por ahora, **el diseño y el prototipo navegable**. La app nativa todavía no está implementada.

## Qué hay aquí

```
index.html               El prototipo listo para abrir o publicar (un solo archivo, sin dependencias)
docs/HANDOFF.md          Especificación de diseño completa (pantallas, tokens, estados, datos)
docs/PROMPT_SCHEMA.md    El prompt que la app genera y el esquema JSON que espera de vuelta
prototype/               El prototipo en archivos separados (fuente editable)
```

## Ver el prototipo

**Opción rápida:** descarga `index.html` y ábrelo en cualquier navegador. Es autocontenido: no necesita servidor, internet ni instalación.

**Publicarlo con GitHub Pages** (queda una URL que puedes abrir desde el celular):

1. En el repo, ve a *Settings → Pages*.
2. En *Source* elige `Deploy from a branch`, rama `main`, carpeta `/ (root)`. Guarda.
3. En un par de minutos queda disponible en `https://renato03sagredo-lab.github.io/Innovaia/`.
4. Ábrela en Chrome del celular y usa *Añadir a pantalla de inicio* para que se vea como una app.

Nota: abrir un `.html` desde la vista de código de GitHub muestra el código, no la app. Para verla hay que usar Pages o descargar el archivo.

**Fuente editable:** `prototype/In nova.dc.html` (requiere los archivos vecinos `support.js`, `android-frame.jsx` y `_ds/`).

Pantallas incluidas: inicio (lista de ideas), captura por voz con transcripción en vivo, captura escrita, procesamiento, ficha del proyecto (nombre editable, panel de métricas, secciones de análisis, cronograma, carpetas, preguntas abiertas), generador de prompt + carga de JSON, y ajustes.

## Flujo de la app

1. **Capturar** — dictar la idea (transcripción en vivo) o escribirla.
2. **Procesar** — de dos formas:
   - análisis interno de la app (requiere integrar un modelo de lenguaje);
   - **generador de prompt**: la app arma un prompt con la idea, la fecha, la moneda y el esquema JSON; el usuario lo lleva a una IA externa y pega el JSON de vuelta, que se carga como proyecto.
3. **Organizar** — el proyecto queda con nombre editable, métricas, secciones de análisis, cronograma, carpetas para notas y preguntas abiertas. Desde cualquier ficha se puede regenerar un prompt que incluye todo lo agregado después.

## Para implementar

Ver `docs/HANDOFF.md`. Stack recomendado: Kotlin + Jetpack Compose (Android nativo) o React Native.

Pendientes funcionales que el prototipo no cubre:
- speech-to-text real para la transcripción;
- llamada a un modelo de lenguaje para el análisis interno;
- persistencia de proyectos, carpetas y notas;
- portapapeles y lectura de archivos JSON en el dispositivo.

## Diseño

Design system **Nocturne** (dark, acento blurple `#9184d9`, Inter, espaciado compacto). Tokens en `prototype/_ds/.../styles.css` y resumidos en `docs/HANDOFF.md`.
