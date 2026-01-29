# TiendaNube Add Settings

You are adding editable settings to an existing TiendaNube theme component.

## Required Skills
Read first:
- `/mnt/skills/user/tiendanube-theme-config/SKILL.md` - Settings structure and types

## Workflow

### Step 1: Analyze the Template
Read the provided `.tpl` file and identify:
- Hardcoded text that should be editable
- Images that should be uploadable
- Colors that should be customizable
- Show/hide toggles needed
- Numeric values (counts, sizes)

### Step 2: Define Settings Prefix
Use a consistent prefix based on component location:
- `home_[component]_` - Home page sections
- `header_` - Header elements
- `footer_` - Footer elements
- `product_` - Product page
- `cart_` - Cart page
- `[page]_` - Other pages

### Step 3: Generate Settings

For each editable element, create a setting:

```
[prefix]_[element]
    name = { "es": "Nombre ES", "pt": "Nome PT", "en": "Name EN" }
    type = [type]
    default = [default_value]
```

### Field Types Reference

| Type | Use For | Default Format |
|------|---------|----------------|
| `checkbox` | Show/hide, toggles | `true` or `false` |
| `text` | Short text, titles | `"texto"` or i18n object |
| `textarea` | Long text, descriptions | `"texto largo"` |
| `i18n_text` | Translatable content | (uses translations.txt) |
| `color` | Any color | `"#RRGGBB"` |
| `image` | Uploadable images | `""` (empty) |
| `dropdown` | Select from options | `"option_value"` |
| `number` | Numeric values | `4` |
| `font` | Typography | `"Roboto"` |

### Step 4: Update Template

Replace hardcoded values with settings:

```twig
{# Before #}
<h2>Productos Destacados</h2>

{# After #}
<h2>{{ settings.home_featured_title }}</h2>
```

For show/hide:
```twig
{% if settings.home_featured_show %}
  {# Component content #}
{% endif %}
```

For images:
```twig
{% if settings.banner_image %}
  <img src="{{ settings.banner_image | static_url }}" alt="{{ settings.banner_alt | default('') }}">
{% endif %}
```

### Step 5: Add Translations (if using i18n_text)

```
es "Texto en español"
pt "Texto em português"  
en "Text in English"
```

## Output Format

Provide:

1. **Settings Fragment**
```
{# Add to config/settings.txt #}

[all settings here]
```

2. **Template Updates**
```twig
{# Changes to [filename].tpl #}

[show diff or full updated sections]
```

3. **Translations Fragment** (if needed)
```
{# Add to config/translations.txt #}

[translations here]
```

## Common Settings Patterns

### Section Toggle + Title
```
home_section_show
    name = { "es": "Mostrar sección", "pt": "Mostrar seção", "en": "Show section" }
    type = checkbox
    default = true

home_section_title
    name = { "es": "Título", "pt": "Título", "en": "Title" }
    type = text
    default = { "es": "Mi Sección", "pt": "Minha Seção", "en": "My Section" }
```

### Image with Alt Text
```
banner_image
    name = { "es": "Imagen", "pt": "Imagem", "en": "Image" }
    type = image

banner_image_alt
    name = { "es": "Texto alternativo", "pt": "Texto alternativo", "en": "Alt text" }
    type = text
    default = ""
```

### Link with Text
```
cta_text
    name = { "es": "Texto del botón", "pt": "Texto do botão", "en": "Button text" }
    type = text
    default = { "es": "Ver más", "pt": "Ver mais", "en": "See more" }

cta_url
    name = { "es": "URL del botón", "pt": "URL do botão", "en": "Button URL" }
    type = text
    default = ""
```

### Dropdown Selection
```
products_count
    name = { "es": "Cantidad de productos", "pt": "Quantidade de produtos", "en": "Products count" }
    type = dropdown
    default = "4"
    options
        4 = { "es": "4 productos", "pt": "4 produtos", "en": "4 products" }
        6 = { "es": "6 productos", "pt": "6 produtos", "en": "6 products" }
        8 = { "es": "8 productos", "pt": "8 produtos", "en": "8 products" }
```

### Color Customization
```
section_bg_color
    name = { "es": "Color de fondo", "pt": "Cor de fundo", "en": "Background color" }
    type = color
    default = "#ffffff"

section_text_color
    name = { "es": "Color del texto", "pt": "Cor do texto", "en": "Text color" }
    type = color
    default = "#333333"
```

## User Input Expected

The user will provide:
- Template file to analyze (path or content)
- What should be editable
- Settings prefix to use (or suggest one)

If unclear, ask for clarification.
