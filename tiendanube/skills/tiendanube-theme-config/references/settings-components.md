# Settings.txt Components Reference

## Structure Overview

```
Section Name
	meta
		icon = icon_name
		advanced = true|false
	component
		property = value
```

**Critical:** Use TAB characters for indentation, never spaces.

## Section Components

### section (implicit)
Main container shown in admin panel sidebar.

```
Página de inicio
	meta
		icon = home
		advanced = false
```

**Properties:**
- First line is the section title (no keyword needed)
- `icon` - Font Awesome icon name (without `fa-` prefix)
- `advanced` - `true` puts section in "Configuraciones avanzadas"

**Common icons:** `home`, `image`, `paint-brush`, `cog`, `arrow-down`, `font`, `shopping-cart`, `list`, `envelope`

### collapse
Accordion container that expands/collapses.

```
	collapse
		title = Carrusel de imágenes
```

**Properties:**
- `title` - Text shown on collapsed header

## Text Components

### title
Section heading within a group.

```
	title
		title = Configuración del encabezado
```

### subtitle
Smaller heading, often used for help links.

```
	subtitle
		subtitle = <a href='/admin/pages/'>Crear página</a>
```

**Note:** Can contain HTML including links.

### description
Paragraph text for instructions.

```
	description
		description = Los banners se mostrarán de izquierda a derecha
```

## Input Components

### checkbox
Boolean toggle switch.

```
	checkbox
		name = show_slider
		description = Mostrar carrusel de imágenes
```

**Properties:**
- `name` - Setting key (snake_case)
- `description` - Label text

**Default:** `false` unless set `true` in defaults.txt

### checkbox_global
Checkbox linked to global store settings.

```
	checkbox_global
		name = shipping_calculator_cart_page
		description = Mostrar calculador de envíos en el carrito
```

### dropdown
Select menu with predefined options.

```
	dropdown
		name = products_per_row
		description = Productos por fila
		values
			2 = 2 productos
			3 = 3 productos
			4 = 4 productos
```

**Properties:**
- `name` - Setting key
- `description` - Label text
- `values` - Key-value pairs (key = display_text)

**In templates:**
```twig
{% if settings.products_per_row == '3' %}
```

### text
Simple text input field.

```
	text
		name = cart_minimum_value
		description = Monto mínimo de compra
		placeholder = Ej.: 3000
```

**Properties:**
- `name` - Setting key
- `description` - Label text
- `placeholder` - Placeholder text (optional)

### i18n_input
Multi-language text input (one input per store language).

```
	i18n_input
		name = welcome_message
		description = Mensaje de bienvenida
```

**In defaults.txt:**
```
welcome_message_es = ¡Bienvenido!
welcome_message_pt = Bem-vindo!
welcome_message_en = Welcome!
welcome_message_es_mx = ¡Bienvenido!
```

**In templates:**
```twig
{{ settings.welcome_message }}  {# Auto-selects current language #}
```

### textarea
Large text area for longer content.

```
	textarea
		name = custom_seal_code
```

### css_code
CSS editor with syntax validation.

```
	css_code
		name = css_code
		description = Código CSS personalizado
```

**In layout.tpl:**
```twig
<style>
{{ settings.css_code | raw }}
</style>
```

## Selection Components

### menu
Dropdown of menus created in admin.

```
	menu
		name = footer_menu
		description = Menú del pie de página
```

**In templates:**
```twig
{% for item in menus[settings.footer_menu] %}
  <a href="{{ item.url }}">{{ item.name }}</a>
{% endfor %}
```

### font
Font family selector.

```
	font
		name = font_headings
		description = Tipografía para títulos
		values
			"Muli", sans-serif = Muli
			"Playfair Display", serif = Playfair Display
			"Open Sans", sans-serif = Open Sans
			"Lora", serif = Lora
```

**In style-colors.scss.tpl:**
```scss
$font-headings: {{ settings.font_headings }};

h1, h2, h3 {
  font-family: $font-headings;
}
```

## Color Components

### color
Color picker with hex value.

```
	color
		name = primary_color
		description = Color principal de tu marca
```

**In defaults.txt:**
```
primary_color = #000000
```

**In style-colors.scss.tpl:**
```scss
$primary-color: {{ settings.primary_color }};
```

### palettes
Predefined color combinations.

```
	palettes
		palettes
			palette_1
				primary_color = #000000
				background_color = #FFFFFF
				text_color = #333333
			palette_2
				primary_color = #FF5500
				background_color = #F9F9F9
				text_color = #222222
```

**Note:** Palette color names must match existing `color` component names.

## Image Components

### image
Single image upload.

```
	image
		original = banner_home.jpg
		title = Cargar imagen
		width = 1200
		height = 400
```

**Properties:**
- `original` - Filename (stored in theme's images folder)
- `title` - Button text
- `width` / `height` - Suggested dimensions

**In templates:**
```twig
{% if store.has_theme_image('banner_home.jpg') %}
  <img src="{{ 'banner_home.jpg' | static_url }}" alt="Banner">
{% endif %}
```

### gallery
Multiple image upload (for sliders).

```
	gallery
		name = slider
		gallery_image = Agregar imagen
		gallery_link = [Opcional] Link de la imagen
		gallery_width = 1580
		gallery_height = 600
```

**Properties:**
- `name` - Gallery identifier
- `gallery_image` - Add image button text
- `gallery_link` - Link field label
- `gallery_width` / `gallery_height` - Suggested dimensions

**CRITICAL - Gallery Naming Rule:**
**NEVER use numeric digits (0-9) in gallery `name` values.** TiendaNube has a bug that prevents all slides from displaying when the gallery name contains numbers.

Use textual numbers in English instead:
```
# WRONG - will cause display issues
gallery
	name = slider_01
	name = banner_2
	name = gallery_section_3

# CORRECT - use textual numbers
gallery
	name = slider_one
	name = banner_two
	name = gallery_section_three
```

**Textual number reference:**
- 1 → one
- 2 → two
- 3 → three
- 4 → four
- 5 → five
- 6 → six
- 7 → seven
- 8 → eight
- 9 → nine
- 10 → ten

**In templates:**
```twig
{% for slide in settings.slider %}
  <div class="slide">
    {% if slide.link %}
      <a href="{{ slide.link }}">
    {% endif %}
    <img src="{{ slide.image | static_url }}" alt="Slide">
    {% if slide.link %}
      </a>
    {% endif %}
  </div>
{% endfor %}
```

## Complete Example

```
Página de inicio
	meta
		icon = home
	collapse
		title = Carrusel de imágenes
	checkbox
		name = show_slider
		description = Mostrar carrusel
	gallery
		name = slider
		gallery_image = Agregar imagen
		gallery_link = Link de la imagen
		gallery_width = 1580
	title
		title = Productos destacados
	checkbox
		name = show_featured
		description = Mostrar productos destacados
	i18n_input
		name = featured_title
		description = Título de la sección

Colores
	meta
		icon = paint-brush
	color
		name = primary_color
		description = Color principal
	color
		name = secondary_color
		description = Color secundario
	palettes
		palettes
			palette_1
				primary_color = #000000
				secondary_color = #FFFFFF

Tipografías
	meta
		icon = font
		advanced = true
	font
		name = font_headings
		description = Títulos
		values
			"Muli", sans-serif = Muli
			"Open Sans", sans-serif = Open Sans
```

## Special Components

### section_order
Drag & drop reordering for homepage sections.

```
	section_order
		name = home_order_position
		start_index = 0
		sections
			slider = Carrusel de imágenes
			welcome = Mensaje de bienvenida
			products = Productos destacados
			informatives = Información de envíos
			categories = Banners de categorías
			video = Video
			instafeed = Instagram
```

**Properties:**
- `name` - Base name for position settings
- `start_index` - Starting index (usually 0)
- `sections` - Key-value pairs (section_key = Display Name)

**Generated settings:**
- `home_order_position_0` = first section key
- `home_order_position_1` = second section key
- etc.

**In templates (home.tpl):**
```twig
{% set sections = [
  'home_order_position_0',
  'home_order_position_1', 
  'home_order_position_2',
  'home_order_position_3',
  'home_order_position_4',
  'home_order_position_5',
  'home_order_position_6'
] %}

{% for section in sections %}
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

### link
Link to another section in settings panel.

```
	link
		to = Página de inicio
		description = Configurar secciones de la página de inicio
```

**Properties:**
- `to` - Name of target section (must match section title exactly)
- `description` - Link text displayed

**Use case:** Cross-reference between settings sections.

## Conditional Display

Use `data-toggle-info` attribute in subtitles to show/hide based on checkbox:

```
	checkbox
		name = show_slider
		description = Mostrar carrusel
	subtitle
		subtitle = <span data-toggle-info='show-on_show_slider-active'>Opciones del carrusel</span>
```

Pattern: `show-on_{checkbox_name}-active` or `show-on_{checkbox_name}-inactive`
