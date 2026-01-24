---
name: shopify-liquid-developer
description: |
  Desarrollo completo de temas Shopify Liquid: secciones, bloques, snippets,
  templates, y assets. Usar cuando el usuario pida crear componentes Liquid
  como carruseles, secciones multicolumna, formularios, grids de productos,
  o cualquier elemento de tema Shopify. Triggers: "crea una seccion",
  "liquid section", "shopify theme", "carrusel", "slider", "multicolumna",
  "cotizar envio", "bloque liquid", "snippet", "seccion responsive",
  "grid de productos", "hero banner", "testimonios", "FAQ accordion"
---

# Shopify Liquid Development

## Paso Obligatorio

Antes de generar CUALQUIER codigo Liquid, SIEMPRE ejecutar:

```
mcp__shopify-dev__learn_shopify_api(api: "liquid")
```

Esto carga documentacion completa de Liquid incluyendo arquitectura de temas, sintaxis, filtros, tags, objetos y ejemplos.

## Convenciones

### Idioma
- SIEMPRE escribir los nombres y labels de settings y bloques en INGLES
- Esto aplica a: `name`, `label`, `info`, `placeholder` en schemas
- Ejemplo: usar "Background color" en lugar de "Color de fondo"

### Naming
- Variables: `snake_case` (ej: `product_card_title`, `background_image_url`)
- Clases CSS: `kebab-case` con prefijos BEM (ej: `section-name__element--modifier`)
- IDs de settings: `snake_case` coincidiendo con nombres de variables

### CSS
- **CSS pequeno**: usar `{% style %}...{% endstyle %}` inline dentro de la seccion
- **CSS extenso**: crear archivo `.css` separado en `assets/` e incluirlo con:
  ```liquid
  {{ 'section-name.css' | asset_url | stylesheet_tag }}
  ```
- NUNCA estilos inline excepto CSS variables desde settings
- Tailwind soportado; fallback a `theme.css`, `base.css`, o `innovate.css`
- CSS moderno: Grid, Flexbox, custom properties, clamp()

### JavaScript
- **JS pequeno**: usar `<script defer>...</script>` inline dentro de la seccion
- **JS complejo** (custom elements, logica extensa): crear archivo `.js` en `assets/` e incluirlo con:
  ```liquid
  <script src="{{ 'section-name.js' | asset_url }}" defer></script>
  ```
- SIEMPRE Vanilla JS - NUNCA jQuery ni librerias externas
- ES6+: const/let, arrow functions, template literals, async/await
- APIs nativas: `fetch()`, `querySelector()`, `addEventListener()`

### Schemas
- SIEMPRE incluir `default` en TODOS los settings
- SIEMPRE validar campos con `!= blank` en Liquid
- SIEMPRE incluir `presets` con el mismo nombre asignado a la seccion
- Organizar settings con separadores `header`
- Usar translation keys (`t:`) para todos los labels

Ejemplo de presets:
```json
{
  "name": "Featured Collection",
  "presets": [
    {
      "name": "Featured Collection"
    }
  ]
}
```

Ver [references/schema-patterns.md](references/schema-patterns.md) para patrones de validacion.

## Workflow

1. Llamar `learn_shopify_api(api: "liquid")` para cargar documentacion
2. Identificar tipo de componente: section, block, o snippet
3. Generar codigo siguiendo convenciones anteriores
4. SIEMPRE validar con `validate_theme` antes de presentar al usuario
5. Incluir keys de traduccion para `locales/en.default.json`

## Tipos de Componente

**Section** - Componente modular de ancho completo con schema
```liquid
<div class="section-name">
  {% content_for 'blocks' %}
</div>
{% style %}...{% endstyle %}
<script defer>...</script>
{% schema %}...{% endschema %}
```

**Block** - Componente reutilizable y anidable con schema
```liquid
{% doc %}...{% enddoc %}
<div {{ block.shopify_attributes }}>...</div>
{% style %}...{% endstyle %}
{% schema %}...{% endschema %}
```

**Snippet** - Fragmento reutilizable sin schema, con LiquidDoc
```liquid
{% doc %}
  @param {type} name - Description
{% enddoc %}
...contenido...
{% style %}...{% endstyle %}
```

## Validacion de Codigo

Despues de generar archivos de tema, SIEMPRE validar:

```
mcp__shopify-dev__validate_theme(
  conversationId: "<id>",
  absoluteThemePath: "<path>",
  filesCreatedOrUpdated: [{ path: "sections/ejemplo.liquid" }]
)
```

Corregir errores y re-validar hasta que el codigo pase.
