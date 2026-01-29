# Document Changes

You are documenting changes made to the TiendaNube theme project.

## Documentation Location

All documentation goes in `.claude/docs/`:

```
.claude/docs/
├── changelog.md        ← Chronological log of changes
├── components.md       ← Implemented components registry
├── figma-map.md        ← Figma node → Template mapping
└── decisions.md        ← Technical decisions and rationale
```

## Workflow

### Step 1: Gather Information

Ask the user (if not provided):

1. **What was implemented?** (component name, feature)
2. **Figma source?** (node ID, frame name)
3. **Files created/modified?** (list of .tpl, .css, settings)
4. **Any technical decisions?** (why this approach)

Or detect from recent conversation context.

### Step 2: Update changelog.md

Append to `.claude/docs/changelog.md`:

```markdown
## [YYYY-MM-DD]

### [Component/Feature Name]

- **Figma**: `[node-id]` - [Frame name]
- **Files created**:
  - `templates/snipplets/[path].tpl`
  - CSS added to `static/css/[file].css`
- **Files modified**:
  - `config/settings.txt` - Added `[prefix]_*` settings
  - `config/translations.txt` - Added [N] translations
- **Settings added**:
  - `[prefix]_show` - Toggle visibility
  - `[prefix]_title` - Section title
  - ...
- **Notes**: [Any relevant context]
```

### Step 3: Update components.md

Add or update entry in `.claude/docs/components.md`:

````markdown
## [Component Name]

| Property        | Value                                     |
| --------------- | ----------------------------------------- |
| Type            | Reusable / Section-specific               |
| File            | `templates/snipplets/[path].tpl`          |
| Figma           | `[node-id]`                               |
| Settings Prefix | `[prefix]_`                               |
| Status          | ✅ Complete / 🚧 In Progress / 📋 Planned |

### Parameters (if reusable)

| Parameter  | Type   | Required | Default | Description  |
| ---------- | ------ | -------- | ------- | ------------ |
| product    | object | ✅       | -       | Product data |
| show_price | bool   |          | true    | Show price   |

### Settings (if section-specific)

| Setting          | Type     | Description    |
| ---------------- | -------- | -------------- |
| `[prefix]_show`  | checkbox | Toggle section |
| `[prefix]_title` | text     | Section title  |

### Usage

```twig
{% include 'snipplets/[path].tpl' with { ... } %}
```
````

### Notes

[Any implementation notes, gotchas, dependencies]

````

### Step 4: Update figma-map.md

Add mapping in `.claude/docs/figma-map.md`:

```markdown
| Figma Node | Frame Name | Template | Status |
|------------|------------|----------|--------|
| `7019:24065` | Hero Banner | `snipplets/home/home-hero.tpl` | ✅ |
| `7019:24100` | Featured Products | `snipplets/home/home-featured.tpl` | 🚧 |
| `7019:24200` | Product Card | `snipplets/components/product-card.tpl` | 📋 |
````

### Step 5: Update decisions.md (if applicable)

Only if there was a significant technical decision:

```markdown
## [YYYY-MM-DD] - [Decision Title]

### Context

[What was the situation/problem]

### Options Considered

1. **Option A**: [Description] - Pros/Cons
2. **Option B**: [Description] - Pros/Cons

### Decision

[What was decided and why]

### Consequences

[What this means going forward]
```

## Quick Documentation

For simple changes, user can just say:

- "Documentá el hero banner que acabamos de hacer"
- "Doc: implementé product card desde Figma 7019:24065"

And you extract the info from conversation context.

## Initialize Documentation

If `.claude/docs/` doesn't exist, create it with these starter files:

### changelog.md

```markdown
# Changelog

Registro cronológico de cambios en el theme.

---
```

### components.md

```markdown
# Components Registry

Registro de componentes implementados en el theme.

---

## Index

| Component | Type | File | Status |
| --------- | ---- | ---- | ------ |

---
```

### figma-map.md

```markdown
# Figma → Template Mapping

Mapeo de nodos de Figma a templates del theme.

**Figma File**: [URL]

---

| Figma Node | Frame Name | Template | Status |
| ---------- | ---------- | -------- | ------ |
```

### decisions.md

```markdown
# Technical Decisions

Registro de decisiones técnicas importantes.

---
```

## Output

After documenting, confirm:

```
✅ Documentado en .claude/docs/

📝 changelog.md - Agregada entrada [fecha]
📦 components.md - [Agregado/Actualizado] [Component Name]
🗺️ figma-map.md - Mapeado [node-id] → [template]

¿Algo más que documentar?
```
