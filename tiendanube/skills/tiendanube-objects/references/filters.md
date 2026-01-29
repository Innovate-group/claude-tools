# TiendaNube Filters Reference

Filters are functions applied to values using the pipe `|` syntax.

## Price Filters

### money
Format price with currency symbol.
```twig
{{ product.price | money }}
{# Output: $1,234.56 #}
```

### money_nocents
Format price without cents.
```twig
{{ product.price | money_nocents }}
{# Output: $1,234 #}
```

---

## Translation Filters

### translate (or t)
Translate text using translations.txt.
```twig
{{ 'Agregar al carrito' | translate }}
{{ 'Agregar al carrito' | t }}  {# shorthand #}
```

### translate with parameters
Replace `{1}`, `{2}` placeholders.
```twig
{{ 'Envío gratis a partir de {1}' | translate(minimum | money) }}
```

### pluralize
Get singular or plural form.
```twig
{{ 'producto en tu carrito' | translate | pluralize(cart.items_count) }}
```
Note: Singular and plural forms must be consecutive in translations.txt.

---

## Image Filters

### product_image_url
Get product image URL in specific size.
```twig
{{ product.featured_image | product_image_url('medium') }}
```

**Sizes:**
| Size | Width |
|------|-------|
| `tiny` | 50px |
| `thumb` | 100px |
| `small` | 240px |
| `medium` | 320px |
| `large` | 480px |
| `huge` | 640px |
| `original` | 1024px |

### img_tag
Convert URL to img element.
```twig
{{ store.logo | img_tag }}
{# Output: <img src="..." alt="..." title="..."> #}
```

### img_tag with class
```twig
{{ image_url | img_tag('', 'img-fluid') }}
```

---

## URL Filters

### static_url
Get URL for static file in theme.
```twig
{{ 'css/style.css' | static_url }}
{{ 'images/banner.jpg' | static_url }}
```

### css_tag
Convert to stylesheet link.
```twig
{{ 'css/style-colors.scss.tpl' | static_url | css_tag }}
{# Output: <link rel="stylesheet" type="text/css" href="..."> #}
```

### script_tag
Convert to script element.
```twig
{{ 'js/store.js' | static_url | script_tag }}
{# Output: <script type="text/javascript" src="..."></script> #}

{# Async script #}
{{ 'js/store.js' | static_url | script_tag(true) }}
```

### a_tag
Create link element.
```twig
{{ url | a_tag('Link Text') }}
{{ url | a_tag('Text', 'btn btn-primary') }}
{# Output: <a href="..." class="btn btn-primary">Text</a> #}
```

### add_param
Add URL parameter.
```twig
{{ url | add_param('page', 2) }}
{# http://example.com → http://example.com?page=2 #}
```

### add_params
Add multiple parameters.
```twig
{{ url | add_params({ page: 2, sort: 'price' }) }}
```

### is_external
Check if URL is external.
```twig
{% if item.url | is_external %}target="_blank"{% endif %}
```

---

## Logo Filters

### payment_logo
Get payment method logo URL.
```twig
{{ 'visa' | payment_logo }}
```

### shipping_logo
Get shipping method logo URL.
```twig
{{ 'ups' | shipping_logo }}
```

### flag_url
Get country flag URL.
```twig
{{ 'ar' | flag_url }}
{# Output: https://.../flags/ar.png #}
```

---

## Text Filters

### raw
Output without HTML escaping.
```twig
{{ product.description | raw }}
{{ settings.css_code | raw }}
```

### slugify
Convert to URL-safe string.
```twig
{{ product.name | slugify }}
{# "Mi Producto!" → "mi-producto" #}
```

### truncate
Limit string length.
```twig
{{ product.description | truncate(100) }}
```

### strip_tags
Remove HTML tags.
```twig
{{ product.description | strip_tags }}
```

### nl2br
Convert newlines to `<br>`.
```twig
{{ text | nl2br }}
```

---

## Array Filters

### first
Get first element.
```twig
{{ products | first }}
```

### last
Get last element.
```twig
{{ products | last }}
```

### take
Get first N elements.
```twig
{{ products | take(4) }}
```

### length
Get array length.
```twig
{{ products | length }}
```

### sort
Sort array.
```twig
{{ products | sort }}
```

### reverse
Reverse array.
```twig
{{ products | reverse }}
```

### join
Join array elements.
```twig
{{ tags | join(', ') }}
```

---

## Validation Filters

### is_valid_email
Check email validity.
```twig
{% if email | is_valid_email %}
  {# Valid email #}
{% endif %}
```

---

## Address Filters

### format_address
Format customer address as HTML.
```twig
{{ customer | format_address }}
```

---

## Social Filters

### fb_like
Generate Facebook like button.
```twig
{{ product.social_url | fb_like }}
```

### tweet_button
Generate Twitter share button.
```twig
{{ product.social_url | tweet_button }}
```

### pinterest_button
Generate Pinterest pin button.
```twig
{{ product.featured_image | product_image_url('large') | pinterest_button(product.social_url, product.name) }}
```

---

## Google Fonts Filter

### google_fonts_url
Generate Google Fonts URL.
```twig
{{ [settings.font_headings, settings.font_rest] | google_fonts_url('300,400,700') }}
```

---

## Date Filters

### date
Format date.
```twig
{{ order.date | date('d/m/Y') }}
```

---

## Number Filters

### number_format
Format number with separators.
```twig
{{ value | number_format(2, ',', '.') }}
```

### round
Round number.
```twig
{{ value | round }}
{{ value | round(2) }}
```

### abs
Absolute value.
```twig
{{ value | abs }}
```

---

## Conditional Filters

### default
Provide fallback value.
```twig
{{ variable | default('fallback') }}
```

---

## Common Usage Patterns

### Product Card
```twig
<div class="product-card">
  <a href="{{ product.url }}">
    <img src="{{ product.featured_image | product_image_url('medium') }}" 
         alt="{{ product.name }}" 
         class="img-fluid">
  </a>
  <h3>{{ product.name }}</h3>
  {% if product.compare_at_price %}
    <del>{{ product.compare_at_price | money }}</del>
  {% endif %}
  <span>{{ product.price | money }}</span>
</div>
```

### Navigation with External Check
```twig
{% for item in navigation %}
  <a href="{{ item.url }}" 
     {% if item.url | is_external %}target="_blank" rel="noopener"{% endif %}
     {% if item.current %}class="active"{% endif %}>
    {{ item.name }}
  </a>
{% endfor %}
```

### Translated Button with Stock Check
```twig
{% if product.available %}
  <button class="btn btn-primary">
    {{ 'Agregar al carrito' | translate }}
  </button>
{% else %}
  <button class="btn btn-secondary" disabled>
    {{ 'Sin stock' | translate }}
  </button>
{% endif %}
```

### Pagination
```twig
{% if pages.amount > 1 %}
  {% if pages.previous %}
    <a href="{{ pages.previous }}">{{ 'Anterior' | translate }}</a>
  {% endif %}
  
  {% for page in pages.numbers %}
    <a href="{{ page.url }}" 
       {% if page.selected %}class="active"{% endif %}>
      {{ page.number }}
    </a>
  {% endfor %}
  
  {% if pages.next %}
    <a href="{{ pages.next }}">{{ 'Siguiente' | translate }}</a>
  {% endif %}
{% endif %}
```

### Load Stylesheets
```twig
{# Critical inline CSS #}
<style>
{% include "static/css/style-critical.tpl" %}
</style>

{# Dynamic colors from settings #}
{{ 'css/style-colors.scss.tpl' | static_url | css_tag }}

{# Custom CSS from admin #}
<style>
{{ settings.css_code | raw }}
</style>
```

### Load Scripts
```twig
{# Sync script #}
{{ 'js/vendor.js' | static_url | script_tag }}

{# Async script #}
{{ 'js/analytics.js' | static_url | script_tag(true) }}

{# External script #}
{{ '//ajax.googleapis.com/ajax/libs/jquery/3.6.0/jquery.min.js' | script_tag }}
```
