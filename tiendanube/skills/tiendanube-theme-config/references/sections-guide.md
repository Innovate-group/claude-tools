# sections.txt Reference

## Overview

`sections.txt` defines **product sections** that merchants can populate from the admin panel. Each section creates a new tab in `Mi Tiendanube > Organizar productos` where merchants drag and drop products.

## Basic Syntax

```
section_key
    name = Display Name
```

- **section_key**: Internal identifier (snake_case, used in templates)
- **name**: Human-readable name shown in admin (supports translations)

## Example Configuration

```
# config/sections.txt

featured
    name = Productos destacados

new
    name = Productos nuevos

sale
    name = Productos en oferta

best_sellers
    name = Más vendidos
```

## How It Works

### 1. Admin Panel

Each section appears as a tab:

```
Mi Tiendanube > Organizar productos
┌─────────────────────────────────────────────────────────┐
│ [Destacados] [Nuevos] [En oferta] [Más vendidos]        │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  Arrastrá productos aquí para destacarlos               │
│                                                         │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐                 │
│  │ Product │  │ Product │  │ Product │                 │
│  │    1    │  │    2    │  │    3    │                 │
│  └─────────┘  └─────────┘  └─────────┘                 │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### 2. Template Variables

Each section generates a variable with products:

| Section Key | Template Variable | Description |
|-------------|-------------------|-------------|
| `featured` | `{{ featured_products }}` | Array of featured products |
| `new` | `{{ new_products }}` | Array of new products |
| `sale` | `{{ sale_products }}` | Array of sale products |
| `best_sellers` | `{{ best_sellers_products }}` | Array of best sellers |

**Pattern**: `{{ [section_key]_products }}`

### 3. Template Usage

```twig
{# Display featured products #}
{% if featured_products %}
<section class="home-featured" data-store="home-products-featured">
  <div class="container">
    <h2>{{ 'Productos destacados' | translate }}</h2>
    
    <div class="row">
      {% for product in featured_products %}
        <div class="col-6 col-md-3">
          {% include 'snipplets/product-card.tpl' with { product: product } %}
        </div>
      {% endfor %}
    </div>
  </div>
</section>
{% endif %}
```

## Multiple Sections Example

### sections.txt

```
featured
    name = Productos destacados

new
    name = Novedades

sale
    name = Ofertas

collection_summer
    name = Colección Verano

collection_winter
    name = Colección Invierno
```

### home.tpl

```twig
{# Featured Products #}
{% if featured_products %}
  {% include 'snipplets/home/home-products-section.tpl' with {
    products: featured_products,
    title: 'Productos destacados' | translate,
    data_store: 'home-products-featured'
  } %}
{% endif %}

{# New Products #}
{% if new_products %}
  {% include 'snipplets/home/home-products-section.tpl' with {
    products: new_products,
    title: 'Novedades' | translate,
    data_store: 'home-products-new'
  } %}
{% endif %}

{# Sale Products #}
{% if sale_products %}
  {% include 'snipplets/home/home-products-section.tpl' with {
    products: sale_products,
    title: 'Ofertas' | translate,
    data_store: 'home-products-sale'
  } %}
{% endif %}
```

### Reusable Section Snipplet

**snipplets/home/home-products-section.tpl:**

```twig
{#
  Parameters:
  - products (array): Products to display, required
  - title (string): Section title, required
  - data_store (string): Data store anchor, required
  - limit (int): Max products to show, default 8
  - columns (int): Columns on desktop, default 4
#}
{% set limit = limit | default(8) %}
{% set columns = columns | default(4) %}
{% set col_class = 'col-6 col-md-' ~ (12 / columns) %}

<section class="home-products-section py-5" data-store="{{ data_store }}">
  <div class="container">
    <h2 class="section-title text-center mb-4">{{ title }}</h2>
    
    <div class="row">
      {% for product in products | slice(0, limit) %}
        <div class="{{ col_class }} mb-4">
          {% include 'snipplets/product-card.tpl' with { product: product } %}
        </div>
      {% endfor %}
    </div>
  </div>
</section>
```

## Integration with section_order

You can combine sections with `section_order` in settings.txt to let merchants reorder them:

### settings.txt

```
section_order
    name = home_sections_order
    start_index = 0
    sections
        featured = Productos destacados
        new = Novedades
        sale = Ofertas
        banner = Banner promocional
```

### home.tpl

```twig
{% for i in 1..4 %}
  {% set section_key = attribute(settings, 'home_sections_order_' ~ i) %}
  
  {% if section_key == 'featured' and featured_products %}
    {% include 'snipplets/home/home-featured.tpl' %}
  
  {% elseif section_key == 'new' and new_products %}
    {% include 'snipplets/home/home-new.tpl' %}
  
  {% elseif section_key == 'sale' and sale_products %}
    {% include 'snipplets/home/home-sale.tpl' %}
  
  {% elseif section_key == 'banner' %}
    {% include 'snipplets/home/home-banner.tpl' %}
  
  {% endif %}
{% endfor %}
```

## Controlling Display with Settings

Combine sections with settings to give merchants control:

### settings.txt

```
collapse
    title = Productos destacados
    
checkbox
    name = show_featured_products
    description = Mostrar sección de productos destacados

dropdown
    name = featured_products_count
    description = Cantidad de productos a mostrar
    values
        4 = 4 productos
        8 = 8 productos
        12 = 12 productos
```

### defaults.txt

```
show_featured_products = true
featured_products_count = 8
```

### Template

```twig
{% if settings.show_featured_products and featured_products %}
<section class="home-featured">
  <div class="row">
    {% for product in featured_products | slice(0, settings.featured_products_count) %}
      <div class="col-6 col-md-3">
        {% include 'snipplets/product-card.tpl' %}
      </div>
    {% endfor %}
  </div>
</section>
{% endif %}
```

## Multi-language Names

For section names in multiple languages, use translations:

### sections.txt

```
featured
    name = Productos destacados
```

### translations.txt

```
es "Productos destacados"
pt "Produtos em destaque"
en "Featured products"
es_mx "Productos destacados"
```

The admin panel will show the name in the merchant's language.

## Best Practices

1. **Use descriptive keys**: `best_sellers` not `bs`
2. **Limit sections**: Too many tabs confuse merchants (4-6 recommended)
3. **Check for empty**: Always wrap with `{% if section_products %}`
4. **Add data-store**: For app integration anchors
5. **Combine with settings**: Give merchants control over visibility and count
6. **Use consistent naming**: Match section key with template file names

## Common Sections

| Key | Name (ES) | Use Case |
|-----|-----------|----------|
| `featured` | Productos destacados | Hero products, best items |
| `new` | Novedades | Recently added products |
| `sale` | Ofertas | Discounted products |
| `best_sellers` | Más vendidos | Top selling products |
| `collection_*` | Colección X | Seasonal/thematic collections |
| `recommended` | Recomendados | Curated recommendations |
