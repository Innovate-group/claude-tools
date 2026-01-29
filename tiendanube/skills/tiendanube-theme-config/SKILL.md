---
name: tiendanube-theme-config
description: Expert skill for TiendaNube/Nuvemshop theme configuration files. Use when creating or modifying settings.txt, defaults.txt, translations.txt, sections.txt, or data.json files in the config/ directory. Enables theme customization through the admin panel including colors, fonts, images, checkboxes, dropdowns, galleries, and multi-language support.
---

# TiendaNube Theme Configuration

## Overview

This skill provides expertise for managing the `config/` directory in TiendaNube themes. These files control how merchants customize their store's appearance through the admin panel "Personalizar el diseño actual".

## Config Directory Structure

```
config/
├── settings.txt      # UI components for customization panel
├── defaults.txt      # Default values for settings
├── translations.txt  # Multi-language text strings
├── sections.txt      # Product sections (featured, new, sale, etc.)
└── data.json         # Real-time color preview connection
```

## sections.txt - Product Sections

This file defines **product groupings** that merchants can manage from the admin panel.

### Purpose
- Creates tabs in `Mi Tiendanube > Organizar productos`
- Merchants assign products to each section
- Sections can be displayed anywhere in the theme (home, sidebars, etc.)

### Syntax

```
section_key
    name = Section Display Name

another_section
    name = Another Section Name
```

### Example sections.txt

```
featured
    name = Productos destacados

new
    name = Productos nuevos

sale
    name = Productos en oferta

best_sellers
    name = Más vendidos
```

### Admin Panel Result

Each section appears as a tab in `Mi Tiendanube > Organizar productos`:

```
┌─────────────────┬─────────────────┬─────────────────┬─────────────────┐
│ Prod. destacados│ Productos nuevos│ Prod. en oferta │  Más vendidos   │
└─────────────────┴─────────────────┴─────────────────┴─────────────────┘
```

### Using Sections in Templates

Each section generates a variable with the products:

```twig
{# Featured products - from "featured" section #}
{% for product in featured_products %}
  {% include 'snipplets/product-card.tpl' with { product: product } %}
{% endfor %}

{# New products - from "new" section #}
{% for product in new_products %}
  {% include 'snipplets/product-card.tpl' with { product: product } %}
{% endfor %}

{# Sale products - from "sale" section #}
{% for product in sale_products %}
  {% include 'snipplets/product-card.tpl' with { product: product } %}
{% endfor %}

{# Best sellers - from "best_sellers" section #}
{% for product in best_sellers_products %}
  {% include 'snipplets/product-card.tpl' with { product: product } %}
{% endfor %}
```

### Variable Naming Convention

Section key → Variable name:
- `featured` → `featured_products`
- `new` → `new_products`
- `sale` → `sale_products`
- `best_sellers` → `best_sellers_products`

Pattern: `{section_key}_products`

### Combining with Settings

Control section visibility and display options:

**settings.txt:**
```
	collapse
		title = Productos destacados
	checkbox
		name = home_featured_show
		description = Mostrar sección de productos destacados
	dropdown
		name = home_featured_count
		description = Cantidad de productos
		values
			4 = 4 productos
			8 = 8 productos
			12 = 12 productos
```

**defaults.txt:**
```
home_featured_show = true
home_featured_count = 8
```

**Template:**
```twig
{% if settings.home_featured_show and featured_products %}
  <section class="home-featured" data-store="home-products-featured">
    <div class="container">
      <h2>{{ 'Productos destacados' | translate }}</h2>
      <div class="row">
        {% for product in featured_products | slice(0, settings.home_featured_count) %}
          <div class="col-6 col-md-3">
            {% include 'snipplets/product-card.tpl' with { product: product } %}
          </div>
        {% endfor %}
      </div>
    </div>
  </section>
{% endif %}
```

### section_order in settings.txt

To let merchants **reorder sections** on the homepage via drag & drop:

**settings.txt:**
```
	section_order
		name = home_order_position
		start_index = 0
		sections
			slider = Carrusel de imágenes
			welcome = Mensaje institucional
			products = Productos destacados
			informatives = Información de envíos
			categories = Banners de categorías
			video = Video
			instafeed = Instagram
```

**home.tpl:**
```twig
{% for section in ['home_order_position_0', 'home_order_position_1', 'home_order_position_2', 'home_order_position_3', 'home_order_position_4', 'home_order_position_5', 'home_order_position_6'] %}
  {% set section_select = attribute(settings, section) %}
  
  {% if section_select == 'products' %}
    {% include 'snipplets/home/home-featured-products.tpl' %}
  {% elseif section_select == 'slider' %}
    {% include 'snipplets/home/home-slider.tpl' %}
  {% elseif section_select == 'welcome' %}
    {% include 'snipplets/home/home-welcome.tpl' %}
  {% elseif section_select == 'categories' %}
    {% include 'snipplets/home/home-categories.tpl' %}
  {% elseif section_select == 'video' %}
    {% include 'snipplets/home/home-video.tpl' %}
  {% elseif section_select == 'instafeed' %}
    {% include 'snipplets/home/home-instagram.tpl' %}
  {% endif %}
{% endfor %}
```

## File Relationships

```
settings.txt  ──defines──>  Admin UI Components
      │                           │
      │                     uses "name" property
      │                           │
      ▼                           ▼
defaults.txt  ──provides──>  Default Values
      │                           │
      │                     {{ settings.name }}
      │                           │
      ▼                           ▼
Templates (.tpl)  <──reads──  Configuration Values
      │
      │  uses text strings
      ▼
translations.txt  ──provides──>  Multi-language Support
```

## Quick Reference

**Adding a new setting:**
1. Add component in `settings.txt`
2. Set default value in `defaults.txt`
3. Add translations in `translations.txt` (if needed)
4. Use `{{ settings.name }}` in templates

**Component types available:**
- `section` - Main section with icon
- `collapse` - Accordion container
- `title` / `subtitle` / `description` - Text elements
- `checkbox` - Boolean toggle
- `dropdown` - Select options
- `color` - Color picker
- `font` - Font selector
- `image` - Single image upload
- `gallery` - Multiple images (NEVER use digits in name, use textual: one, two, three...)
- `i18n_input` - Multi-language text input
- `textarea` - Large text area
- `css_code` - CSS editor with validation
- `menu` - Menu selector
- `text` - Simple text input
- `palettes` - Predefined color combinations
- `section_order` - Drag & drop section reordering
- `link` - Link to another section in settings

For detailed syntax: See `references/settings-components.md`

## Critical Rules

### Indentation
**ALWAYS use TABS, never spaces** for indentation in settings.txt:
```
Section Name
	meta
		icon = home
	checkbox
		name = feature_enabled
		description = Enable feature
```

### Naming Convention
Use `snake_case` for all `name` properties:
```
name = show_featured_products    ✓
name = showFeaturedProducts      ✗
name = show-featured-products    ✗
```

### Gallery Names - No Numeric Digits
**CRITICAL:** Never use numeric digits (0-9) in `gallery` component `name` values. TiendaNube has a bug that prevents all slides from displaying when the gallery name contains numbers.

Use textual numbers in English instead:
```
# WRONG - will cause display issues
gallery
	name = slider_01
	name = banner_2

# CORRECT - use textual numbers
gallery
	name = slider_one
	name = banner_two
```

Textual numbers: one, two, three, four, five, six, seven, eight, nine, ten

### Translation Matching
Text in templates MUST match translations.txt exactly (case-sensitive):
```twig
{{ 'Agregar al carrito' | translate }}
```
```
es "Agregar al carrito"
pt "Adicionar ao carrinho"
en "Add to cart"
es_mx "Agregar al carrito"
```

### Defaults Cross-Reference
Every `name` in settings.txt that needs a default MUST exist in defaults.txt:
```
# settings.txt
checkbox
	name = show_slider

# defaults.txt
show_slider = true
```

## Required Patterns (MANDATORY)

When creating component settings, these patterns are **REQUIRED**:

### 1. Responsive Images (Desktop + Mobile)

**ALWAYS create separate image settings for desktop and mobile when a component uses images.**

**settings.txt:**
```
	subtitle
		subtitle = Imagen para escritorio
	image
		original = banner_01_desktop.jpg
		title = Cargar imagen (1920x600 recomendado)
		width = 1920
		height = 600
	subtitle
		subtitle = Imagen para celulares
	image
		original = banner_01_mobile.jpg
		title = Cargar imagen (768x900 recomendado)
		width = 768
		height = 900
```

**Template usage:**
```twig
{# Desktop image #}
{% if store.has_theme_image('banner_01_desktop.jpg') %}
  <img 
    src="{{ 'banner_01_desktop.jpg' | static_url }}" 
    alt="Banner"
    class="d-none d-md-block img-fluid"
  >
{% endif %}

{# Mobile image #}
{% if store.has_theme_image('banner_01_mobile.jpg') %}
  <img 
    src="{{ 'banner_01_mobile.jpg' | static_url }}" 
    alt="Banner"
    class="d-block d-md-none img-fluid"
  >
{% endif %}
```

**Alternative with `<picture>` element:**
```twig
<picture>
  {% if store.has_theme_image('banner_01_mobile.jpg') %}
    <source media="(max-width: 767px)" srcset="{{ 'banner_01_mobile.jpg' | static_url }}">
  {% endif %}
  {% if store.has_theme_image('banner_01_desktop.jpg') %}
    <img src="{{ 'banner_01_desktop.jpg' | static_url }}" alt="Banner" class="img-fluid">
  {% endif %}
</picture>
```

**Recommended dimensions:**

| Type | Desktop | Mobile |
|------|---------|--------|
| Hero/Slider | 1920x600 | 768x900 |
| Banner full | 1920x400 | 768x600 |
| Banner half | 960x400 | 768x400 |
| Category | 600x400 | 768x400 |

### 2. Spacing Controls (Margin & Padding)

**ALWAYS add margin and padding settings for each component section.**

**settings.txt:**
```
	collapse
		title = Banner promocional
	checkbox
		name = banner_promo_show
		description = Mostrar banner promocional
	image
		original = banner_promo_desktop.jpg
		title = Imagen escritorio
		width = 1920
		height = 400
	image
		original = banner_promo_mobile.jpg
		title = Imagen celulares
		width = 768
		height = 600
	title
		title = Espaciado
	dropdown
		name = banner_promo_margin_top
		description = Margen superior
		values
			none = Sin margen
			small = Pequeño
			medium = Mediano
			large = Grande
	dropdown
		name = banner_promo_margin_bottom
		description = Margen inferior
		values
			none = Sin margen
			small = Pequeño
			medium = Mediano
			large = Grande
	dropdown
		name = banner_promo_padding_top
		description = Padding superior
		values
			none = Sin padding
			small = Pequeño
			medium = Mediano
			large = Grande
	dropdown
		name = banner_promo_padding_bottom
		description = Padding inferior
		values
			none = Sin padding
			small = Pequeño
			medium = Mediano
			large = Grande
```

**defaults.txt:**
```
banner_promo_show = true
banner_promo_margin_top = none
banner_promo_margin_bottom = medium
banner_promo_padding_top = medium
banner_promo_padding_bottom = medium
```

**CSS classes (style.scss.tpl):**
```scss
// Spacing utilities for sections
$spacing-values: (
  'none': 0,
  'small': 1rem,
  'medium': 2rem,
  'large': 4rem
);

@each $name, $value in $spacing-values {
  .section-mt-#{$name} { margin-top: $value; }
  .section-mb-#{$name} { margin-bottom: $value; }
  .section-pt-#{$name} { padding-top: $value; }
  .section-pb-#{$name} { padding-bottom: $value; }
}

// Responsive adjustments
@media (max-width: 767px) {
  .section-mt-large { margin-top: 2rem; }
  .section-mb-large { margin-bottom: 2rem; }
  .section-pt-large { padding-top: 2rem; }
  .section-pb-large { padding-bottom: 2rem; }
}
```

**Template usage:**
```twig
{% set spacing_classes = [
  'section-mt-' ~ settings.banner_promo_margin_top,
  'section-mb-' ~ settings.banner_promo_margin_bottom,
  'section-pt-' ~ settings.banner_promo_padding_top,
  'section-pb-' ~ settings.banner_promo_padding_bottom
] | join(' ') %}

<section class="banner-promo {{ spacing_classes }}" data-store="banner-promo">
  {# Content #}
</section>
```

### 3. Complete Component Template

When creating a new component with images, **always include**:

1. ✅ Show/hide checkbox
2. ✅ Desktop image
3. ✅ Mobile image  
4. ✅ Alt text (i18n_input)
5. ✅ Link (text input)
6. ✅ Margin top/bottom
7. ✅ Padding top/bottom

**Full example - settings.txt:**
```
	collapse
		title = Banner Hero
	checkbox
		name = hero_show
		description = Mostrar banner hero
	subtitle
		subtitle = Imagen de escritorio
	image
		original = hero_desktop.jpg
		title = Cargar imagen (1920x600 recomendado)
		width = 1920
		height = 600
	subtitle
		subtitle = Imagen de celulares
	image
		original = hero_mobile.jpg
		title = Cargar imagen (768x900 recomendado)
		width = 768
		height = 900
	i18n_input
		name = hero_alt
		description = Texto alternativo de la imagen (SEO)
	text
		name = hero_link
		description = Link del banner (opcional)
		placeholder = https://ejemplo.com/promocion
	title
		title = Espaciado
	dropdown
		name = hero_margin_top
		description = Margen superior
		values
			none = Sin margen
			small = Pequeño
			medium = Mediano
			large = Grande
	dropdown
		name = hero_margin_bottom
		description = Margen inferior
		values
			none = Sin margen
			small = Pequeño
			medium = Mediano
			large = Grande
	dropdown
		name = hero_padding_top
		description = Padding superior
		values
			none = Sin padding
			small = Pequeño
			medium = Mediano
			large = Grande
	dropdown
		name = hero_padding_bottom
		description = Padding inferior
		values
			none = Sin padding
			small = Pequeño
			medium = Mediano
			large = Grande
```

**Full example - defaults.txt:**
```
hero_show = true
hero_alt_es = Banner promocional
hero_alt_pt = Banner promocional
hero_alt_en = Promotional banner
hero_alt_es_mx = Banner promocional
hero_link = 
hero_margin_top = none
hero_margin_bottom = none
hero_padding_top = none
hero_padding_bottom = none
```

**Full example - template:**
```twig
{% if settings.hero_show %}
  {% set spacing_classes = [
    'section-mt-' ~ settings.hero_margin_top,
    'section-mb-' ~ settings.hero_margin_bottom,
    'section-pt-' ~ settings.hero_padding_top,
    'section-pb-' ~ settings.hero_padding_bottom
  ] | join(' ') %}
  
  {% set has_desktop = store.has_theme_image('hero_desktop.jpg') %}
  {% set has_mobile = store.has_theme_image('hero_mobile.jpg') %}
  
  {% if has_desktop or has_mobile %}
  <section class="hero-banner {{ spacing_classes }}" data-store="home-hero">
    {% if settings.hero_link %}
      <a href="{{ settings.hero_link }}">
    {% endif %}
    
    <picture>
      {% if has_mobile %}
        <source media="(max-width: 767px)" srcset="{{ 'hero_mobile.jpg' | static_url }}">
      {% endif %}
      {% if has_desktop %}
        <img 
          src="{{ 'hero_desktop.jpg' | static_url }}" 
          alt="{{ settings.hero_alt }}"
          class="img-fluid w-100"
          loading="lazy"
        >
      {% endif %}
    </picture>
    
    {% if settings.hero_link %}
      </a>
    {% endif %}
  </section>
  {% endif %}
{% endif %}
```

## Common Workflows

### Adding a Checkbox Feature

**1. settings.txt:**
```
	checkbox
		name = show_announcement_bar
		description = Mostrar barra de anuncios
```

**2. defaults.txt:**
```
show_announcement_bar = true
```

**3. Template usage:**
```twig
{% if settings.show_announcement_bar %}
  {% include 'snipplets/announcement-bar.tpl' %}
{% endif %}
```

### Adding a Color Setting

**1. settings.txt:**
```
	color
		name = accent_color
		description = Color de acento
```

**2. defaults.txt:**
```
accent_color = #FF5500
```

**3. style-colors.scss.tpl:**
```scss
$accent-color: {{ settings.accent_color }};

.btn-accent {
  background-color: $accent-color;
}
```

### Adding Multi-language Text

**1. settings.txt:**
```
	i18n_input
		name = promo_message
		description = Mensaje promocional
```

**2. defaults.txt (per language):**
```
promo_message_es = ¡Envío gratis en compras mayores a $5000!
promo_message_pt = Frete grátis em compras acima de R$200!
promo_message_en = Free shipping on orders over $50!
promo_message_es_mx = ¡Envío gratis en compras mayores a $1000!
```

**3. Template usage:**
```twig
<p class="promo">{{ settings.promo_message }}</p>
```

### Adding an Image Banner (Responsive)

**1. settings.txt:**
```
	subtitle
		subtitle = Imagen de escritorio
	image
		original = banner_home_desktop.jpg
		title = Cargar imagen (1920x400 recomendado)
		width = 1920
		height = 400
	subtitle
		subtitle = Imagen de celulares
	image
		original = banner_home_mobile.jpg
		title = Cargar imagen (768x600 recomendado)
		width = 768
		height = 600
	i18n_input
		name = banner_home_alt
		description = Texto alternativo (SEO)
```

**2. defaults.txt:**
```
banner_home_alt_es = Banner principal
banner_home_alt_pt = Banner principal
banner_home_alt_en = Main banner
banner_home_alt_es_mx = Banner principal
```

**3. Template usage:**
```twig
{% set has_desktop = store.has_theme_image('banner_home_desktop.jpg') %}
{% set has_mobile = store.has_theme_image('banner_home_mobile.jpg') %}

{% if has_desktop or has_mobile %}
<picture>
  {% if has_mobile %}
    <source media="(max-width: 767px)" srcset="{{ 'banner_home_mobile.jpg' | static_url }}">
  {% endif %}
  {% if has_desktop %}
    <img 
      src="{{ 'banner_home_desktop.jpg' | static_url }}" 
      alt="{{ settings.banner_home_alt }}"
      class="img-fluid w-100"
      loading="lazy"
    >
  {% endif %}
</picture>
{% endif %}
```

### Adding a Dropdown

**1. settings.txt:**
```
	dropdown
		name = header_style
		description = Estilo del encabezado
		values
			classic = Clásico
			modern = Moderno
			minimal = Minimalista
```

**2. defaults.txt:**
```
header_style = classic
```

**3. Template usage:**
```twig
{% if settings.header_style == 'modern' %}
  {% include 'snipplets/header/header-modern.tpl' %}
{% else %}
  {% include 'snipplets/header/header-classic.tpl' %}
{% endif %}
```

## Validation Checklist

Before deploying config changes:

**Syntax:**
- [ ] All indentation uses TABS (not spaces)
- [ ] No duplicate `name` values in settings.txt
- [ ] All settings with defaults have entries in defaults.txt
- [ ] All 4 languages present in translations.txt (es, pt, en, es_mx)
- [ ] Translation strings match exactly in templates
- [ ] data.json references correct SCSS file name
- [ ] Gallery `name` values contain NO numeric digits (use one, two, three instead of 1, 2, 3)

**Required Patterns:**
- [ ] Components with images have BOTH desktop and mobile versions
- [ ] All section components have margin/padding settings
- [ ] Image dimensions are reasonable for web
- [ ] Alt text (i18n_input) provided for all images
- [ ] Spacing CSS classes exist in stylesheet

## Resources

- `references/settings-components.md` - Complete component syntax reference
- `references/translations-guide.md` - Translation file patterns
- `references/sections-guide.md` - Product sections and section_order guide
