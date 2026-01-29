---
name: twig-advanced
description: Advanced Twig template patterns and techniques for code reuse. Use when working with macros, embeds, includes, template inheritance, escaping, whitespace control, arrow functions, or collection filters. Applicable to TiendaNube themes and any Twig-based project.
---

# Advanced Twig Patterns

Reference for advanced Twig templating techniques. Twig version in TiendaNube: 2.x/3.x compatible.

## Code Reuse Comparison

| Technique | Purpose | Scope | Best For |
|-----------|---------|-------|----------|
| `include` | Simple reuse | Inherits parent context | Partials, components |
| `embed` | Include + override blocks | Inherits context | Customizable components |
| `macro` | Function-like reuse | Isolated scope | Repeated markup with params |
| `extends` | Vertical inheritance | Full template | Page layouts |
| `use` | Horizontal inheritance | Block import | Shared blocks across layouts |

## Include

Include another template and render its content. Inherits current context by default.

```twig
{# Basic include #}
{% include 'partials/header.tpl' %}

{# Pass additional variables #}
{% include 'components/product-card.tpl' with { product: item, show_price: true } %}

{# Isolate scope - only passed variables available #}
{% include 'components/product-card.tpl' with { product: item } only %}

{# Conditional include #}
{% include ajax ? 'partials/ajax-content.tpl' : 'partials/full-content.tpl' %}

{# Ignore if missing #}
{% include 'optional/banner.tpl' ignore missing %}

{# Include with fallback #}
{% include ['custom/header.tpl', 'default/header.tpl'] %}
```

## Embed

Combines `include` and `extends`. Include a template and override its blocks.

**Base component (components/card.tpl):**
```twig
<div class="card {{ class|default('') }}">
  <div class="card-header">
    {% block header %}Default Header{% endblock %}
  </div>
  <div class="card-body">
    {% block content %}{% endblock %}
  </div>
  <div class="card-footer">
    {% block footer %}{% endblock %}
  </div>
</div>
```

**Using embed:**
```twig
{% embed 'components/card.tpl' %}
  {% block header %}
    <h3>{{ product.name }}</h3>
  {% endblock %}
  
  {% block content %}
    <p>{{ product.description }}</p>
    <span class="price">{{ product.price | money }}</span>
  {% endblock %}
  
  {% block footer %}
    <button class="js-addtocart">{{ 'Add to cart' | translate }}</button>
  {% endblock %}
{% endembed %}

{# Pass variables to embed #}
{% embed 'components/card.tpl' with { class: 'card-featured' } %}
  {% block content %}Featured product content{% endblock %}
{% endembed %}

{# Isolate scope #}
{% embed 'components/card.tpl' with { product: item } only %}
  {% block content %}{{ product.name }}{% endblock %}
{% endembed %}
```

**Important:** Macros imported outside are NOT available inside embed blocks. Re-import if needed:
```twig
{% import 'macros/helpers.tpl' as helpers %}

{% embed 'components/card.tpl' %}
  {% block content %}
    {# Must re-import inside embed #}
    {% import 'macros/helpers.tpl' as helpers %}
    {{ helpers.icon('cart') }}
  {% endblock %}
{% endembed %}
```

## Macros

Reusable functions that return markup. Have isolated scope - no access to parent context.

**Define macro:**
```twig
{% macro input(name, value, type, placeholder) %}
  <input 
    type="{{ type|default('text') }}" 
    name="{{ name }}" 
    value="{{ value|e }}"
    placeholder="{{ placeholder|default('') }}"
    class="form-control"
  />
{% endmacro %}

{% macro button(text, type, class) %}
  <button type="{{ type|default('button') }}" class="btn {{ class|default('btn-primary') }}">
    {{ text }}
  </button>
{% endmacro %}

{# Macro with HTML content using default filter #}
{% macro alert(message, type) %}
  <div class="alert alert-{{ type|default('info') }}">
    {{ message|raw }}
  </div>
{% endmacro %}
```

**Use macro in same file (_self):**
```twig
{{ _self.input('email', '', 'email', 'Enter email') }}
{{ _self.button('Submit', 'submit', 'btn-success') }}
```

**Import from another file:**
```twig
{# Import all macros #}
{% import 'macros/forms.tpl' as forms %}
{{ forms.input('username', user.name) }}

{# Import specific macros #}
{% from 'macros/forms.tpl' import input, button %}
{{ input('email', '') }}
{{ button('Send') }}

{# Import with alias #}
{% from 'macros/forms.tpl' import input as text_input %}
{{ text_input('name', '') }}
```

**Check if macro exists:**
```twig
{% import 'macros/helpers.tpl' as helpers %}
{% if helpers.icon is defined %}
  {{ helpers.icon('cart') }}
{% endif %}
```

## Template Inheritance (extends)

Child template inherits and overrides parent blocks.

**Base layout (layouts/base.tpl):**
```twig
<!DOCTYPE html>
<html lang="{{ lang }}">
<head>
  <meta charset="UTF-8">
  <title>{% block title %}{{ store.name }}{% endblock %}</title>
  {% block head_css %}
    {{ 'style.css' | static_url | css_tag }}
  {% endblock %}
</head>
<body class="{% block body_class %}{% endblock %}">
  
  {% block header %}
    {% include 'partials/header.tpl' %}
  {% endblock %}
  
  <main>
    {% block content %}{% endblock %}
  </main>
  
  {% block footer %}
    {% include 'partials/footer.tpl' %}
  {% endblock %}
  
  {% block scripts %}
    {{ 'store.js' | static_url | script_tag }}
  {% endblock %}
</body>
</html>
```

**Child template (product.tpl):**
```twig
{% extends 'layouts/base.tpl' %}

{% block title %}{{ product.name }} | {{ parent() }}{% endblock %}

{% block body_class %}page-product{% endblock %}

{% block content %}
  <h1>{{ product.name }}</h1>
  {{ product.description }}
{% endblock %}

{% block scripts %}
  {{ parent() }}
  {{ 'product.js' | static_url | script_tag }}
{% endblock %}
```

**Use parent()** to include parent block content:
```twig
{% block scripts %}
  {{ parent() }}  {# Include parent's scripts #}
  <script>console.log('Additional script');</script>
{% endblock %}
```

**Dynamic extends:**
```twig
{% extends ajax ? 'layouts/ajax.tpl' : 'layouts/base.tpl' %}
```

## Horizontal Reuse (use)

Import blocks from another template without extending it.

**Reusable blocks (blocks/sidebar.tpl):**
```twig
{% block sidebar %}
  <aside class="sidebar">
    {% block sidebar_content %}
      Default sidebar
    {% endblock %}
  </aside>
{% endblock %}
```

**Use in multiple templates:**
```twig
{% extends 'layouts/base.tpl' %}
{% use 'blocks/sidebar.tpl' %}

{% block content %}
  <div class="row">
    <div class="col-8">Main content</div>
    <div class="col-4">{{ block('sidebar') }}</div>
  </div>
{% endblock %}

{# Override imported block #}
{% block sidebar_content %}
  Custom sidebar for this page
{% endblock %}
```

## Capture with set

Capture HTML into a variable for later use.

```twig
{# Capture simple value #}
{% set greeting = 'Hello' %}

{# Capture block of HTML #}
{% set product_html %}
  <div class="product">
    <h2>{{ product.name }}</h2>
    <p>{{ product.price | money }}</p>
  </div>
{% endset %}

{# Use captured content #}
{{ product_html }}

{# Capture for conditional use #}
{% set error_message %}
  {% if errors %}
    <div class="alert alert-danger">
      {% for error in errors %}
        <p>{{ error }}</p>
      {% endfor %}
    </div>
  {% endif %}
{% endset %}

{{ error_message }}  {# Only outputs if errors exist #}
```

## Escaping & Security

### Auto-escaping

Twig auto-escapes by default. Use `raw` to output unescaped HTML.

```twig
{# Auto-escaped (safe) #}
{{ user_input }}  {# <script> becomes &lt;script&gt; #}

{# Raw output (trust the content) #}
{{ product.description | raw }}

{# Explicit escape strategies #}
{{ data | escape }}           {# HTML (default) #}
{{ data | e }}                {# Shortcut #}
{{ data | escape('html') }}   {# Explicit HTML #}
{{ data | escape('js') }}     {# JavaScript context #}
{{ data | escape('css') }}    {# CSS context #}
{{ data | escape('url') }}    {# URL parameter #}
{{ data | escape('html_attr') }} {# HTML attribute #}
```

### Autoescape blocks

```twig
{# Disable auto-escape for a block #}
{% autoescape false %}
  {{ trusted_html }}
{% endautoescape %}

{# Force specific escape strategy #}
{% autoescape 'js' %}
  var data = {{ json_data }};
{% endautoescape %}
```

### Safe output in macros

```twig
{% macro render_list(items) %}
  <ul>
    {% for item in items %}
      {# Using raw here means caller is responsible for escaping #}
      <li>{{ item | raw }}</li>
    {% endfor %}
  </ul>
{% endmacro %}

{# Safe usage - pre-escape user content #}
{% set safe_items = [] %}
{% for item in user_items %}
  {% set safe_items = safe_items | merge([item | e ~ ' <em>safe</em>']) %}
{% endfor %}
{{ _self.render_list(safe_items) }}
```

## Whitespace Control

Use `-` modifier to trim whitespace.

```twig
{# Trim left whitespace #}
{%- if condition %}

{# Trim right whitespace #}
{% if condition -%}

{# Trim both sides #}
{%- if condition -%}

{# Same for output tags #}
{{- variable -}}
```

**Example - compact output:**
```twig
<ul>
  {%- for item in items -%}
    <li>{{- item.name -}}</li>
  {%- endfor -%}
</ul>
{# Output: <ul><li>Item1</li><li>Item2</li></ul> #}
```

### Spaceless filter

Remove whitespace between HTML tags:

```twig
{% apply spaceless %}
  <nav>
    <a href="/">Home</a>
    <a href="/products">Products</a>
  </nav>
{% endapply %}
{# Output: <nav><a href="/">Home</a><a href="/products">Products</a></nav> #}
```

## Arrow Functions & Collection Filters

Modern Twig supports arrow functions for filtering and transforming collections.

### filter

Remove elements that don't match condition:

```twig
{# Filter by condition #}
{% set available = products | filter(p => p.stock > 0) %}

{# Filter with key and value #}
{% set filtered = items | filter((v, k) => v.active and k != 'deprecated') %}

{# In loops #}
{% for product in products | filter(p => p.featured) %}
  {{ product.name }}
{% endfor %}
```

### map

Transform each element:

```twig
{# Extract property #}
{% set names = products | map(p => p.name) %}

{# Transform values #}
{% set prices = products | map(p => p.price * 1.21) %}

{# With key #}
{% set formatted = items | map((v, k) => k ~ ': ' ~ v) %}
```

### reduce

Reduce collection to single value:

```twig
{# Sum prices #}
{% set total = products | reduce((carry, p) => carry + p.price, 0) %}

{# Concatenate #}
{% set all_names = products | reduce((carry, p) => carry ~ ', ' ~ p.name, '') %}
```

### sort

Sort collection:

```twig
{# Default sort #}
{% set sorted = items | sort %}

{# Custom sort with arrow function #}
{% set by_price = products | sort((a, b) => a.price <=> b.price) %}

{# Reverse sort #}
{% set by_price_desc = products | sort((a, b) => b.price <=> a.price) %}
```

### column

Extract column from array of arrays/objects:

```twig
{% set names = products | column('name') %}
{# ['Product A', 'Product B', ...] #}
```

### batch

Group into batches:

```twig
{% for row in products | batch(3) %}
  <div class="row">
    {% for product in row %}
      <div class="col-4">{{ product.name }}</div>
    {% endfor %}
  </div>
{% endfor %}
```

## Block Function

Access block content programmatically:

```twig
{# Check if block is defined #}
{% if block('sidebar') is defined %}
  {{ block('sidebar') }}
{% endif %}

{# Conditional title #}
<title>
  {%- if block('title') is defined -%}
    {{ block('title') }} | 
  {%- endif -%}
  {{ store.name }}
</title>

{# Render block from another template #}
{{ block('content', 'other-template.tpl') }}
```

## Verbatim (Raw Text)

Output Twig syntax as literal text:

```twig
{% verbatim %}
  This {{ will not }} be parsed as Twig.
  {% This is literal text %}
{% endverbatim %}

{# Useful for JavaScript templates #}
{% verbatim %}
  <script type="text/template" id="vue-template">
    <div>{{ message }}</div>
  </script>
{% endverbatim %}
```

## apply Tag

Apply filters to a block of content:

```twig
{% apply upper %}
  This text will be uppercase
{% endapply %}

{% apply spaceless %}
  <div>
    <span>Compact</span>
  </div>
{% endapply %}

{# Chain filters #}
{% apply lower | escape %}
  {{ user_content }}
{% endapply %}
```

## Common Patterns for TiendaNube

### Reusable Product Card

**macros/product.tpl:**
```twig
{% macro card(product, show_variants) %}
  <div class="product-card" data-store="product-item-{{ product.id }}">
    <a href="{{ product.url }}">
      <img src="{{ product.featured_image | product_image_url('medium') }}" 
           alt="{{ product.name }}" 
           loading="lazy">
    </a>
    <h3>{{ product.name }}</h3>
    <span class="price">{{ product.price | money }}</span>
    
    {% if show_variants|default(false) and product.has_variants %}
      <div class="variants">
        {% for variant in product.variants %}
          <span>{{ variant.name }}</span>
        {% endfor %}
      </div>
    {% endif %}
  </div>
{% endmacro %}
```

### Form Components with Embed

**components/form-group.tpl:**
```twig
<div class="form-group {{ error ? 'has-error' : '' }}">
  <label for="{{ id }}">{% block label %}{% endblock %}</label>
  {% block input %}{% endblock %}
  {% if error %}
    <span class="error-message">{{ error }}</span>
  {% endif %}
</div>
```

**Usage:**
```twig
{% embed 'components/form-group.tpl' with { id: 'email', error: errors.email|default('') } %}
  {% block label %}{{ 'Email' | translate }}{% endblock %}
  {% block input %}
    <input type="email" id="email" name="email" value="{{ customer.email }}" class="form-control">
  {% endblock %}
{% endembed %}
```

### Conditional Layout Sections

```twig
{# Capture potential empty content #}
{% set sidebar_content %}
  {% if category.children %}
    {% include 'partials/category-nav.tpl' %}
  {% endif %}
{% endset %}

<div class="row">
  <div class="{{ sidebar_content | trim ? 'col-9' : 'col-12' }}">
    {% block content %}{% endblock %}
  </div>
  
  {% if sidebar_content | trim %}
    <div class="col-3">
      {{ sidebar_content }}
    </div>
  {% endif %}
</div>
```

## String Filters

### replace

Replace substrings in a string. TiendaNube uses chained single-replacement syntax:

```twig
{# TiendaNube syntax - chain multiple replace calls #}
{% set clean_phone = phone | replace(' ', '') | replace('-', '') | replace('(', '') | replace(')', '') %}

{# Single replacement #}
{{ "Hello World" | replace('World', 'Twig') }}
{# Output: Hello Twig #}

{# Remove characters (replace with empty string) #}
{{ "+54 911 1234-5678" | replace(' ', '') | replace('-', '') }}
{# Output: +5491112345678 #}
```

**Important:** The dictionary syntax `replace({'from': 'to'})` from standard Twig documentation may not work in TiendaNube. Always use chained single replacements.

### Other String Filters

```twig
{# Trim whitespace #}
{{ "  hello  " | trim }}
{# Output: hello #}

{# Lowercase / Uppercase #}
{{ "Hello" | lower }}  {# hello #}
{{ "Hello" | upper }}  {# HELLO #}

{# Capitalize first letter #}
{{ "hello world" | capitalize }}  {# Hello world #}

{# Title case #}
{{ "hello world" | title }}  {# Hello World #}

{# Split string to array #}
{% set parts = "a,b,c" | split(',') %}
{# ['a', 'b', 'c'] #}

{# Slice substring #}
{{ "Hello" | slice(0, 2) }}  {# He #}

{# Length #}
{{ "Hello" | length }}  {# 5 #}

{# Default value if empty #}
{{ "" | default('N/A') }}  {# N/A #}

{# Format string #}
{{ "Hello %s" | format(name) }}
```

## Best Practices

1. **Use `include` for simple partials** without customization
2. **Use `embed` for components** that need block overrides
3. **Use `macro` for repeated patterns** with different data
4. **Use `extends` for page layouts** with vertical inheritance
5. **Use `use` for shared blocks** across different layouts
6. **Always escape user input** - only use `raw` for trusted content
7. **Keep macros in separate files** for reusability
8. **Use `only` keyword** when you want to isolate scope
9. **Prefer `filter` over `for...if`** for cleaner loops
10. **Use whitespace control** for inline elements (nav, breadcrumbs)
