---
name: tiendanube-bootstrap-frontend
description: Expert skill for frontend development of TiendaNube/Nuvemshop themes using Bootstrap 4. Use when developing, modifying, or creating theme components (.tpl, .scss, .js files), implementing designs from Figma, or building responsive ecommerce UI components. Integrates with Figma MCP for pixel-perfect design-to-code implementation.
---

# TiendaNube Bootstrap 4 Frontend Development

## Overview

This skill provides expertise for developing TiendaNube (Nuvemshop) themes using Bootstrap 4 as the CSS framework. It enables pixel-perfect implementation of Figma designs into responsive theme components.

## Workflow Decision

**Implementing a new design from Figma?**
→ Follow "Design-to-Code Workflow" section

**Creating/modifying theme components?**
→ Follow "Component Development" section

**Need Bootstrap 4 reference?**
→ See `references/bootstrap4.md`

**Need TiendaNube structure reference?**
→ See `references/tiendanube-theme.md`

## Design-to-Code Workflow

When implementing a Figma design:

### 1. Analyze the Design
```
get_metadata     → Understand structure hierarchy
get_screenshot   → Visual reference
get_variable_defs → Extract design tokens
```

### 2. Extract Design Context
```
get_design_context → Get layout, spacing, typography
```

### 3. Map to Bootstrap 4
- Auto-layout horizontal → `row` + `col-*`
- Auto-layout vertical → Stack with `mb-*` spacing
- Padding/margins → Bootstrap spacing utilities (`p-*`, `m-*`)
- Colors → CSS variables from design tokens

### 4. Build the Component
```html
<!-- TiendaNube snipplet structure -->
<div class="section-{name}">
  <div class="container">
    <div class="row">
      <!-- Bootstrap grid matching Figma layout -->
    </div>
  </div>
</div>
```

### 5. Verify Implementation
Compare screenshot with rendered component.

For detailed Figma MCP workflow: See `references/figma-mcp-workflow.md`

## Component Development

### File Structure
```
snipplets/
├── {component-name}/
│   └── {component-name}.tpl
templates/
└── {page}.tpl
static/css/
├── style-critical.tpl    # Above-fold CSS
└── style-async.scss.tpl  # Deferred CSS
```

### Snipplet Template
```twig
{# snipplets/component/component-name.tpl #}
<div class="component-name">
  <div class="container">
    <div class="row">
      {% for item in items %}
        <div class="col-6 col-md-4 col-lg-3">
          {# Item content #}
        </div>
      {% endfor %}
    </div>
  </div>
</div>
```

### CSS Pattern
```scss
// style-async.scss.tpl
.component-name {
  // Use Bootstrap utilities first, custom CSS only when needed
  padding-top: 40px;  // or section spacing
  padding-bottom: 40px;
  
  // Custom styles for non-Bootstrap requirements
  &__title {
    // Only if Bootstrap text utilities insufficient
  }
}
```

## Bootstrap 4 Core Patterns

### Responsive Grid
```html
<!-- Mobile: 2 cols, Tablet: 3 cols, Desktop: 4 cols -->
<div class="row">
  <div class="col-6 col-md-4 col-lg-3">Item</div>
</div>
```

### Spacing Scale
| Size | Value | Class |
|------|-------|-------|
| 1 | 0.25rem (4px) | `p-1`, `m-1` |
| 2 | 0.5rem (8px) | `p-2`, `m-2` |
| 3 | 1rem (16px) | `p-3`, `m-3` |
| 4 | 1.5rem (24px) | `p-4`, `m-4` |
| 5 | 3rem (48px) | `p-5`, `m-5` |

### Display/Visibility
```html
<!-- Mobile only -->
<div class="d-block d-md-none">Mobile</div>

<!-- Desktop only -->
<div class="d-none d-md-block">Desktop</div>
```

### Flexbox
```html
<div class="d-flex justify-content-between align-items-center">
  <span>Left</span>
  <span>Right</span>
</div>
```

For complete reference: See `references/bootstrap4.md`

## TiendaNube Patterns

### Product Card
```twig
<div class="col-6 col-md-4 col-lg-3">
  <div class="item item-product">
    <a href="{{ product.url }}" class="item-link">
      <div class="item-image">
        <img src="{{ product.featured_image | product_image_url('medium') }}" 
             alt="{{ product.name }}" 
             class="img-fluid"
             loading="lazy">
      </div>
      <div class="item-info">
        <h3 class="item-name">{{ product.name }}</h3>
        <div class="item-price">
          {% if product.compare_at_price %}
            <span class="price-compare text-muted text-decoration-line-through">
              {{ product.compare_at_price | money }}
            </span>
          {% endif %}
          <span class="price font-weight-bold">{{ product.price | money }}</span>
        </div>
      </div>
    </a>
  </div>
</div>
```

### Section Container
```twig
<section class="section-{{ section_name }} py-4 py-md-5">
  <div class="container">
    {% if title %}
      <h2 class="section-title text-center mb-4">{{ title }}</h2>
    {% endif %}
    <div class="row">
      {# Content #}
    </div>
  </div>
</section>
```

### Form Component
```twig
<form class="form-{{ form_name }}">
  <div class="form-group">
    <label for="{{ field_id }}">{{ label | translate }}</label>
    <input type="{{ type }}" 
           class="form-control" 
           id="{{ field_id }}" 
           name="{{ field_name }}"
           {% if required %}required{% endif %}>
  </div>
  <button type="submit" class="btn btn-primary btn-block">
    {{ submit_text | translate }}
  </button>
</form>
```

For complete reference: See `references/tiendanube-theme.md`

## Implementation Guidelines

### Mobile-First
1. Start with mobile styles (no breakpoint)
2. Add `md` breakpoint for tablet
3. Add `lg`/`xl` for desktop enhancements

### Performance
1. Critical CSS inline (~14KB max)
2. `loading="lazy"` on images below fold
3. Use appropriate image sizes
4. Defer non-critical JS

### Accessibility
1. Semantic HTML elements
2. Alt text on images
3. Proper heading hierarchy
4. Focus states on interactive elements

### Code Style
1. Prefer Bootstrap utilities over custom CSS
2. Use BEM for custom classes: `.block__element--modifier`
3. Keep snipplets small and focused
4. Comment complex logic in templates

## Resources

### References
- `references/bootstrap4.md` - Bootstrap 4 classes and utilities
- `references/tiendanube-theme.md` - Theme structure and syntax
- `references/figma-mcp-workflow.md` - Design-to-code process
