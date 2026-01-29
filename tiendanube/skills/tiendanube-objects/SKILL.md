---
name: tiendanube-objects
description: Complete reference for TiendaNube/Nuvemshop template objects, variables, and filters. Use when working with .tpl template files, accessing store data, products, cart, customers, or any platform variables. Includes all available objects (store, product, cart, customer, category, etc.) and Twig filters (money, translate, product_image_url, etc.).
---

# TiendaNube Objects & Filters Reference

## Overview

This skill provides complete reference for all objects, variables, and filters available in TiendaNube theme templates (.tpl files). Objects are platform-provided variables accessible via Twig syntax.

## Twig Syntax Basics

**Output expression:**
```twig
{{ object.attribute }}
```

**Control structures:**
```twig
{% if condition %}...{% endif %}
{% for item in array %}...{% endfor %}
```

**Filters (pipe syntax):**
```twig
{{ value | filter }}
{{ value | filter(param) }}
{{ value | filter1 | filter2 }}
```

## Quick Object Reference

| Object | Description | Common Use |
|--------|-------------|------------|
| `store` | Store information | Name, URLs, social links |
| `product` | Product data | Name, price, images, variants |
| `category` | Category data | Name, products, subcategories |
| `cart` | Shopping cart | Items, total, count |
| `customer` | Logged-in customer | Name, orders, addresses |
| `settings` | Theme settings | Values from settings.txt |
| `section` | Homepage sections | Featured products |
| `page` | Custom pages | Content, URL |
| `language` | Current language | Code, name |

## Essential Filters

### Price Formatting
```twig
{{ product.price | money }}           {# $1,234.56 #}
{{ product.price | money_nocents }}   {# $1,234 #}
```

### Translation
```twig
{{ 'Agregar al carrito' | translate }}
{{ 'Envío gratis a partir de {1}' | translate(amount | money) }}
```

### Images
```twig
{{ product.featured_image | product_image_url('medium') }}
```
Sizes: `tiny` (50px), `thumb` (100px), `small` (240px), `medium` (320px), `large` (480px), `huge` (640px), `original` (1024px)

### URLs
```twig
{{ 'css/style.css' | static_url }}
{{ 'image.jpg' | static_url }}
```

### HTML Generation
```twig
{{ 'style.css' | static_url | css_tag }}
{{ 'script.js' | static_url | script_tag }}
{{ store.logo | img_tag }}
{{ url | a_tag('Link text') }}
```

## Common Patterns

### Display Product
```twig
<div class="product">
  <img src="{{ product.featured_image | product_image_url('medium') }}" 
       alt="{{ product.name }}">
  <h3>{{ product.name }}</h3>
  {% if product.compare_at_price %}
    <span class="old-price">{{ product.compare_at_price | money }}</span>
  {% endif %}
  <span class="price">{{ product.price | money }}</span>
</div>
```

### Check Stock
```twig
{% if product.available %}
  <button>{{ 'Agregar al carrito' | translate }}</button>
{% else %}
  <button disabled>{{ 'Sin stock' | translate }}</button>
{% endif %}
```

### Loop Products
```twig
{% for product in products %}
  {% include 'snipplets/product-card.tpl' %}
{% endfor %}
```

### Navigation Menu
```twig
{% for item in navigation %}
  <a href="{{ item.url }}" 
     {% if item.current %}class="active"{% endif %}>
    {{ item.name }}
  </a>
  {% if item.subitems %}
    {% for subitem in item.subitems %}
      <a href="{{ subitem.url }}">{{ subitem.name }}</a>
    {% endfor %}
  {% endif %}
{% endfor %}
```

### Cart Summary
```twig
{% if cart.items_count > 0 %}
  <span>{{ cart.items_count }} {{ 'productos' | translate }}</span>
  <span>{{ cart.total | money }}</span>
{% endif %}
```

### Conditional by Country
```twig
{% if store.country == 'BR' %}
  {# Brazil-specific content #}
{% elseif store.country == 'AR' %}
  {# Argentina-specific content #}
{% endif %}
```

### Check Settings
```twig
{% if settings.show_slider %}
  {% include 'snipplets/slider.tpl' %}
{% endif %}
```

## Resources

### Detailed References
- `references/objects.md` - Complete object attributes
- `references/filters.md` - All available filters

### Object Availability by Template

| Template | Available Objects |
|----------|-------------------|
| layout.tpl | store, settings, cart, customer, navigation, languages |
| home.tpl | + sections |
| product.tpl | + product |
| category.tpl | + category, products, pages, filters |
| cart.tpl | + cart (detailed) |
| page.tpl | + page |
| search.tpl | + products, search_query |
| contact.tpl | + contact_result |
| account/*.tpl | + customer (detailed), orders |
