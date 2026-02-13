---
name: shopify-liquid-shipping-estimator
description: |
  Creacion de snippets Shopify Liquid para estimacion de costos de envio
  en paginas de producto (PDP) y carrito (cart drawer). Genera componentes
  con input de codigo postal y calculo de tarifas via API de carrito.
  Soporta dos contextos: PDP (backup/restore cart) y Cart (usa carrito actual).
  Triggers: "shipping estimator", "cotizador de envio", "calcular envio",
  "estimador de flete", "shipping calculator", "tarifas de envio PDP",
  "costos de envio en producto", "shipping rates section",
  "shipping estimator cart", "cotizador de envio en carrito"
---

# Shopify Liquid Shipping Estimator

## Paso Obligatorio

Antes de generar CUALQUIER codigo, SIEMPRE ejecutar:

```
mcp__shopify-dev__learn_shopify_api(api: "liquid")
```

## Prerequisito: Datos de Codigos Postales

ANTES de generar el componente, PREGUNTAR al usuario:

> Para generar el estimador de envio necesito los datos de codigos postales.
> Por favor proporciona:
> 1. El JSON con el mapeo de codigo postal a provincia/ciudad, O
> 2. La ruta del archivo si ya existe en el tema (ej: `snippets/zip-codes-json.liquid`)
>
> Ejemplo de estructura esperada:
> ```json
> {
>   "1000": { "province": "Buenos Aires", "city": "Capital Federal" },
>   "5000": { "province": "Cordoba", "city": "Cordoba" }
> }
> ```

NO proceder hasta obtener esta informacion.

## Arquitectura del Componente

El estimador tiene dos contextos de uso:

### 1. PDP (Pagina de Producto)
- Archivo: `snippets/shipping-estimator.liquid`
- Incluye CSS, HTML y JavaScript del Custom Element
- Usa flujo de backup/clear/restore del carrito
- Se pasa el product object: `{% render 'shipping-estimator', product: product %}`

### 2. Cart Drawer (Carrito)
- Archivo: `snippets/shipping-estimator-cart.liquid`
- Incluye CSS propio (NO hereda estilos del PDP) y HTML
- NO incluye JavaScript (reutiliza el Custom Element del PDP)
- Opera en "cart mode": calcula con el carrito actual sin backup/restore
- Se renderiza oculto y se mueve al contenedor Vue via `appendChild`
- Se incluye con: `{% render 'shipping-estimator-cart' %}`

### 3. Snippet de datos postales
- Archivo: `snippets/zip-codes-json.liquid`
- JSON con mapeo codigo_postal -> { province, city }

### Archivos totales

| Archivo | Obligatorio | Descripcion |
|---------|-------------|-------------|
| `snippets/shipping-estimator.liquid` | Si | Snippet PDP (CSS + HTML + JS) |
| `snippets/shipping-estimator-cart.liquid` | Si, si hay cart drawer | Snippet Cart (CSS + HTML, sin JS) |
| `snippets/zip-codes-json.liquid` | Si | JSON con datos postales |

## Flujo de Calculo de Envio (CRITICO)

### Modo PDP (backup/restore)

1. **Backup del carrito actual** - GET `/cart.js`
2. **Limpiar carrito** - POST `/cart/clear.js`
3. **Agregar variante actual** - POST `/cart/add.js` con variant_id y quantity: 1
4. **Preparar calculo** - POST `/cart/prepare_shipping_rates.json` con direccion
5. **Esperar** - delay 1500ms para que el carrier service procese
6. **Polling de tarifas** - GET `/cart/async_shipping_rates.json` (hasta 10 reintentos, 1s entre cada uno)
7. **Reintentar** - Si no hay rates, repetir pasos 4-6 hasta 3 veces
8. **Limpiar carrito** - POST `/cart/clear.js`
9. **Restaurar carrito** - POST `/cart/add.js` para cada item del backup
10. **Renderizar resultados**

### Modo Cart (sin backup)

1. **Preparar calculo** - POST `/cart/prepare_shipping_rates.json` con direccion
2. **Esperar** - delay 1500ms
3. **Polling de tarifas** - GET `/cart/async_shipping_rates.json`
4. **Reintentar** - Si no hay rates, repetir pasos 1-3 hasta 3 veces
5. **Renderizar resultados**

IMPORTANTE:
- El POST a `prepare_shipping_rates` DEBE hacerse ANTES del GET a `async_shipping_rates`
- En modo PDP: SIEMPRE restaurar el carrito original al finalizar, incluso si hay error
- En modo PDP: Deshabilitar botones de add-to-cart durante el calculo
- Usar `try/catch/finally` para garantizar restauracion del carrito

## Deteccion de Modo

El Custom Element detecta su contexto via `data-context`:

```html
<!-- PDP: sin data-context -->
<shipping-estimator data-variant-id="{{ current_variant.id }}">

<!-- Cart: con data-context="cart" -->
<shipping-estimator data-context="cart" id="ShippingEstimatorCart" style="display:none;">
```

El metodo `isCartMode()` verifica `this.dataset.context === 'cart'`.

## Leccion Aprendida: CSS Scoping en Cart Drawer

**CRITICO**: Cuando el snippet del cart se renderiza dentro de un componente Vue (cart drawer), los estilos CSS del snippet PDP NO estan disponibles. Esto porque:

1. El PDP snippet solo se carga en paginas de producto
2. El cart drawer se carga en todas las paginas

**Solucion**: El snippet del cart DEBE incluir su propio bloque `<style>` con TODOS los estilos necesarios, prefijados con `.cart--shipping-estimator` para evitar colisiones.

```html
<!-- En shipping-estimator-cart.liquid -->
<style>
  .cart--shipping-estimator .shipping-estimator { ... }
  .cart--shipping-estimator .shipping-estimator__form { ... }
  /* TODOS los estilos duplicados y prefijados */
</style>
```

## Integracion con Cart Drawer (Vue)

El snippet del cart se renderiza oculto en el HTML base y se mueve al componente Vue en `onMounted`:

```html
<!-- En el template Liquid del carrito -->
{% render 'shipping-estimator-cart' %}
```

```js
// En el componente Vue del carrito (ModalCart.vue)
const shippingEstimatorSlot = ref(null)

onMounted(() => {
    const el = document.getElementById('ShippingEstimatorCart')
    if (el && shippingEstimatorSlot.value) {
        shippingEstimatorSlot.value.appendChild(el)
        el.style.display = ''
    }
})
```

Si el proyecto no usa Vue, el snippet del cart se puede incluir directamente en el template del carrito.

## Convenciones

### Idioma
- Labels de schema en INGLES: "Section title", "Zip input placeholder"
- Textos por defecto en el idioma del usuario/tienda

### Naming
- Custom element: `<shipping-estimator>`
- Clases CSS: BEM con prefijo `shipping-estimator__`
- IDs de settings: `snake_case`

### JavaScript
- Custom Element class dentro de `if (!customElements.get('shipping-estimator'))` para evitar doble registro
- Vanilla JS, ES6+ (async/await)
- Fetch API para todas las peticiones
- `localStorage` para persistir ultimo codigo postal
- Manejo de estados: form (input), loading, error, results
- Escuchar `variant:changed` en modo PDP para actualizar variant_id

### CSS
- Estilos inline en `<style>` tag (no `{% style %}`)
- BEM naming
- Responsive con `@media (max-width: 749px)`
- `[hidden] { display: none !important; }` para estados

### Renderizado de Resultados

Los rates se separan en dos secciones:
- **Envio a domicilio** (delivery)
- **Retiro en punto** (pickup)

La clasificacion se hace por keywords en el nombre del rate:
```js
var pickupKeywords = ['retir', 'pick', 'sucursal', 'punto'];
```

Cada rate card muestra:
- Nombre del rate (bold)
- Descripcion
- Rango de entrega (formateado como "Llega entre el lunes 01/01 y el miercoles 03/01")
- Precio (o "GRATIS" en verde con precio original tachado si es gratuito)

## Workflow

1. Ejecutar `learn_shopify_api(api: "liquid")`
2. PREGUNTAR por datos de codigos postales (JSON o ruta de archivo)
3. Generar `snippets/zip-codes-json.liquid` si no existe
4. Generar `snippets/shipping-estimator.liquid` (PDP) usando template de referencia
5. Si hay cart drawer: generar `snippets/shipping-estimator-cart.liquid` (Cart)
6. Validar con `validate_theme`
7. Corregir errores y re-validar

## Templates

Ver [references/component-template.md](references/component-template.md) para:
- Snippet PDP completo (CSS + HTML + JavaScript Custom Element)
- Snippet Cart completo (CSS + HTML, sin JS)
- Integracion con Vue cart drawer

## Validacion

Despues de generar, SIEMPRE validar:

```
mcp__shopify-dev__validate_theme(
  conversationId: "<id>",
  absoluteThemePath: "<path>",
  filesCreatedOrUpdated: [
    { path: "snippets/shipping-estimator.liquid" },
    { path: "snippets/shipping-estimator-cart.liquid" },
    { path: "snippets/zip-codes-json.liquid" }
  ]
)
```

Corregir errores y re-validar hasta que el codigo pase.

## Notas Importantes

- El componente es **country-agnostic**: funciona con cualquier estructura de pais
- El POST a `prepare_shipping_rates` DEBE hacerse ANTES del GET a `async_shipping_rates`
- El polling debe reintentar con delay de 1.5s entre prepare y poll, y hasta 3 intentos completos
- SIEMPRE restaurar el carrito original al finalizar en modo PDP, incluso si hay error
- Un solo Custom Element sirve para ambos contextos (PDP y Cart) gracias a `isCartMode()`
- El snippet del cart DEBE tener sus propios estilos CSS (no hereda del PDP)
- En proyectos con Vue cart drawer: renderizar oculto y mover con `appendChild` en `onMounted`
- Usar `localStorage` para recordar el ultimo codigo postal del usuario
