# Schema Patterns

## Defaults Obligatorios

Cada setting DEBE tener un valor `default`:

### Text
```json
{
  "type": "text",
  "id": "heading_text",
  "label": "t:sections.example.settings.heading.label",
  "default": "Default Heading"
}
```

### Select
```json
{
  "type": "select",
  "id": "layout_style",
  "label": "t:sections.example.settings.layout.label",
  "options": [
    { "value": "grid", "label": "t:options.layout.grid" },
    { "value": "list", "label": "t:options.layout.list" }
  ],
  "default": "grid"
}
```

### Range
```json
{
  "type": "range",
  "id": "items_per_row",
  "label": "t:sections.example.settings.items_per_row.label",
  "min": 1,
  "max": 6,
  "step": 1,
  "default": 3
}
```

### Checkbox
```json
{
  "type": "checkbox",
  "id": "show_vendor",
  "label": "t:sections.example.settings.show_vendor.label",
  "default": false
}
```

### Color
```json
{
  "type": "color",
  "id": "background_color",
  "label": "t:sections.example.settings.background.label",
  "default": "#ffffff"
}
```

## Validacion != blank

### Textos
```liquid
{% if section.settings.heading_text != blank %}
  <h2 class="section__heading">{{ section.settings.heading_text }}</h2>
{% endif %}
```

### Imagenes
```liquid
{% if section.settings.background_image != blank %}
  {{ section.settings.background_image | image_url: width: 1920 | image_tag }}
{% else %}
  {{ 'collection-1' | placeholder_svg_tag: 'placeholder-svg' }}
{% endif %}
```

### Colecciones
```liquid
{% if section.settings.collection != blank %}
  {% for product in section.settings.collection.products limit: section.settings.products_to_show %}
    {% render 'product-card', product: product %}
  {% endfor %}
{% else %}
  <p>{{ 'sections.collection.no_collection' | t }}</p>
{% endif %}
```

### Contenido de Blocks
```liquid
{% for block in section.blocks %}
  {% case block.type %}
    {% when 'slide' %}
      {% if block.settings.image != blank %}
        <div class="carousel__slide" {{ block.shopify_attributes }}>
          {{ block.settings.image | image_url: width: 1200 | image_tag }}
          {% if block.settings.caption != blank %}
            <p class="carousel__caption">{{ block.settings.caption }}</p>
          {% endif %}
        </div>
      {% endif %}
  {% endcase %}
{% endfor %}
```

## Organizacion con Headers

```json
{
  "settings": [
    {
      "type": "header",
      "content": "t:sections.example.settings.header_content.content"
    },
    {
      "type": "text",
      "id": "heading",
      "label": "t:sections.example.settings.heading.label",
      "default": "Section Title"
    },
    {
      "type": "richtext",
      "id": "description",
      "label": "t:sections.example.settings.description.label",
      "default": "<p>Section description</p>"
    },
    {
      "type": "header",
      "content": "t:sections.example.settings.header_layout.content"
    },
    {
      "type": "select",
      "id": "layout",
      "label": "t:sections.example.settings.layout.label",
      "options": [
        { "value": "full", "label": "t:options.layout.full_width" },
        { "value": "contained", "label": "t:options.layout.contained" }
      ],
      "default": "contained"
    }
  ]
}
```

## Presets con Blocks

```json
{
  "presets": [
    {
      "name": "t:sections.carousel.name",
      "settings": {
        "autoplay": true,
        "autoplay_speed": 5
      },
      "blocks": [
        { "type": "slide" },
        { "type": "slide" },
        { "type": "slide" }
      ]
    }
  ]
}
```
