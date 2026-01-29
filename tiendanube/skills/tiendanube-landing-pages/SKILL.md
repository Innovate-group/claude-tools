---
name: tiendanube-landing-pages
description: Expert skill for creating custom landing pages in TiendaNube/Nuvemshop themes. Use when creating page-specific templates, since TiendaNube doesn't support native page templates. Implements the handle-based routing pattern in page.tpl with configurable handles from settings.txt to load custom snipplets per page. Supports reorderable sections via section_order.
---

# TiendaNube Landing Pages

## Overview

TiendaNube **does not support** native page templates (unlike Shopify's `page.custom.liquid`). All pages use the same `templates/page.tpl` template.

**Solution:** Implement handle-based routing in `page.tpl` that checks the page handle against a **configurable handle from settings.txt** and loads the corresponding snipplet. Using `i18n_input` allows different handles per language.

**Key Feature:** Landing pages support **reorderable sections** using TiendaNube's `section_order` component, allowing store owners to drag-and-drop sections in any order from the admin panel.

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     Admin Panel                              │
│  Mi Tiendanube > Contenido > Páginas > [Nueva Página]       │
│  - Título: "Quiénes Somos"                                  │
│  - URL: /quienes-somos  ← This becomes the HANDLE           │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│              Personalizar Tema > Settings                    │
│                                                              │
│  settings.page_about_handle = "quienes-somos"               │
│  (configurable via i18n_input for multi-language support)   │
│                                                              │
│  section_order: Drag & drop to reorder sections             │
│  ┌─────────────────────────────────────────┐                │
│  │ ☰ Hero / Banner principal               │                │
│  │ ☰ Contenido principal                   │                │
│  │ ☰ Sección de equipo                     │                │
│  │ ☰ Call to Action                        │                │
│  └─────────────────────────────────────────┘                │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                   templates/page.tpl                         │
│                                                              │
│  {% if settings.page_about_handle and                       │
│        page.handle == settings.page_about_handle %}         │
│    {% include 'snipplets/pages/page-about.tpl' %}           │
│  {% else %}                                                  │
│    {# Default page content #}                                │
│    {{ page.content | raw }}                                  │
│  {% endif %}                                                 │
│                                                              │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│              snipplets/pages/page-about.tpl                  │
│                                                              │
│  {# Section order loop - renders sections in admin order #} │
│  {% for i in 1..6 %}                                        │
│    {% set section = 'page_about_order_' ~ i %}              │
│    {% set section_select = attribute(settings, section) %}  │
│    {% include 'snipplets/pages/page-about-switch.tpl' %}    │
│  {% endfor %}                                               │
└─────────────────────────────────────────────────────────────┘
```

## File Structure

```
templates/
├── page.tpl                        # Router - handles all pages
└── snipplets/
    └── pages/
        ├── page-default.tpl        # Default page layout (optional)
        ├── page-about.tpl          # Landing page container with section loop
        ├── page-about-switch.tpl   # Section switch for about page
        ├── page-about/             # Individual sections folder
        │   ├── section-hero.tpl
        │   ├── section-content.tpl
        │   ├── section-team.tpl
        │   └── section-cta.tpl
        └── page-[handle].tpl       # Pattern for other landing pages

config/
├── settings.txt                    # Landing page settings with section_order
├── defaults.txt                    # Default values including section order
└── translations.txt                # Translations
```

## Reorderable Sections Pattern

### How section_order Works

The `section_order` component in settings.txt creates a drag-and-drop interface in the admin panel. It generates position variables like `page_about_order_1`, `page_about_order_2`, etc., which contain the section key for that position.

### Settings Structure (settings.txt)

```
Página Quiénes Somos
	meta
		icon = users
		advanced = true
		default = page_about_order
	collapse
		title = Configuración de la página
		backto = page_about_order
	i18n_input
		name = page_about_handle
		description = Handle (URL) de la página. Configurable por idioma.
	collapse
		title = Hero / Banner principal
		backto = page_about_order
	{# Hero section settings here... #}
	collapse
		title = Contenido principal
		backto = page_about_order
	{# Content section settings here... #}
	collapse
		title = Sección de equipo
		backto = page_about_order
	{# Team section settings here... #}
	collapse
		title = Call to Action
		backto = page_about_order
	{# CTA section settings here... #}
	collapse
		title = Página Quiénes Somos
		backto = _top
	section_order
		name = page_about_order
		start_index = 1
		sections
			hero = Hero / Banner principal
			content = Contenido principal
			team = Sección de equipo
			cta = Call to Action
```

**Key elements:**
- `meta.default = page_about_order` - Opens section_order by default
- `collapse.backto = page_about_order` - Links section back to the order panel
- `section_order.name` - Base name for position variables
- `section_order.sections` - Map of section keys to display names

### Defaults (defaults.txt)

```
page_about_handle = quienes-somos
page_about_order_1 = hero
page_about_order_2 = content
page_about_order_3 = team
page_about_order_4 = cta
page_about_order_5 = empty
page_about_order_6 = empty
```

### Landing Page Template (page-about.tpl)

```twig
{#
  Landing Page: Quiénes Somos / About Us
  Handle: configurable via settings.page_about_handle
  Settings prefix: page_about_
  Supports reorderable sections via section_order
#}

{% set page_spacing_classes = [
  'section-mt-' ~ settings.page_about_margin_top,
  'section-mb-' ~ settings.page_about_margin_bottom,
  'section-pt-' ~ settings.page_about_padding_top,
  'section-pb-' ~ settings.page_about_padding_bottom
] | join(' ') %}

<article class="landing-page page-about {{ page_spacing_classes }}" data-store="page-about">

  {# ======================= #}
  {#   SECTION ORDER LOOP    #}
  {# ======================= #}

  {% set rendered_sections = [] %}

  {% for i in 1..6 %}
    {% set section = 'page_about_order_' ~ i %}
    {% set section_select = attribute(settings, section) %}

    {# Avoid rendering the same section twice #}
    {% if section_select and section_select != 'empty' and section_select not in rendered_sections %}
      {% include 'snipplets/pages/page-about-switch.tpl' %}
      {% set rendered_sections = rendered_sections | merge([section_select]) %}
    {% endif %}
  {% endfor %}

</article>
```

### Section Switch (page-about-switch.tpl)

```twig
{# Section switch for About page - renders section based on section_select variable #}

{% if section_select == 'hero' %}

  {# ===== HERO SECTION ===== #}
  {% set has_hero_desktop = 'page_about_hero_desktop.jpg' | has_custom_image %}
  {% set has_hero_mobile = 'page_about_hero_mobile.jpg' | has_custom_image %}

  {% if has_hero_desktop or has_hero_mobile or settings.page_about_hero_title %}
    {% set hero_spacing = 'section-mt-' ~ settings.page_about_hero_margin_top ~ ' section-mb-' ~ settings.page_about_hero_margin_bottom ~ ' section-pt-' ~ settings.page_about_hero_padding_top ~ ' section-pb-' ~ settings.page_about_hero_padding_bottom %}
    <section class="page-hero {{ hero_spacing }}" data-store="page-about-hero">
      {% include 'snipplets/pages/page-about/section-hero.tpl' %}
    </section>
  {% endif %}

{% elseif section_select == 'content' %}

  {# ===== CONTENT SECTION ===== #}
  {% set content_spacing = 'section-mt-' ~ settings.page_about_content_margin_top ~ ' section-mb-' ~ settings.page_about_content_margin_bottom ~ ' section-pt-' ~ settings.page_about_content_padding_top ~ ' section-pb-' ~ settings.page_about_content_padding_bottom %}
  <section class="page-content {{ content_spacing }}" data-store="page-about-content">
    {% include 'snipplets/pages/page-about/section-content.tpl' %}
  </section>

{% elseif section_select == 'team' %}

  {# ===== TEAM SECTION ===== #}
  {% if settings.page_about_show_team %}
    {% set team_spacing = 'section-mt-' ~ settings.page_about_team_margin_top ~ ' section-mb-' ~ settings.page_about_team_margin_bottom ~ ' section-pt-' ~ settings.page_about_team_padding_top ~ ' section-pb-' ~ settings.page_about_team_padding_bottom %}
    <section class="page-team {{ team_spacing }}" data-store="page-about-team">
      {% include 'snipplets/pages/page-about/section-team.tpl' %}
    </section>
  {% endif %}

{% elseif section_select == 'cta' %}

  {# ===== CTA SECTION ===== #}
  {% if settings.page_about_cta_title or settings.page_about_cta_button %}
    {% set cta_spacing = 'section-mt-' ~ settings.page_about_cta_margin_top ~ ' section-mb-' ~ settings.page_about_cta_margin_bottom ~ ' section-pt-' ~ settings.page_about_cta_padding_top ~ ' section-pb-' ~ settings.page_about_cta_padding_bottom %}
    <section class="page-cta {{ cta_spacing }}" data-store="page-about-cta">
      {% include 'snipplets/pages/page-about/section-cta.tpl' %}
    </section>
  {% endif %}

{% endif %}
```

## Complete Example: About Page with Reorderable Sections

### 1. Settings (settings.txt)

```
Página Quiénes Somos
	meta
		icon = users
		advanced = true
		default = page_about_order
	collapse
		title = Configuración de la página
		backto = page_about_order
	i18n_input
		name = page_about_handle
		description = Handle (URL) de la página. Configurable por idioma (ej: ES: quienes-somos, EN: about-us).
	collapse
		title = Espaciado general
		backto = page_about_order
	dropdown
		name = page_about_margin_top
		description = Margen superior
		values
			none = Sin margen
			small = Pequeño
			medium = Mediano
			large = Grande
	dropdown
		name = page_about_margin_bottom
		description = Margen inferior
		values
			none = Sin margen
			small = Pequeño
			medium = Mediano
			large = Grande
	dropdown
		name = page_about_padding_top
		description = Padding superior
		values
			none = Sin padding
			small = Pequeño
			medium = Mediano
			large = Grande
	dropdown
		name = page_about_padding_bottom
		description = Padding inferior
		values
			none = Sin padding
			small = Pequeño
			medium = Mediano
			large = Grande
	collapse
		title = Hero / Banner principal
		backto = page_about_order
	title
		title = Espaciado
	dropdown
		name = page_about_hero_margin_top
		description = Margen superior
		values
			none = Sin margen
			small = Pequeño
			medium = Mediano
			large = Grande
	dropdown
		name = page_about_hero_margin_bottom
		description = Margen inferior
		values
			none = Sin margen
			small = Pequeño
			medium = Mediano
			large = Grande
	dropdown
		name = page_about_hero_padding_top
		description = Padding superior
		values
			none = Sin padding
			small = Pequeño
			medium = Mediano
			large = Grande
	dropdown
		name = page_about_hero_padding_bottom
		description = Padding inferior
		values
			none = Sin padding
			small = Pequeño
			medium = Mediano
			large = Grande
	title
		title = Configuración
	subtitle
		subtitle = Imagen de escritorio
	image
		original = page_about_hero_desktop.jpg
		title = Cargar imagen (1920x500 recomendado)
		width = 1920
		height = 500
	subtitle
		subtitle = Imagen de celulares
	image
		original = page_about_hero_mobile.jpg
		title = Cargar imagen (768x600 recomendado)
		width = 768
		height = 600
	i18n_input
		name = page_about_hero_alt
		description = Texto alternativo de la imagen (SEO)
	i18n_input
		name = page_about_hero_title
		description = Título sobre el hero
	i18n_input
		name = page_about_hero_subtitle
		description = Subtítulo sobre el hero
	collapse
		title = Contenido principal
		backto = page_about_order
	title
		title = Espaciado
	dropdown
		name = page_about_content_margin_top
		description = Margen superior
		values
			none = Sin margen
			small = Pequeño
			medium = Mediano
			large = Grande
	dropdown
		name = page_about_content_margin_bottom
		description = Margen inferior
		values
			none = Sin margen
			small = Pequeño
			medium = Mediano
			large = Grande
	dropdown
		name = page_about_content_padding_top
		description = Padding superior
		values
			none = Sin padding
			small = Pequeño
			medium = Mediano
			large = Grande
	dropdown
		name = page_about_content_padding_bottom
		description = Padding inferior
		values
			none = Sin padding
			small = Pequeño
			medium = Mediano
			large = Grande
	title
		title = Configuración
	textarea
		name = page_about_content
		description = Contenido de la página (HTML permitido). Dejar vacío para usar el contenido del admin.
	collapse
		title = Sección de equipo
		backto = page_about_order
	title
		title = Espaciado
	dropdown
		name = page_about_team_margin_top
		description = Margen superior
		values
			none = Sin margen
			small = Pequeño
			medium = Mediano
			large = Grande
	dropdown
		name = page_about_team_margin_bottom
		description = Margen inferior
		values
			none = Sin margen
			small = Pequeño
			medium = Mediano
			large = Grande
	dropdown
		name = page_about_team_padding_top
		description = Padding superior
		values
			none = Sin padding
			small = Pequeño
			medium = Mediano
			large = Grande
	dropdown
		name = page_about_team_padding_bottom
		description = Padding inferior
		values
			none = Sin padding
			small = Pequeño
			medium = Mediano
			large = Grande
	title
		title = Configuración
	checkbox
		name = page_about_show_team
		description = Mostrar sección de equipo
	i18n_input
		name = page_about_team_title
		description = Título de la sección
	subtitle
		subtitle = Miembro 1
	image
		original = page_about_team_1.jpg
		title = Foto del miembro
		width = 300
		height = 300
	i18n_input
		name = page_about_team_01_name
		description = Nombre
	i18n_input
		name = page_about_team_01_role
		description = Cargo/Rol
	subtitle
		subtitle = Miembro 2
	image
		original = page_about_team_2.jpg
		title = Foto del miembro
		width = 300
		height = 300
	i18n_input
		name = page_about_team_02_name
		description = Nombre
	i18n_input
		name = page_about_team_02_role
		description = Cargo/Rol
	subtitle
		subtitle = Miembro 3
	image
		original = page_about_team_3.jpg
		title = Foto del miembro
		width = 300
		height = 300
	i18n_input
		name = page_about_team_03_name
		description = Nombre
	i18n_input
		name = page_about_team_03_role
		description = Cargo/Rol
	subtitle
		subtitle = Miembro 4
	image
		original = page_about_team_4.jpg
		title = Foto del miembro
		width = 300
		height = 300
	i18n_input
		name = page_about_team_04_name
		description = Nombre
	i18n_input
		name = page_about_team_04_role
		description = Cargo/Rol
	collapse
		title = Call to Action
		backto = page_about_order
	title
		title = Espaciado
	dropdown
		name = page_about_cta_margin_top
		description = Margen superior
		values
			none = Sin margen
			small = Pequeño
			medium = Mediano
			large = Grande
	dropdown
		name = page_about_cta_margin_bottom
		description = Margen inferior
		values
			none = Sin margen
			small = Pequeño
			medium = Mediano
			large = Grande
	dropdown
		name = page_about_cta_padding_top
		description = Padding superior
		values
			none = Sin padding
			small = Pequeño
			medium = Mediano
			large = Grande
	dropdown
		name = page_about_cta_padding_bottom
		description = Padding inferior
		values
			none = Sin padding
			small = Pequeño
			medium = Mediano
			large = Grande
	title
		title = Configuración
	color
		name = page_about_cta_bg_color
		description = Color de fondo
	i18n_input
		name = page_about_cta_title
		description = Título
	i18n_input
		name = page_about_cta_description
		description = Descripción
	i18n_input
		name = page_about_cta_button
		description = Texto del botón
	i18n_input
		name = page_about_cta_url
		description = Link del botón
	collapse
		title = Página Quiénes Somos
		backto = _top
	section_order
		name = page_about_order
		start_index = 1
		sections
			hero = Hero / Banner principal
			content = Contenido principal
			team = Sección de equipo
			cta = Call to Action
```

### 2. Defaults (defaults.txt)

```
page_about_handle = quienes-somos
page_about_margin_top = none
page_about_margin_bottom = none
page_about_padding_top = none
page_about_padding_bottom = none
page_about_order_1 = hero
page_about_order_2 = content
page_about_order_3 = team
page_about_order_4 = cta
page_about_order_5 = empty
page_about_order_6 = empty
page_about_hero_margin_top = none
page_about_hero_margin_bottom = none
page_about_hero_padding_top = none
page_about_hero_padding_bottom = none
page_about_content_margin_top = none
page_about_content_margin_bottom = medium
page_about_content_padding_top = medium
page_about_content_padding_bottom = medium
page_about_team_margin_top = none
page_about_team_margin_bottom = medium
page_about_team_padding_top = medium
page_about_team_padding_bottom = medium
page_about_show_team = 0
page_about_cta_margin_top = none
page_about_cta_margin_bottom = none
page_about_cta_padding_top = medium
page_about_cta_padding_bottom = medium
page_about_cta_bg_color = #F6F3EE
```

## Core Pattern: page.tpl Router

```twig
{# templates/page.tpl #}

{% set has_custom_template = false %}

{# ======================= #}
{#   LANDING PAGE ROUTER   #}
{#   Handle configurable   #}
{# ======================= #}

{# About Page - handle from settings #}
{% if settings.page_about_handle and page.handle == settings.page_about_handle %}
  {% set has_custom_template = true %}
  {% include 'snipplets/pages/page-about.tpl' %}

{# Contact Page - handle from settings #}
{% elseif settings.page_contact_handle and page.handle == settings.page_contact_handle %}
  {% set has_custom_template = true %}
  {% include 'snipplets/pages/page-contact.tpl' %}

{# FAQ Page - handle from settings #}
{% elseif settings.page_faq_handle and page.handle == settings.page_faq_handle %}
  {% set has_custom_template = true %}
  {% include 'snipplets/pages/page-faq.tpl' %}

{% endif %}

{# ======================= #}
{#    DEFAULT PAGE LAYOUT  #}
{# ======================= #}

{% if not has_custom_template %}
<section class="page-content" data-store="page-content">
  <div class="container py-5">
    <div class="row justify-content-center">
      <div class="col-12 col-lg-10">

        {# Page Title #}
        <h1 class="page-title mb-4">{{ page.name }}</h1>

        {# Page Content from Admin #}
        <div class="page-body user-content">
          {{ page.content | raw }}
        </div>

      </div>
    </div>
  </div>
</section>
{% endif %}
```

## The `page` Object

Available variables in page templates:

```twig
{{ page.id }}              {# Page ID #}
{{ page.name }}            {# Page title #}
{{ page.handle }}          {# URL slug (e.g., "quienes-somos") #}
{{ page.content }}         {# HTML content from admin editor #}
{{ page.url }}             {# Full URL #}
{{ page.seo_title }}       {# SEO title if set #}
{{ page.seo_description }} {# SEO description if set #}
{{ page.meta_description }} {# Meta description #}
```

## Best Practices

### 1. Naming Conventions

| Element | Pattern | Example |
|---------|---------|---------|
| Snipplet file | `page-{handle}.tpl` | `page-about.tpl` |
| Section switch | `page-{handle}-switch.tpl` | `page-about-switch.tpl` |
| Section folder | `page-{handle}/` | `page-about/` |
| Individual section | `section-{name}.tpl` | `section-hero.tpl` |
| Settings prefix | `page_{handle}_` | `page_about_` |
| Section order name | `page_{handle}_order` | `page_about_order` |
| Data store | `page-{handle}` | `data-store="page-about"` |
| CSS class | `.page-{handle}` | `.page-about` |

### 2. Settings Naming Convention

Use `snake_case` with zero-padded numbers for indexed items (e.g., `01`, `02`, `03`):

```
# Recommended pattern
i18n_input
	name = page_about_team_01_name

i18n_input
	name = page_about_team_02_name
```

This matches the existing theme patterns like `page_vendedoras_01_name`, `banner_services_01_title`, etc.

### 3. Always Include

- [ ] `section_order` component for reorderable sections
- [ ] `backto = page_{handle}_order` on each collapse
- [ ] `meta.default = page_{handle}_order` to show order panel by default
- [ ] Hero section with responsive images (desktop + mobile)
- [ ] Fallback to `{{ page.content | raw }}` if no custom content
- [ ] Spacing controls per section (margin/padding)
- [ ] SEO: proper heading hierarchy (h1 only once)
- [ ] Data-store attributes for app hooks

### 4. Section Order Loop Pattern

```twig
{% set rendered_sections = [] %}

{% for i in 1..6 %}
  {% set section = 'page_about_order_' ~ i %}
  {% set section_select = attribute(settings, section) %}

  {% if section_select and section_select != 'empty' and section_select not in rendered_sections %}
    {% include 'snipplets/pages/page-about-switch.tpl' %}
    {% set rendered_sections = rendered_sections | merge([section_select]) %}
  {% endif %}
{% endfor %}
```

## Common Landing Page Types

| Type | Typical Sections | Handle Examples |
|------|------------------|-----------------|
| About | Hero, Story, Team, Values, CTA | quienes-somos, about |
| Contact | Hero, Form, Map, Info | contacto, contact |
| FAQ | Hero, Accordion, CTA | faq, preguntas-frecuentes |
| Stores | Hero, Map, Store List | tiendas, stores, locales |
| Promo | Hero, Products, CTA | promo, sale, ofertas |
| Lookbook | Hero, Gallery, Products | lookbook, coleccion |

## Checklist: New Landing Page with Reorderable Sections

When creating a new landing page:

- [ ] Create page in admin with desired handle
- [ ] Create main snipplet: `snipplets/pages/page-{handle}.tpl`
- [ ] Create section switch: `snipplets/pages/page-{handle}-switch.tpl`
- [ ] Create section folder: `snipplets/pages/page-{handle}/`
- [ ] Create individual section files in the folder
- [ ] Add handle check to `page.tpl` router
- [ ] Add settings section in `settings.txt`:
  - [ ] `meta.default = page_{handle}_order`
  - [ ] `i18n_input` for handle (first setting!)
  - [ ] Each section as a `collapse` with `backto = page_{handle}_order`
  - [ ] Spacing controls per section
  - [ ] `section_order` at the end with all sections
- [ ] Add defaults in `defaults.txt`:
  - [ ] Handle default
  - [ ] Section order defaults (`page_{handle}_order_1`, etc.)
  - [ ] Spacing defaults
- [ ] Add translations in `translations.txt`
- [ ] Test reordering sections from admin
- [ ] Test with various section combinations

## Resources

- Related skill: `tiendanube-theme-config` - Settings and translations
- Related skill: `tiendanube-objects` - Available Twig objects
- See `home.tpl` and `home-section-switch.tpl` for reference implementation
