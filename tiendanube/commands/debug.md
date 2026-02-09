# TiendaNube Debug

You are debugging a TiendaNube/Nuvemshop theme template. The platform has incomplete documentation, so inspecting actual data at runtime is essential.

## CRITICAL: How Debugging Works in TiendaNube

TiendaNube templates use Twig. There is NO console, NO debugger, NO dev tools for template variables. The ONLY way to inspect data is rendering it in the HTML output.

**Primary technique:**

```twig
<pre style="background:#111;color:#0f0;padding:16px;font-size:12px;overflow:auto;max-height:400px;position:relative;z-index:9999;">
{{ variable | json_encode }}
</pre>
```

**Why `json_encode`:** It serializes the entire object tree — nested objects, arrays, booleans, nulls — into readable JSON. Without it, Twig renders objects as empty strings or throws errors.

---

## Workflow

### Step 1: Identify what to debug

Ask the user:

```
🔍 ¿QUÉ NECESITÁS DEBUGGEAR?

1. **¿Qué variable o dato no funciona como esperás?**
   - Ej: "el precio no se muestra", "no sé qué campos tiene el producto", "el carrito no muestra variantes"

2. **¿En qué template/página ocurre?**
   - Ej: product.tpl, home.tpl, cart.tpl, un snipplet específico

3. **¿Tenés URL de la tienda para probar?**
```

### Step 2: Insert debug output

Based on what the user needs, insert the appropriate debug snippet into the template. Use a clearly visible `<pre>` block so it's impossible to miss in the page.

**IMPORTANT:** Always wrap in a conditional comment or distinctive container so it's easy to find and remove later:

```twig
{# === DEBUG START === #}
<pre style="background:#111;color:#0f0;padding:16px;font-size:12px;overflow:auto;max-height:400px;position:relative;z-index:9999;border:3px solid #f00;">
  <strong>🔍 DEBUG: [variable_name]</strong>
  {{ variable_name | json_encode }}
</pre>
{# === DEBUG END === #}
```

### Step 3: Read the output

If Chrome MCP is available:

1. Navigate to the page
2. Use JS to extract the debug output:

```javascript
// Read debug output from the page
const debugEl = document.querySelector('pre[style*="background:#111"]');
debugEl ? debugEl.textContent : "DEBUG BLOCK NOT FOUND";
```

3. Parse the JSON and analyze the data structure
4. Report findings to the user

If Chrome MCP is NOT available:

- Tell the user to open the page and copy the JSON output
- Analyze whatever they paste

### Step 4: Document findings

After discovering the data structure, report it clearly:

```
📋 ESTRUCTURA DE [variable_name]:

{
  "id": 12345,
  "name": "Producto Ejemplo",
  "price": 9780,           ← precio en centavos
  "variants": [
    {
      "id": 67890,
      "name": "Talle M",
      "stock": 5,
      "price": 9780
    }
  ],
  "images": [...],
  "custom_fields": null     ← no está disponible en este contexto
}

**Hallazgos clave:**
- El precio viene en centavos, hay que dividir por 100
- `variants` es un array, no un objeto
- `custom_fields` es null en este template (probablemente solo disponible en product.tpl)
```

### Step 5: Clean up

**ALWAYS remind the user or remove debug blocks after finishing:**

```
🧹 LIMPIEZA: Recordá eliminar los bloques de debug antes de deployar.
Buscá en los archivos modificados: {# === DEBUG START === #}
```

---

## Common Debug Targets

### Product (use in product.tpl or any template with product variable)

```twig
{# === DEBUG START === #}
<pre style="background:#111;color:#0f0;padding:16px;font-size:12px;overflow:auto;max-height:400px;position:relative;z-index:9999;border:3px solid #f00;">
  <strong>🔍 DEBUG: product</strong>
  {{ product | json_encode }}
</pre>
{# === DEBUG END === #}
```

### Product Variants

```twig
{# === DEBUG START === #}
<pre style="background:#111;color:#0f0;padding:16px;font-size:12px;overflow:auto;max-height:400px;position:relative;z-index:9999;border:3px solid #f00;">
  <strong>🔍 DEBUG: product.variants</strong>
  {{ product.variants | json_encode }}
</pre>
{# === DEBUG END === #}
```

### Cart

```twig
{# === DEBUG START === #}
<pre style="background:#111;color:#0f0;padding:16px;font-size:12px;overflow:auto;max-height:400px;position:relative;z-index:9999;border:3px solid #f00;">
  <strong>🔍 DEBUG: cart</strong>
  {{ cart | json_encode }}
</pre>
{# === DEBUG END === #}
```

### Store / Settings

```twig
{# === DEBUG START === #}
<pre style="background:#111;color:#0f0;padding:16px;font-size:12px;overflow:auto;max-height:400px;position:relative;z-index:9999;border:3px solid #f00;">
  <strong>🔍 DEBUG: store</strong>
  {{ store | json_encode }}
</pre>
{# === DEBUG END === #}
```

### Category (use in category.tpl)

```twig
{# === DEBUG START === #}
<pre style="background:#111;color:#0f0;padding:16px;font-size:12px;overflow:auto;max-height:400px;position:relative;z-index:9999;border:3px solid #f00;">
  <strong>🔍 DEBUG: category</strong>
  {{ category | json_encode }}
</pre>
{# === DEBUG END === #}
```

### Customer (use in account templates)

```twig
{# === DEBUG START === #}
<pre style="background:#111;color:#0f0;padding:16px;font-size:12px;overflow:auto;max-height:400px;position:relative;z-index:9999;border:3px solid #f00;">
  <strong>🔍 DEBUG: customer</strong>
  {{ customer | json_encode }}
</pre>
{# === DEBUG END === #}
```

### Current Page Context

```twig
{# === DEBUG START === #}
<pre style="background:#111;color:#0f0;padding:16px;font-size:12px;overflow:auto;max-height:400px;position:relative;z-index:9999;border:3px solid #f00;">
  <strong>🔍 DEBUG: page context</strong>
  template: {{ template }}
  page_title: {{ page_title | default('N/A') }}
  is_catalog: {{ settings.is_catalog | json_encode }}
  store_name: {{ store.name }}
</pre>
{# === DEBUG END === #}
```

### All Settings (caution: large output)

```twig
{# === DEBUG START === #}
<pre style="background:#111;color:#0f0;padding:16px;font-size:12px;overflow:auto;max-height:400px;position:relative;z-index:9999;border:3px solid #f00;">
  <strong>🔍 DEBUG: settings (all)</strong>
  {{ settings | json_encode }}
</pre>
{# === DEBUG END === #}
```

### Specific Setting Value

```twig
{# === DEBUG START === #}
<pre style="background:#111;color:#0f0;padding:16px;font-size:12px;overflow:auto;max-height:400px;position:relative;z-index:9999;border:3px solid #f00;">
  <strong>🔍 DEBUG: specific setting</strong>
  home_order_position_0: {{ settings.home_order_position_0 | default('NOT SET') }}
  home_featured_show: {{ settings.home_featured_show | json_encode }}
</pre>
{# === DEBUG END === #}
```

---

## Advanced Techniques

### Debug a loop (show each item's structure)

```twig
{# === DEBUG START === #}
{% for item in products %}
  <pre style="background:#111;color:#0f0;padding:8px;font-size:11px;overflow:auto;max-height:200px;border:1px solid #f00;margin:4px 0;">
    <strong>🔍 product[{{ loop.index0 }}]</strong>
    {{ item | json_encode }}
  </pre>
{% endfor %}
{# === DEBUG END === #}
```

### Debug a variable that might not exist

```twig
{# === DEBUG START === #}
<pre style="background:#111;color:#0f0;padding:16px;font-size:12px;overflow:auto;max-height:400px;position:relative;z-index:9999;border:3px solid #f00;">
  <strong>🔍 DEBUG: variable check</strong>
  is defined: {{ variable_name is defined | json_encode }}
  is null: {{ variable_name is null | json_encode }}
  is empty: {{ variable_name is empty | json_encode }}
  value: {{ variable_name | default('UNDEFINED') | json_encode }}
</pre>
{# === DEBUG END === #}
```

### Debug what filters return

```twig
{# === DEBUG START === #}
<pre style="background:#111;color:#0f0;padding:16px;font-size:12px;overflow:auto;max-height:400px;position:relative;z-index:9999;border:3px solid #f00;">
  <strong>🔍 DEBUG: filter comparison</strong>
  raw price: {{ product.price | json_encode }}
  money filter: {{ product.price | money }}
  price_raw: {{ product.price_raw | default('NOT AVAILABLE') | json_encode }}
  compare_at_price: {{ product.compare_at_price | default('NOT AVAILABLE') | json_encode }}
</pre>
{# === DEBUG END === #}
```

### Debug JS variables (add to store.js.tpl or inline)

```javascript
// Debug LS object and cart state
console.log("=== TN DEBUG ===");
console.log("LS.store:", JSON.stringify(LS.store, null, 2));
console.log("LS.cart:", JSON.stringify(LS.cart, null, 2));
console.log("LS.theme:", JSON.stringify(LS.theme, null, 2));
```

### Deploy-safe conditional debug (only show for specific query param)

```twig
{% if params.debug == 'true' %}
{# === DEBUG START === #}
<pre style="background:#111;color:#0f0;padding:16px;font-size:12px;overflow:auto;max-height:400px;position:fixed;bottom:0;left:0;right:0;z-index:99999;border:3px solid #f00;">
  <strong>🔍 DEBUG MODE (add ?debug=true to URL)</strong>
  {{ product | json_encode }}
</pre>
{# === DEBUG END === #}
{% endif %}
```

---

## Common Gotchas

| Problem                        | Cause                                           | Solution                                                           |
| ------------------------------ | ----------------------------------------------- | ------------------------------------------------------------------ | ------------- |
| Empty output / nothing renders | Variable is an object without `__toString`      | Use `                                                              | json_encode`  |
| `json_encode` shows `{}`       | Object has no public properties in this context | Try accessing sub-properties: `variable.id`, `variable.name`       |
| Circular reference error       | Object references itself                        | Debug sub-properties individually instead of the root object       |
| `settings` output is huge      | Settings contains ALL theme config              | Debug specific settings by name: `settings.setting_name`           |
| Variable is `null`             | Not available in this template context          | Check which template you're in — variables are context-dependent   |
| Filter throws error            | Filter expects specific type                    | Check type first with `variable is iterable`, `variable is string` |
| Price shows as integer         | TiendaNube stores prices in centavos            | Divide by 100 or use `                                             | money` filter |

---

## Template Context Reference

Not all variables are available everywhere. Quick reference:

| Variable   | Available in                                   |
| ---------- | ---------------------------------------------- |
| `product`  | product.tpl, snipplets that receive it         |
| `products` | home.tpl (featured), category.tpl (listing)    |
| `category` | category.tpl                                   |
| `cart`     | cart.tpl, layout.tpl (global)                  |
| `customer` | account templates, layout.tpl (when logged in) |
| `store`    | All templates (global)                         |
| `settings` | All templates (global)                         |
| `page`     | page.tpl                                       |
| `order`    | order confirmation templates                   |

If a variable is `null` or undefined, you're probably in the wrong template context. Debug `template` to confirm which template is rendering.

---

## REMEMBER

1. **`json_encode` is your best friend** — always use it, never dump raw objects
2. **Wrap in `{# === DEBUG START/END === #}`** — makes cleanup easy
3. **Red border + fixed position** — makes debug blocks impossible to miss
4. **Clean up before deploy** — search for DEBUG START markers
5. **Document findings** — if you discover undocumented data structures, suggest adding them to the tiendanube-objects skill
