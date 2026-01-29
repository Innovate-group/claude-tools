# Bootstrap 4 Quick Reference

## Grid System

### Breakpoints
| Breakpoint | Class infix | Dimensions |
|------------|-------------|------------|
| Extra small | None | <576px |
| Small | `sm` | ≥576px |
| Medium | `md` | ≥768px |
| Large | `lg` | ≥992px |
| Extra large | `xl` | ≥1200px |

### Grid Structure
```html
<div class="container"> <!-- or container-fluid -->
  <div class="row">
    <div class="col-{breakpoint}-{1-12}">Content</div>
  </div>
</div>
```

### Column Classes
- `.col` - Equal width columns
- `.col-{1-12}` - Specific width (all breakpoints)
- `.col-sm-{1-12}` - Small and up
- `.col-md-{1-12}` - Medium and up
- `.col-lg-{1-12}` - Large and up
- `.col-xl-{1-12}` - Extra large only
- `.col-auto` - Width based on content

### Offset Classes
- `.offset-{1-11}` - All breakpoints
- `.offset-{breakpoint}-{1-11}` - Responsive offsets

### Order Classes
- `.order-{0-12}` - Reorder columns
- `.order-first` / `.order-last`

## Spacing Utilities

### Format: `{property}{sides}-{breakpoint}-{size}`

**Properties:**
- `m` - margin
- `p` - padding

**Sides:**
- `t` - top
- `b` - bottom
- `l` - left
- `r` - right
- `x` - left & right
- `y` - top & bottom
- (blank) - all sides

**Sizes:**
- `0` - 0
- `1` - 0.25rem
- `2` - 0.5rem
- `3` - 1rem
- `4` - 1.5rem
- `5` - 3rem
- `auto` - auto (margins only)

### Examples
```html
<div class="mt-3">margin-top: 1rem</div>
<div class="px-4">padding-left/right: 1.5rem</div>
<div class="mb-md-5">margin-bottom: 3rem on md+</div>
<div class="mx-auto">center horizontally</div>
```

## Display Utilities

### Format: `.d-{breakpoint}-{value}`

**Values:** `none`, `inline`, `inline-block`, `block`, `table`, `table-cell`, `table-row`, `flex`, `inline-flex`

### Common Patterns
```html
<div class="d-none d-md-block">Hidden on xs/sm, visible on md+</div>
<div class="d-block d-lg-none">Visible until lg</div>
<div class="d-flex">Flexbox container</div>
```

## Flexbox Utilities

### Direction
- `.flex-row` / `.flex-row-reverse`
- `.flex-column` / `.flex-column-reverse`

### Justify Content (main axis)
- `.justify-content-start`
- `.justify-content-end`
- `.justify-content-center`
- `.justify-content-between`
- `.justify-content-around`

### Align Items (cross axis)
- `.align-items-start`
- `.align-items-end`
- `.align-items-center`
- `.align-items-baseline`
- `.align-items-stretch`

### Align Self (individual)
- `.align-self-start`
- `.align-self-end`
- `.align-self-center`
- `.align-self-baseline`
- `.align-self-stretch`

### Flex Grow/Shrink
- `.flex-grow-0` / `.flex-grow-1`
- `.flex-shrink-0` / `.flex-shrink-1`

### Wrap
- `.flex-wrap` / `.flex-nowrap` / `.flex-wrap-reverse`

## Text Utilities

### Alignment
- `.text-left` / `.text-center` / `.text-right`
- `.text-{breakpoint}-left` (responsive)

### Transform
- `.text-lowercase` / `.text-uppercase` / `.text-capitalize`

### Weight & Style
- `.font-weight-bold` / `.font-weight-normal` / `.font-weight-light`
- `.font-italic`

### Wrapping
- `.text-nowrap` / `.text-truncate`

## Color Utilities

### Text Colors
`.text-primary`, `.text-secondary`, `.text-success`, `.text-danger`, `.text-warning`, `.text-info`, `.text-light`, `.text-dark`, `.text-muted`, `.text-white`

### Background Colors
`.bg-primary`, `.bg-secondary`, `.bg-success`, `.bg-danger`, `.bg-warning`, `.bg-info`, `.bg-light`, `.bg-dark`, `.bg-white`, `.bg-transparent`

## Border Utilities

### Add Borders
- `.border` - all sides
- `.border-top` / `.border-right` / `.border-bottom` / `.border-left`

### Remove Borders
- `.border-0` - all sides
- `.border-top-0` / `.border-right-0` / `.border-bottom-0` / `.border-left-0`

### Border Radius
- `.rounded` - all corners
- `.rounded-top` / `.rounded-right` / `.rounded-bottom` / `.rounded-left`
- `.rounded-circle` / `.rounded-pill`
- `.rounded-0` - remove radius

## Sizing Utilities

### Width
- `.w-25` / `.w-50` / `.w-75` / `.w-100` / `.w-auto`
- `.mw-100` - max-width: 100%

### Height
- `.h-25` / `.h-50` / `.h-75` / `.h-100` / `.h-auto`
- `.mh-100` - max-height: 100%

## Position Utilities

- `.position-static` / `.position-relative` / `.position-absolute` / `.position-fixed` / `.position-sticky`
- `.fixed-top` / `.fixed-bottom`
- `.sticky-top`

## Common Components

### Buttons
```html
<button class="btn btn-primary">Primary</button>
<button class="btn btn-outline-secondary">Outline</button>
<button class="btn btn-lg">Large</button>
<button class="btn btn-sm">Small</button>
<button class="btn btn-block">Full width</button>
```

### Cards
```html
<div class="card">
  <img class="card-img-top" src="..." alt="...">
  <div class="card-body">
    <h5 class="card-title">Title</h5>
    <p class="card-text">Content</p>
    <a href="#" class="btn btn-primary">Action</a>
  </div>
</div>
```

### Forms
```html
<div class="form-group">
  <label for="input">Label</label>
  <input type="text" class="form-control" id="input">
</div>
<div class="form-check">
  <input class="form-check-input" type="checkbox" id="check">
  <label class="form-check-label" for="check">Check</label>
</div>
```

### Images
```html
<img class="img-fluid" src="..."> <!-- Responsive -->
<img class="img-thumbnail" src="..."> <!-- With border -->
```

## Responsive Patterns

### Hide/Show by Breakpoint
```html
<!-- Mobile only -->
<div class="d-block d-md-none">Mobile</div>

<!-- Desktop only -->
<div class="d-none d-md-block">Desktop</div>

<!-- Tablet only -->
<div class="d-none d-md-block d-lg-none">Tablet</div>
```

### Responsive Grid Example
```html
<div class="row">
  <!-- Full width on mobile, half on tablet, third on desktop -->
  <div class="col-12 col-md-6 col-lg-4">Item</div>
</div>
```
