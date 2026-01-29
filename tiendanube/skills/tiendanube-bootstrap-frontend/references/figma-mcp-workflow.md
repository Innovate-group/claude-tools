# Figma MCP Workflow

## Overview

Figma MCP (Model Context Protocol) enables AI tools to access design context directly from Figma files, enabling pixel-perfect implementation.

## Available Tools

### get_design_context
Retrieves code and design information for a Figma node.
- Returns: Layout structure, component hierarchy, design tokens
- Use: Primary tool for implementing designs

### get_screenshot  
Captures a screenshot of a Figma node.
- Returns: Visual reference image
- Use: Visual verification, understanding complex layouts

### get_metadata
Returns XML structure with node IDs, types, positions, sizes.
- Returns: Structural overview of the design
- Use: Understanding design hierarchy before implementation

### get_variable_defs
Gets variable definitions (colors, spacing, typography).
- Returns: Design tokens mapping (e.g., `{'primary': '#FF5500'}`)
- Use: Extracting design system values

### get_code_connect_map
Maps Figma components to codebase components.
- Returns: Component name and source location mappings
- Use: Reusing existing codebase components

## Workflow Steps

### 1. Analyze Design Structure
```
Call: get_metadata
Purpose: Understand overall layout hierarchy
Output: Node tree with IDs and layer types
```

### 2. Get Visual Reference
```
Call: get_screenshot
Purpose: Visual understanding of the target
Output: Screenshot for reference
```

### 3. Extract Design Context
```
Call: get_design_context
Purpose: Get implementable design data
Output: Layout info, styles, component structure
```

### 4. Get Design Tokens
```
Call: get_variable_defs
Purpose: Extract colors, spacing, typography
Output: Variable definitions for CSS
```

### 5. Check Component Mappings
```
Call: get_code_connect_map
Purpose: Identify reusable components
Output: Existing component references
```

## Design Data Extraction

### From get_design_context, extract:

**Layout Information:**
- Width/height dimensions
- Padding and margins
- Gap values between elements
- Auto-layout direction (row/column)

**Typography:**
- Font family
- Font size (px)
- Font weight
- Line height
- Letter spacing
- Text alignment

**Colors:**
- Fill colors (hex/rgba)
- Stroke colors
- Background colors
- Text colors

**Borders:**
- Border width
- Border radius
- Border color

**Effects:**
- Box shadows
- Opacity

## Mapping to Bootstrap 4

### Spacing
| Figma Value | Bootstrap Class |
|-------------|-----------------|
| 4px | `p-1`, `m-1` |
| 8px | `p-2`, `m-2` |
| 16px | `p-3`, `m-3` |
| 24px | `p-4`, `m-4` |
| 48px | `p-5`, `m-5` |

### Grid Columns
| Figma Width % | Bootstrap Class |
|---------------|-----------------|
| 8.33% | `col-1` |
| 16.67% | `col-2` |
| 25% | `col-3` |
| 33.33% | `col-4` |
| 50% | `col-6` |
| 66.67% | `col-8` |
| 75% | `col-9` |
| 100% | `col-12` |

### Typography Mapping
Map Figma text styles to Bootstrap:
```scss
// Figma: 32px Bold
.h1, h1 { font-size: 2rem; font-weight: 700; }

// Figma: 24px Bold  
.h2, h2 { font-size: 1.5rem; font-weight: 700; }

// Figma: 16px Regular
p, .body { font-size: 1rem; font-weight: 400; }

// Figma: 14px Regular
.small { font-size: 0.875rem; }
```

## Implementation Pattern

### Step 1: Analyze the frame
```
1. Get screenshot for visual reference
2. Get metadata for structure overview
3. Identify main container and sections
```

### Step 2: Build structure
```html
<!-- Match Figma frame structure -->
<div class="container">
  <div class="row">
    <!-- Map Figma auto-layout to Bootstrap grid -->
    <div class="col-md-6">...</div>
    <div class="col-md-6">...</div>
  </div>
</div>
```

### Step 3: Apply styles
```scss
// Extract from Figma design context
.component {
  // Spacing from Figma padding
  padding: 24px; // or use Bootstrap p-4
  
  // Colors from variable defs
  background-color: var(--bg-primary);
  color: var(--text-primary);
  
  // Border from design context
  border-radius: 8px;
}
```

### Step 4: Verify accuracy
```
1. Compare screenshot with implementation
2. Check responsive behavior
3. Validate spacing and alignment
4. Confirm typography matches
```

## Best Practices

### Design Analysis
1. Start with the outermost frame
2. Work inward, layer by layer
3. Identify repeating patterns
4. Note responsive breakpoints in design

### Code Generation
1. Use semantic HTML elements
2. Prefer Bootstrap utilities over custom CSS
3. Only add custom CSS when Bootstrap lacks the utility
4. Maintain mobile-first approach

### Pixel Perfect Tips
1. Match exact spacing values
2. Use design token colors, not approximations
3. Preserve font hierarchy from design
4. Respect auto-layout direction and gaps
5. Account for border-box sizing

## Common Patterns

### Card Component
```
Figma structure:
- Frame (card container)
  - Image
  - Frame (content)
    - Text (title)
    - Text (description)
    - Button

Bootstrap implementation:
<div class="card">
  <img class="card-img-top">
  <div class="card-body">
    <h5 class="card-title">
    <p class="card-text">
    <a class="btn btn-primary">
  </div>
</div>
```

### Grid of Items
```
Figma structure:
- Frame (grid container, auto-layout wrap)
  - Component instance (item) x N

Bootstrap implementation:
<div class="row">
  {% for item in items %}
  <div class="col-6 col-md-4 col-lg-3">
    <!-- item content -->
  </div>
  {% endfor %}
</div>
```

### Two-Column Layout
```
Figma structure:
- Frame (horizontal auto-layout)
  - Frame (left column)
  - Frame (right column)

Bootstrap implementation:
<div class="row">
  <div class="col-md-6"><!-- left --></div>
  <div class="col-md-6"><!-- right --></div>
</div>
```

## Error Handling

### Large Selections
If get_screenshot times out:
1. Select smaller sections
2. Process frame by frame
3. Combine results

### Missing Variables
If variable defs incomplete:
1. Extract colors from design context fills
2. Document as custom values
3. Create CSS variables manually

### Complex Components
If structure is too deep:
1. Get metadata first for overview
2. Process child nodes individually
3. Build bottom-up
