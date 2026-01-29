# TiendaNube Theme Structure

## Directory Structure

```
theme/
├── config/
│   ├── settings.txt        # Theme settings schema
│   ├── data.json          # Default values
│   └── translations.txt   # Multi-language strings
├── layouts/
│   └── layout.tpl         # Base HTML template
├── templates/
│   ├── home.tpl           # Homepage
│   ├── product.tpl        # Product detail
│   ├── category.tpl       # Category listing
│   ├── cart.tpl           # Shopping cart
│   ├── contact.tpl        # Contact page
│   ├── page.tpl           # Static pages
│   ├── search.tpl         # Search results
│   └── ...
├── snipplets/
│   ├── header/            # Header components
│   ├── footer/            # Footer components
│   ├── product/           # Product components
│   ├── navigation/        # Menus and nav
│   ├── grid/              # Grid items
│   ├── forms/             # Form components
│   ├── home/              # Homepage sections
│   └── ...
└── static/
    ├── css/
    │   ├── style-critical.tpl   # Above-fold CSS (inline)
    │   ├── style-async.scss.tpl # Deferred CSS
    │   └── style-colors.scss.tpl # Dynamic colors
    ├── js/
    │   └── store.js.tpl
    └── images/
```

## Template Syntax (Twig-like)

### Variables
```twig
{{ store.name }}
{{ product.name }}
{{ product.price | money }}
```

### Conditionals
```twig
{% if product.available %}
  <button>Add to cart</button>
{% else %}
  <button disabled>Out of stock</button>
{% endif %}
```

### Loops
```twig
{% for product in products %}
  <div class="product-item">
    {{ product.name }}
  </div>
{% endfor %}
```

### Includes
```twig
{% include 'snipplets/product/product-card.tpl' %}
{% include 'snipplets/header/header.tpl' with { 'show_cart': true } %}
```

### Filters
```twig
{{ product.price | money }}        {# Format as currency #}
{{ text | translate }}             {# Multi-language #}
{{ image | product_image_url }}    {# Image URL #}
{{ product.name | slugify }}       {# URL-safe string #}
```

## CSS Architecture

### Critical CSS (`style-critical.tpl`)
Inline styles for above-the-fold content:
- Reset/normalize
- Layout structure
- Header/navigation
- Typography basics
- Critical component styles

### Async CSS (`style-async.scss.tpl`)
Deferred styles for non-critical elements:
- Full component styles
- Animations
- Below-fold sections
- Print styles

### Color Variables (`style-colors.scss.tpl`)
Dynamic SCSS with admin-configurable values:
```scss
$primary-color: {{ settings.primary_color }};
$secondary-color: {{ settings.secondary_color }};
$text-color: {{ settings.text_color }};
$background-color: {{ settings.background_color }};
```

## Snipplet Patterns

### Product Card (`snipplets/grid/item.tpl`)
```html
<div class="col-6 col-md-4 col-lg-3">
  <div class="item item-product">
    <div class="item-image">
      <a href="{{ product.url }}">
        <img src="{{ product.featured_image | product_image_url('medium') }}" 
             alt="{{ product.name }}" 
             class="img-fluid">
      </a>
    </div>
    <div class="item-info">
      <h3 class="item-name">
        <a href="{{ product.url }}">{{ product.name }}</a>
      </h3>
      <div class="item-price">
        {% if product.compare_at_price %}
          <span class="price-compare">{{ product.compare_at_price | money }}</span>
        {% endif %}
        <span class="price">{{ product.price | money }}</span>
      </div>
    </div>
  </div>
</div>
```

### Section Container Pattern
```html
<section class="section-{{ section_name }}">
  <div class="container">
    <div class="row">
      {% for item in items %}
        {% include 'snipplets/grid/item.tpl' %}
      {% endfor %}
    </div>
  </div>
</section>
```

## JavaScript Patterns

### Store Events
```javascript
// Cart update
LS.on('cart:updated', function(cart) {
  // Update cart UI
});

// Quick shop
LS.on('product:quickshop', function(product) {
  // Handle quick view
});
```

### AJAX Cart
```javascript
LS.addToCart({
  product_id: {{ product.id }},
  quantity: 1,
  variant_id: {{ variant.id }}
});
```

## Common Variables

### Store
- `{{ store.name }}` - Store name
- `{{ store.url }}` - Base URL
- `{{ store.logo }}` - Logo URL
- `{{ store.currency }}` - Currency code

### Product
- `{{ product.id }}` - Product ID
- `{{ product.name }}` - Product name
- `{{ product.price }}` - Price (raw)
- `{{ product.price | money }}` - Formatted price
- `{{ product.url }}` - Product URL
- `{{ product.featured_image }}` - Main image
- `{{ product.images }}` - All images array
- `{{ product.description }}` - HTML description
- `{{ product.available }}` - Stock boolean
- `{{ product.variants }}` - Variants array
- `{{ product.compare_at_price }}` - Original price

### Cart
- `{{ cart.items }}` - Cart items array
- `{{ cart.total }}` - Total price
- `{{ cart.items_count }}` - Number of items

### Settings
- `{{ settings.primary_color }}`
- `{{ settings.show_slider }}`
- `{{ settings.header_style }}`

## Image Sizes

```twig
{{ image | product_image_url('tiny') }}    {# 50x50 #}
{{ image | product_image_url('thumb') }}   {# 100x100 #}
{{ image | product_image_url('small') }}   {# 240x240 #}
{{ image | product_image_url('medium') }}  {# 480x480 #}
{{ image | product_image_url('large') }}   {# 640x640 #}
{{ image | product_image_url('huge') }}    {# 1024x1024 #}
{{ image | product_image_url('original') }} {# Full size #}
```

## Mobile-First Approach

Always start with mobile styles, then add breakpoints:

```scss
.product-grid {
  // Mobile first (default)
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 15px;
  
  // Tablet
  @media (min-width: 768px) {
    grid-template-columns: repeat(3, 1fr);
    gap: 20px;
  }
  
  // Desktop
  @media (min-width: 992px) {
    grid-template-columns: repeat(4, 1fr);
    gap: 30px;
  }
}
```

## Performance Considerations

1. **Critical CSS**: Keep inline CSS minimal (~14KB max)
2. **Lazy Loading**: Use `loading="lazy"` for images below fold
3. **Image Optimization**: Use appropriate image sizes
4. **JS Deferral**: Load non-critical JS with `defer`
5. **Font Loading**: Use `font-display: swap`
