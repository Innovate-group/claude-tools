# TiendaNube Component Implementation

You are implementing a component for a TiendaNube/Nuvemshop theme.

## CRITICAL: Interactive Workflow

**STOP and ASK before proceeding at each phase.** Do not assume. Do not skip questions. If in doubt, ASK.

---

## Phase 0: Gather Requirements (MANDATORY)

**Before doing ANYTHING, ask these questions. Wait for answers.**

### Essential Questions (Always Ask)

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
```

### If User Provides Figma URL/Node

After receiving Figma info:

1. Fetch the node using `figma_get_node()`
2. Analyze structure and nested components
3. **SHOW the user what you understood:**

```
📐 ANÁLISIS DEL DISEÑO:

**Estructura detectada:**
- Contenedor principal: [FRAME/SECTION name]
- Elementos hijos:
  - [TEXT] "Título de sección"
  - [FRAME] Grid de 4 columnas
    - [INSTANCE] Product Card (x4) ← Componente existente
  - [INSTANCE] Button "Ver más"

**Bootstrap equivalente:**
- Container → `.container`
- Grid 4 cols → `.row` + `.col-6.col-md-3`
- Cards → `{% include 'snipplets/product-card.tpl' %}`

**¿Esto coincide con lo que esperabas?** ¿Hay algo que deba ajustar?
```

---

## Phase 1: Check Existing Components

**Before creating ANYTHING, check what exists.**

```bash
# Check theme structure
ls -la templates/snipplets/
ls -la templates/snipplets/home/
ls -la templates/snipplets/components/
```

### Decision Point - ASK THE USER

If you find a similar component:

```
🔍 ENCONTRÉ COMPONENTES SIMILARES:

**Existente:** `snipplets/home/home-slider.tpl`
- Tiene: Swiper.js, settings para imágenes, autoplay
- Le falta: El nuevo diseño tiene texto overlay

**Opciones:**
A) **MODIFICAR** el existente - Agregar las nuevas funcionalidades
B) **CREAR VARIANTE** - `home-slider-v2.tpl` manteniendo el original
C) **REEMPLAZAR** - Crear nuevo y deprecar el anterior

**¿Qué preferís?** (Si no estás seguro, te recomiendo A o B para no romper nada existente)
```

If NO similar component exists:

```
✅ No encontré componentes similares. Voy a crear uno nuevo.

**Ubicación propuesta:** `snipplets/home/home-[nombre].tpl`
**Settings prefix:** `home_[nombre]_`

**¿Te parece bien esta ubicación y naming?**
```

---

## Phase 2: Define Component Type

Based on user answers, classify:

### Option A: Reusable Snipplet

**When:** Used in multiple places, same structure

```
📦 TIPO: Snipplet Reutilizable

**Características:**
- Se va a usar en: [lugares]
- Recibe datos por parámetros
- NO accede a settings directamente
- Es auto-contenido

**Archivo:** `snipplets/components/[nombre].tpl`

**Parámetros que voy a definir:**
┌─────────────┬─────────┬──────────┬─────────────────┐
│ Parámetro   │ Tipo    │ Required │ Default         │
├─────────────┼─────────┼──────────┼─────────────────┤
│ product     │ object  │ ✓        │ -               │
│ show_price  │ bool    │          │ true            │
│ image_size  │ string  │          │ 'medium'        │
└─────────────┴─────────┴──────────┴─────────────────┘

**¿Falta algún parámetro que necesites?**
```

### Option B: Section-Specific

**When:** Used once, tied to a page

```
📦 TIPO: Sección Específica

**Características:**
- Aparece solo en: [página]
- Lee settings directamente con prefix
- Puede incluir snipplets reutilizables

**Archivo:** `snipplets/[page]/[page]-[nombre].tpl`
**Settings prefix:** `[page]_[nombre]_`

**Settings que voy a crear:**
┌─────────────────────┬──────────┬────────────────┐
│ Setting             │ Tipo     │ Default        │
├─────────────────────┼──────────┼────────────────┤
│ [prefix]_show       │ checkbox │ true           │
│ [prefix]_title      │ i18n     │ "Título"       │
│ [prefix]_image_desk │ image    │ -              │
│ [prefix]_image_mob  │ image    │ -              │
│ [prefix]_margin_top │ dropdown │ 'none'         │
│ [prefix]_margin_bot │ dropdown │ 'medium'       │
│ [prefix]_padding_*  │ dropdown │ 'medium'       │
└─────────────────────┴──────────┴────────────────┘

**¿Hay algo más que deba ser editable desde el admin?**
```

---

## Phase 3: Confirm Before Implementation

**Show the complete plan BEFORE writing any code:**

````
📋 PLAN DE IMPLEMENTACIÓN:

**Archivos a crear/modificar:**
1. `snipplets/home/home-hero.tpl` (NUEVO)
2. `config/settings.txt` (AGREGAR sección)
3. `config/defaults.txt` (AGREGAR valores)
4. `config/translations.txt` (AGREGAR textos)
5. `static/css/components/_hero.scss` (NUEVO - si hace falta)

**Estructura del componente:**
```twig
<section class="home-hero {{ spacing_classes }}" data-store="home-hero">
  <div class="container">
    <picture>
      <source media="(max-width: 767px)" srcset="mobile.jpg">
      <img src="desktop.jpg" alt="...">
    </picture>
  </div>
</section>
````

**Settings (8 campos):**

- Show/hide, título, imagen desktop, imagen mobile
- Alt text, link, margin top/bottom, padding top/bottom

**¿Todo OK? ¿Procedo con la implementación?**

```

---

## Phase 4: Implementation

Only after user confirms, proceed with:

### Required Skills to Read First
```

1. /mnt/skills/user/tiendanube-bootstrap-frontend/SKILL.md
2. /mnt/skills/user/tiendanube-theme-config/SKILL.md
3. /mnt/skills/user/tiendanube-objects/SKILL.md
4. /mnt/skills/user/twig-advanced/SKILL.md

```

### Implementation Checklist

**Template (.tpl):**
- [ ] Bootstrap 4.5 grid (container, row, col-*)
- [ ] `data-store` attribute for app hooks
- [ ] `js-` prefix for JS hooks
- [ ] Proper escaping (`| e`, `| raw` only when safe)
- [ ] Lazy loading on images
- [ ] Responsive images (picture + source)

**Settings (MANDATORY for image components):**
- [ ] Show/hide checkbox
- [ ] Desktop image
- [ ] Mobile image
- [ ] Alt text (i18n_input)
- [ ] Link (optional)
- [ ] Margin top/bottom dropdowns
- [ ] Padding top/bottom dropdowns

**Translations:**
- [ ] All 4 languages (es, pt, en, es_mx)
- [ ] Match exactly in templates

**CSS (if needed):**
- [ ] Mobile-first approach
- [ ] BEM naming or Bootstrap utilities
- [ ] No !important unless absolutely necessary

---

## Phase 5: Deliver & Document

After implementation, provide:

```

✅ IMPLEMENTACIÓN COMPLETA

**Archivos creados:**

1. `snipplets/home/home-hero.tpl`
2. Fragmento para `settings.txt`
3. Fragmento para `defaults.txt`
4. Fragmento para `translations.txt`

**Cómo usar:**

```twig
{% include 'snipplets/home/home-hero.tpl' %}
```

**Settings disponibles en admin:**

- Mostrar/ocultar sección
- Imagen desktop (1920x600)
- Imagen mobile (768x900)
- Texto alternativo
- Link del banner
- Espaciado (margin/padding)

**¿Querés que documente esto en `.claude/docs/`?** (Puedo usar el comando /doc)

````

---

## Quick Reference: When to Ask

| Situation | Action |
|-----------|--------|
| Missing Figma/design info | ASK for source |
| Similar component exists | ASK modify vs create |
| Unclear if reusable or section | ASK about usage |
| Settings not specified | ASK what should be editable |
| Mobile design not provided | ASK or propose interpretation |
| Naming unclear | PROPOSE and confirm |
| Breaking changes possible | WARN and ask |
| Implementation ready | SHOW plan, wait for OK |

---

## Naming Conventions

- **Files**: `kebab-case.tpl`
- **Settings prefix**: `snake_case_`
- **CSS classes**: `kebab-case`
- **JS hooks**: `js-kebab-case`
- **Data store**: `data-store="kebab-case"`

---

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
````

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

---

## REMEMBER

1. **ASK first, code later**
2. **Show what you understood** before implementing
3. **Propose and confirm** naming and structure
4. **Always include** responsive images + spacing controls
5. **Document** what you created
