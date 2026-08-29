# Generador de prompt y esquema JSON

La app puede delegar el análisis a una IA externa. Genera un prompt, el usuario lo pega en ChatGPT/Claude, y devuelve el JSON a la app.

## Prompt generado

El prompt se arma en tiempo de ejecución con: el texto de la idea (dictado, escrito, o reconstruido desde un proyecto existente), la fecha actual, la moneda y la profundidad configuradas en Ajustes, y el esquema JSON literal.

```
Actúa como analista de innovación y evalúa la siguiente idea. Devuelve ÚNICAMENTE un
objeto JSON válido, sin texto antes ni después, sin bloques de código.

IDEA:
"""
<texto de la idea>
"""

CONTEXTO:
- Fecha de hoy: YYYY-MM-DD (úsala para evaluar el timing).
- Moneda para los costos: USD | CLP | EUR.
- Profundidad del informe: Corto | Completo | Inversor.

ESQUEMA EXACTO (respeta nombres y tipos):
<el JSON de abajo, serializado>

REGLAS:
- viabilidad: entero 0-100, realista, no optimista por defecto.
- originalidad: entero 1-5.
- timing: una de estas etiquetas: "Adelantado", "Oportuno", "A tiempo", "Tarde".
- Cada nota (viabilidad_nota, costo_nota, timing_nota) en una sola frase concreta.
- Las secciones son texto plano de 1 a 3 frases, sin viñetas ni markdown.
- cronograma: entre 2 y 5 hitos.
- preguntas: entre 2 y 5, todas terminan en signo de interrogación.
- Todo en español neutro; los términos técnicos pueden ir en inglés.
- No inventes cifras precisas si no hay base: usa rangos.
```

Cuando el prompt se genera **desde una ficha existente**, el bloque IDEA no es solo la idea original: incluye el nombre del proyecto, todas las secciones del análisis previo, las notas agregadas en las carpetas y las preguntas abiertas. Así la IA reanaliza la idea con todo lo que creció alrededor.

## Esquema JSON esperado

```json
{
  "nombre": "Nombre corto del proyecto",
  "estado": "Sin validar",
  "viabilidad": 72,
  "viabilidad_nota": "Por qué ese porcentaje, en una frase.",
  "costo": "USD 18.400",
  "costo_nota": "Qué cubre esa cifra.",
  "timing": "Oportuno",
  "timing_nota": "Por qué este momento es o no el adecuado.",
  "originalidad": 3,
  "originalidad_nota": "Adaptación de algo existente",
  "idea_base": "La idea explicada en dos frases.",
  "publico": "A quién va dirigido, con edad y contexto.",
  "mvp": "El primer paso concreto para probarla.",
  "riesgos": "Los riesgos principales.",
  "competencia": "Qué existe ya y en qué se diferencia.",
  "ingresos": "Cómo se gana dinero con esto.",
  "recursos": "Personas y recursos necesarios.",
  "cronograma": [{ "cuando": "Mes 1", "que": "Qué se hace en ese periodo." }],
  "preguntas": ["¿Qué falta por resolver?"]
}
```

## Reglas de importación (implementadas en el prototipo)

- Se extrae el primer `{` hasta el último `}`, para tolerar texto o bloques de código alrededor.
- `nombre` es obligatorio; sin él se rechaza el JSON con un mensaje al usuario.
- `viabilidad` se recorta a 0-100; `originalidad` a 1-5 (por defecto 3).
- Los campos de sección (`idea_base`, `publico`, `mvp`, `riesgos`, `competencia`, `ingresos`, `recursos`) se mapean a secciones con ícono y etiqueta fijos; los ausentes simplemente no se muestran.
- `cronograma` y `preguntas` aceptan arreglos vacíos o ausentes.
- El proyecto importado se marca con fuente **"IA externa"** y se agrega como proyecto nuevo, sin sobrescribir el original.
- Errores de parseo se muestran con el mensaje del parser, para que el usuario pueda pedirle a la IA que reemita el JSON.

## Mapeo campo JSON → sección de la ficha

| Campo | Etiqueta en la ficha | Ícono (Phosphor) |
| --- | --- | --- |
| `idea_base` | Idea base | `ph-lightbulb` |
| `publico` | A quién va dirigido | `ph-users-three` |
| `mvp` | Primer paso concreto (MVP) | `ph-flag` |
| `riesgos` | Riesgos principales | `ph-warning` |
| `competencia` | Competencia / qué existe ya | `ph-buildings` |
| `ingresos` | Modelo de ingresos | `ph-currency-circle-dollar` |
| `recursos` | Recursos y personas necesarias | `ph-users` |
