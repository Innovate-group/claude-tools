# TiendaNube Component Implementation

You are implementing a component for a TiendaNube/Nuvemshop theme.

## CRITICAL RULES

1. **NEVER implement directly from Figma.** Always extract a spec file first.
2. **STOP and ASK before proceeding at each phase.** Do not assume. Do not skip questions.
3. **Maximum 3 visual correction iterations.** If it doesn't converge, report deviations and stop.
4. **One component per conversation.** Keep context focused.

---

## Phase 0: Gather Requirements (MANDATORY)

**Before doing ANYTHING, ask these questions. Wait for answers.**

```
🎯 ANTES DE EMPEZAR, necesito algunas respuestas:

1. **Fuente del diseño:**
   - ¿Tenés URL de Figma o node ID?
   - ¿O me describís el componente?

2. **Ubicación:**
   - ¿Dónde va este componente? (home, producto, categoría, header, footer, etc.)
   - ¿Es una sección completa o parte de otra sección?

3. **Reutilización:**
   - ¿Este componente se usa en UN solo lugar, o en VARIOS?
   - Si se usa en varios: ¿con la misma estructura o con variaciones?

4. **Componentes existentes:**
   - ¿Querés que revise si ya existe algo similar en el theme?
   - ¿O preferís crear desde cero?

5. **Alcance:**
   - ¿Solo markup (HTML/Twig) o también JavaScript?
   - ¿Necesita settings editables desde el admin?

6. **Estados interactivos:**
   - ¿Tiene hover, active, open/close, animaciones?
   - ¿Tiene comportamiento responsive diferente? (mobile menu, drawer, etc.)
```

---

## Phase 1: Extract Design Spec from Figma (MANDATORY when Figma is provided)

### IMPORTANT: This phase ONLY extracts data. NO code is written.

When the user provides a Figma URL or node ID:

### Fetching Strategy (adapt to component complexity)

**For simple components (button, card, banner):**

1. Use `get_design_context(nodeId)` to fetch the node data
2. Use `get_screenshot(nodeId)` to capture the visual reference

**For complex/large components (mega menu, header, full page sections):**

1. Use `get_metadata(nodeId)` FIRST to get the high-level node map
2. Identify the main child nodes from the metadata
3. Use `get_design_context(childNodeId)` for EACH major child separately
4. Use `get_screenshot(nodeId)` for the full component visual reference

This prevents truncated responses and gives more accurate data for complex components.

### Asset Handling

- If the Figma MCP returns a `localhost` source for images/SVGs, use that URL directly
- DO NOT create placeholder images or import icon packages
- DO NOT modify asset URLs returned by the Figma MCP server
- Download and store assets in the theme's `static/images/` directory

### Extract ALL design tokens from the Figma data

**Generate a spec file at `specs/[component-name].md`:**

````markdown
# Component Spec: [Component Name]

## Screenshot Reference

[Describe the visual layout based on the Figma screenshot]

## Layout Structure

- Container: [full-width / contained / max-width value]
- Grid system: [columns, gaps, alignment]
- Positioning: [static / absolute / fixed, z-index]

## Typography

| Element | Font Family | Weight  | Size (desktop) | Size (mobile) | Line Height | Letter Spacing | Color |
| ------- | ----------- | ------- | -------------- | ------------- | ----------- | -------------- | ----- |
| Title   | [value]     | [value] | [value]px      | [value]px     | [value]     | [value]        | #hex  |
| ...     | ...         | ...     | ...            | ...           | ...         | ...            | ...   |

## Colors

| Usage        | Hex  | Opacity | Notes |
| ------------ | ---- | ------- | ----- |
| Background   | #xxx | 100%    |       |
| Text primary | #xxx | 100%    |       |
| Border       | #xxx | 100%    |       |
| Hover state  | #xxx | 100%    |       |

## Spacing (all values in px)

| Property          | Desktop | Mobile  |
| ----------------- | ------- | ------- |
| Padding top       | [value] | [value] |
| Padding right     | [value] | [value] |
| Padding bottom    | [value] | [value] |
| Padding left      | [value] | [value] |
| Gap between items | [value] | [value] |
| Margin top        | [value] | [value] |
| Margin bottom     | [value] | [value] |

## Borders & Effects

- Border: [width] [style] [color]
- Border radius: [value]
- Box shadow: [value]
- Transitions: [property] [duration] [easing]

## Interactive States

| State  | What changes     | From → To   |
| ------ | ---------------- | ----------- |
| Hover  | background-color | #xxx → #yyy |
| Active | ...              | ...         |
| Open   | ...              | ...         |

## Responsive Behavior

| Breakpoint         | Changes    |
| ------------------ | ---------- |
| ≤ 767px (mobile)   | [describe] |
| 768-991px (tablet) | [describe] |
| ≥ 992px (desktop)  | [describe] |

## Component Children / Sub-components

- [List each distinct child element with its own mini-spec]

## HTML Structure (semantic, no styles yet)

```html
<nav class="mega-menu">
  <ul class="mega-menu__categories">
    <li class="mega-menu__category">
      <a href="#">Category Name</a>
      <div class="mega-menu__dropdown">...</div>
    </li>
  </ul>
</nav>
```
````

```

4. **SHOW the spec to the user and ask for confirmation:**

```

📐 SPEC EXTRAÍDA DEL FIGMA:

[Show the key measurements and structure]

**¿Esto coincide con lo que esperabas?**

- ¿Hay medidas que quieras ajustar?
- ¿Falta algún estado o breakpoint?
- ¿Los colores son correctos o usa variables del theme?

````

**DO NOT proceed to Phase 2 until the user confirms the spec.**

---

## Phase 2: Check Existing Components

**Before creating ANYTHING, check what exists.**

```bash
# Check theme structure
ls -la templates/snipplets/
ls -la templates/snipplets/home/
ls -la templates/snipplets/components/
````

### Decision Point - ASK THE USER

If you find a similar component:

```
🔍 ENCONTRÉ COMPONENTES SIMILARES:

**Existente:** `snipplets/[path]`
- Tiene: [what it has]
- Le falta: [what's missing for the new design]

**Opciones:**
A) **MODIFICAR** el existente
B) **CREAR VARIANTE** manteniendo el original
C) **REEMPLAZAR** creando nuevo

**¿Qué preferís?**
```

If NO similar component exists:

```
✅ No encontré componentes similares. Voy a crear uno nuevo.

**Ubicación propuesta:** `snipplets/[path]/[nombre].tpl`
**Settings prefix:** `[prefix]_`

**¿Te parece bien?**
```

---

## Phase 3: Define Component Type & Plan

Based on user answers, classify and present the full plan:

````
📋 PLAN DE IMPLEMENTACIÓN:

**Tipo:** [Snipplet Reutilizable / Sección Específica]

**Archivos a crear/modificar:**
1. `snipplets/[path]/[name].tpl` (NUEVO)
2. `config/settings.txt` (AGREGAR sección)
3. `config/defaults.txt` (AGREGAR valores)
4. `config/translations.txt` (AGREGAR textos)
5. CSS: [inline en .tpl / nuevo archivo .scss / agregar a existente]

**Estructura del componente:**
```twig
[Show proposed Twig structure]
````

**Settings (X campos):**
[List all settings]

**Implementación contra spec:** `specs/[component-name].md`

- Todas las medidas vienen del spec file
- NO voy a interpretar Figma en esta fase
- Cada valor CSS tiene origen trazable al spec

**¿Todo OK? ¿Procedo?**

```

**DO NOT proceed until the user confirms.**

---

## Phase 4: Implementation (Against the Spec, NOT Figma)

### Required Skills to Read First
```

1. /mnt/skills/user/tiendanube-bootstrap-frontend/SKILL.md
2. /mnt/skills/user/tiendanube-theme-config/SKILL.md
3. /mnt/skills/user/tiendanube-objects/SKILL.md
4. /mnt/skills/user/twig-advanced/SKILL.md

````

### CRITICAL: Implementation Rules

1. **Every CSS value must come from the spec file.** Do not invent values.
2. **Use the spec's HTML structure** as the semantic base.
3. **Mobile-first CSS** — start with mobile measurements from spec, use media queries for desktop.
4. **BEM naming** for component-specific styles, Bootstrap utilities where they match exactly.

### Implementation Checklist

**Template (.tpl):**
- [ ] Bootstrap 4.5 grid (container, row, col-*)
- [ ] `data-store` attribute for app hooks
- [ ] `js-` prefix for JS hooks
- [ ] Proper escaping (`| e`, `| raw` only when safe)
- [ ] Lazy loading on images
- [ ] Responsive images (picture + source)

**CSS:**
- [ ] All values match spec file exactly
- [ ] Mobile-first with breakpoints from spec
- [ ] No `!important` unless absolutely necessary
- [ ] Transitions/animations from spec

**Settings (if applicable):**
- [ ] Show/hide checkbox
- [ ] All configurable content fields
- [ ] Desktop + mobile images
- [ ] Alt text (i18n_input)
- [ ] Margin/padding dropdowns
- [ ] All 4 languages in translations

---

## Phase 5: Validation (Chrome MCP)

### CRITICAL: Two-layer validation. Automated checks FIRST, screenshot for the user LAST.

Claude Code cannot reliably compare two images pixel-by-pixel. DO NOT take a screenshot and try to "evaluate" if it matches the Figma design. Instead, use the two-layer approach below.

---

### Layer 1: Automated Computed Styles Check (Claude Code does this)

This is the PRIMARY validation. Use Chrome MCP to execute JavaScript that reads actual computed values and compares them against the spec file. This is deterministic and reliable.

**Step 1:** Open the local dev URL in Chrome MCP.

**Step 2:** For EACH element in the spec, run a JS snippet to read computed styles:

```javascript
// Build a validation function that checks all spec values at once
(function() {
  const results = [];

  function check(selector, property, expected) {
    const el = document.querySelector(selector);
    if (!el) {
      results.push({ selector, property, expected, actual: 'ELEMENT NOT FOUND', pass: false });
      return;
    }
    const styles = window.getComputedStyle(el);
    const actual = styles[property];
    // Normalize color values for comparison
    const pass = actual === expected ||
                 actual === expected + 'px' ||
                 actual.replace(/ /g, '') === expected.replace(/ /g, '');
    results.push({ selector, property, expected, actual, pass });
  }

  // === TYPOGRAPHY ===
  check('.mega-menu__title', 'fontSize', '14px');
  check('.mega-menu__title', 'fontWeight', '600');
  check('.mega-menu__title', 'lineHeight', '20px');
  check('.mega-menu__title', 'color', 'rgb(51, 51, 51)');

  // === SPACING ===
  check('.mega-menu__dropdown', 'paddingTop', '24px');
  check('.mega-menu__dropdown', 'paddingLeft', '32px');
  check('.mega-menu__item', 'marginBottom', '8px');

  // === BORDERS & EFFECTS ===
  check('.mega-menu__dropdown', 'borderRadius', '8px');
  check('.mega-menu__dropdown', 'boxShadow', '0px 4px 12px rgba(0, 0, 0, 0.1)');

  // === LAYOUT ===
  check('.mega-menu__dropdown', 'display', 'flex');
  check('.mega-menu__dropdown', 'gap', '24px');

  return JSON.stringify(results, null, 2);
})();
````

**IMPORTANT:** Adapt the selectors and expected values above to match the ACTUAL spec file for this component. The example uses mega-menu — replace with the real component's selectors and spec values.

**Step 3:** Report results as a checklist:

```
🔍 VALIDACIÓN AUTOMATIZADA - Computed styles vs spec:

✅ .mega-menu__title font-size: spec 14px → actual 14px
✅ .mega-menu__title font-weight: spec 600 → actual 600
❌ .mega-menu__dropdown padding-top: spec 24px → actual 16px
✅ .mega-menu__item margin-bottom: spec 8px → actual 8px

Resultado: 3/4 checks passed. 1 desviación encontrada.
```

**Step 4:** Fix any ❌ deviations.

**Step 5:** Re-run the SAME JS validation to confirm fixes.

**Step 6: Track convergence between rounds.** Report the delta so the user can see if fixes are actually working:

```
📈 CONVERGENCIA:

  Round 1: 8/12 checks passed (67%)
  Round 2: 11/12 checks passed (92%) → +3 fixed
  Round 3: 12/12 checks passed (100%) → +1 fixed ✅ Converged
```

**Convergence rules:**

- If a round fixes 0 checks (delta = 0), STOP. Something is structurally wrong. Report to user.
- If all checks pass, STOP. Done.
- **MAXIMUM 3 correction rounds.** If still failing after 3 rounds, report remaining deviations and stop.

**Step 7: Responsive check.** Resize the viewport to mobile (375px width) and run the same validation with mobile spec values:

```javascript
// Resize viewport first, then re-run checks with mobile expected values
window.innerWidth; // Confirm viewport size
```

---

### Layer 2: Screenshot for Human Review (User decides, NOT Claude Code)

After Layer 1 passes (or after 3 rounds), take screenshots and present them to the user. Claude Code does NOT evaluate these screenshots.

**Step 1:** Take desktop screenshot (1280px width).
**Step 2:** Take mobile screenshot (375px width).
**Step 3:** If the component has interactive states, trigger each state and screenshot:

- Hover state (simulate via JS: `element.classList.add('hover')` or trigger mouseenter)
- Open/expanded state
- Active state

**Step 4:** Present to the user:

```
📸 SCREENSHOTS PARA TU REVISIÓN:

1. Desktop (1280px): [screenshot]
2. Mobile (375px): [screenshot]
3. Hover state: [screenshot]

**Validación automatizada:** X/Y checks passed (ver detalle arriba)

¿Se ve bien? ¿Hay algo que ajustar visualmente que no se captura en los computed styles?
```

**The user makes the final visual call.** If the user spots something off, Claude Code asks "¿qué elemento y qué propiedad?" and adds it to the automated check for the next round.

---

### What Chrome MCP is FOR vs NOT FOR

| ✅ USE Chrome MCP for               | ❌ DO NOT use Chrome MCP for        |
| ----------------------------------- | ----------------------------------- |
| Reading computed styles via JS      | Comparing two screenshots visually  |
| Checking element existence in DOM   | Deciding if "it looks right"        |
| Verifying interactive states work   | Evaluating overall visual fidelity  |
| Measuring viewport-specific values  | Making subjective layout judgments  |
| Taking screenshots to SHOW the user | Taking screenshots to self-evaluate |

---

## Phase 6: Deliver & Document

After implementation and validation:

````
✅ IMPLEMENTACIÓN COMPLETA

**Archivos creados/modificados:**
1. `specs/[component-name].md` ← Design spec
2. `snipplets/[path]/[name].tpl` ← Component template
3. `config/settings.txt` ← Admin settings
4. `config/defaults.txt` ← Default values
5. `config/translations.txt` ← 4 languages

**Validación visual:**
- Desktop: [X/Y] checks passed
- Mobile: [X/Y] checks passed
- Deviaciones pendientes: [list or "ninguna"]

**Cómo usar:**
```twig
{% include 'snipplets/[path]/[name].tpl' %}
````

**¿Querés que documente esto con /doc?**

````

### Learning Log (MANDATORY after every component)

After delivering, append a brief entry to `specs/_learning-log.md` capturing what worked and what didn't. This compounds over time and improves future implementations.

```markdown
## [Component Name] - [Date]

**What worked well:**
- [e.g., "spec extraction was accurate, no corrections needed for typography"]
- [e.g., "Bootstrap grid mapped cleanly from the Figma layout"]

**What caused friction:**
- [e.g., "Figma MCP truncated the dropdown children, had to fetch 3 sub-nodes separately"]
- [e.g., "gap property not supported in Bootstrap 4, had to use margin workaround"]

**Spec accuracy:** [X/Y] checks passed on first validation round

**Total correction rounds:** [1-3]

**Reusable patterns discovered:**
- [e.g., "TiendaNube mega menus need z-index: 1050 to sit above the banner carousel"]
- [e.g., "product cards inside mega menu use the same snipplet as home grid"]

**Updates needed in workflow/skills:**
- [e.g., "Add gap → margin workaround to the Figma Translation table"]
- [e.g., "tiendanube-objects skill is missing the `category.banner` property"]
````

This log is the input for periodic reviews. Every 4-5 components, review `_learning-log.md` and update:

- The Figma Output Translation table (if new patterns emerged)
- The tiendanube-\* skills (if undocumented platform data was discovered)
- This command itself (if a phase consistently causes friction)

---

## Quick Reference

| Situation                         | Action                                  |
| --------------------------------- | --------------------------------------- |
| User provides Figma               | Extract spec FIRST, never code directly |
| Spec has ambiguous values         | ASK the user, don't guess               |
| Similar component exists          | ASK modify vs create                    |
| Implementation doesn't match spec | Fix up to 3 rounds, then report         |
| Mobile design not in Figma        | ASK or propose interpretation           |
| More than 3 correction rounds     | STOP and report remaining issues        |

## Naming Conventions

- **Files**: `kebab-case.tpl`
- **Spec files**: `specs/kebab-case.md`
- **Settings prefix**: `snake_case_`
- **CSS classes**: `kebab-case` with BEM (`block__element--modifier`)
- **JS hooks**: `js-kebab-case`
- **Data store**: `data-store="kebab-case"`

## Common Patterns

### Section with Spacing Controls

```twig
{% set spacing_classes = [
  'section-mt-' ~ settings.prefix_margin_top,
  'section-mb-' ~ settings.prefix_margin_bottom,
  'section-pt-' ~ settings.prefix_padding_top,
  'section-pb-' ~ settings.prefix_padding_bottom
] | join(' ') %}

<section class="component-name {{ spacing_classes }}" data-store="component-name">
  {# content #}
</section>
```

### Responsive Image with Picture

```twig
{% set has_desktop = store.has_theme_image('component_desktop.jpg') %}
{% set has_mobile = store.has_theme_image('component_mobile.jpg') %}

{% if has_desktop or has_mobile %}
<picture>
  {% if has_mobile %}
    <source media="(max-width: 767px)" srcset="{{ 'component_mobile.jpg' | static_url }}">
  {% endif %}
  {% if has_desktop %}
    <img src="{{ 'component_desktop.jpg' | static_url }}" alt="{{ settings.component_alt }}" class="img-fluid w-100" loading="lazy">
  {% endif %}
</picture>
{% endif %}
```

### Product Grid

```twig
<div class="row">
  {% for product in products | slice(0, settings.prefix_count) %}
    <div class="col-6 col-md-{{ 12 / settings.prefix_columns }}">
      {% include 'snipplets/product-card.tpl' with { product: product } %}
    </div>
  {% endfor %}
</div>
```

## Figma Output Translation

The Figma MCP server returns design context as React + Tailwind code. This is NOT the target output. You MUST translate:

| Figma MCP Output                              | TiendaNube Theme Equivalent                        |
| --------------------------------------------- | -------------------------------------------------- |
| React JSX                                     | Twig `.tpl` templates                              |
| Tailwind classes (`flex`, `gap-4`, `text-sm`) | Bootstrap 4.5 utilities + custom SCSS              |
| `className`                                   | `class`                                            |
| `useState`, `onClick`                         | jQuery event handlers in `store.js.tpl`            |
| CSS-in-JS / inline styles                     | SCSS in `style-async.scss.tpl` or component `.tpl` |
| `rem` units                                   | `px` (match spec values exactly)                   |
| Design tokens (Figma variables)               | `settings.*` from admin or hardcoded spec values   |

**Translation rules:**

- Tailwind `flex` → Bootstrap `d-flex`
- Tailwind `gap-*` → custom CSS gap or Bootstrap margin utilities
- Tailwind `grid-cols-*` → Bootstrap `row` + `col-*`
- Tailwind responsive `md:` → Bootstrap `col-md-*` or `@media (min-width: 768px)`
- Tailwind `text-*` sizes → explicit `font-size` from spec in px
- Tailwind `p-*`, `m-*` → explicit `padding`/`margin` from spec in px

NEVER copy Tailwind classes into the theme. ALWAYS translate to Bootstrap 4.5 or custom SCSS.

## REMEMBER

1. **SPEC FIRST** — Never go from Figma to code directly
2. **ASK first, code later** — Confirm at every phase
3. **Validate against numbers** — Not "does it look right?" but "is this 16px?"
4. **3 rounds max** — Don't enter infinite correction loops
5. **One component per conversation** — Keep it focused
6. **TRANSLATE, don't copy** — Figma MCP outputs React/Tailwind, you output Twig/Bootstrap/SCSS
7. **LOG what you learn** — Every component adds an entry to `specs/_learning-log.md`
