---
name: shopify-liquid-shipping-estimator
description: |
  Creacion de secciones Shopify Liquid para estimacion de costos de envio
  en paginas de producto (PDP). Genera componentes con seleccion geografica
  de dos pasos (Region/Ciudad) y calculo de tarifas via API de carrito.
  Triggers: "shipping estimator", "cotizador de envio", "calcular envio",
  "estimador de flete", "shipping calculator", "tarifas de envio PDP",
  "costos de envio en producto", "shipping rates section"
---

# Shopify Liquid Shipping Estimator

## Paso Obligatorio

Antes de generar CUALQUIER codigo, SIEMPRE ejecutar:

```
mcp__shopify-dev__learn_shopify_api(api: "liquid")
```

## Prerequisito: Datos Geograficos

ANTES de generar el componente, PREGUNTAR al usuario:

> Para generar el estimador de envio necesito los datos geograficos (regiones y ciudades/comunas).
> Por favor proporciona:
> 1. El JSON con la estructura de regiones y ciudades, O
> 2. La ruta del archivo si ya existe en el tema
>
> Ejemplo de estructura esperada:
> ```json
> {
>   "country_code": "CL",
>   "regions": [
>     {
>       "name": "Region Metropolitana",
>       "code": "RM",
>       "cities": [
>         { "name": "Santiago", "code": "santiago" },
>         { "name": "Providencia", "code": "providencia" }
>       ]
>     }
>   ]
> }
> ```

NO proceder hasta obtener esta informacion.

## Arquitectura del Componente

El estimador consiste en 2-4 archivos:

1. **Section principal**: `sections/shipping-estimator.liquid`
2. **Snippet de geodatos**: `snippets/shipping-geodata.liquid`
3. **JavaScript** (opcional): `assets/shipping-estimator.js` (si es complejo)
4. **CSS** (opcional): `assets/shipping-estimator.css` (si es extenso)

Para componentes simples, incluir JS y CSS inline en la section.

## Flujo de Calculo de Envio (CRITICO)

El componente DEBE seguir este patron exacto en orden:

1. **Backup del carrito actual** - GET `/cart.js`
2. **Limpiar carrito** - POST `/cart/clear.js`
3. **Agregar variante actual** - POST `/cart/add.js` con variant_id y quantity
4. **Preparar calculo** - POST `/cart/prepare_shipping_rates.json` con direccion
5. **Polling de tarifas** - GET `/cart/async_shipping_rates.json` (reintentar hasta obtener)
6. **Limpiar carrito** - POST `/cart/clear.js`
7. **Restaurar carrito original** - POST `/cart/add.js` para cada item del backup
8. **Renderizar resultados**

IMPORTANTE: El paso 4 (POST prepare) y 5 (GET polling) son OBLIGATORIOS en ese orden.

## Convenciones

### Idioma
- Labels de schema en INGLES: "Section title", "Region label"
- Textos por defecto pueden estar en el idioma del usuario

### Naming
- Custom element: `<shipping-estimator>`
- Clases CSS: BEM con prefijo `shipping-estimator__`
- IDs de settings: `snake_case`

### JavaScript
- Custom Element extendiendo HTMLElement
- Vanilla JS, ES6+ (async/await, arrow functions)
- Fetch API para todas las peticiones
- Manejo de estados: loading, error, success, no-rates

### CSS
- Usar `{% style %}...{% endstyle %}` inline para CSS pequeno
- Minimal styling - colores heredados del tema
- Variables CSS para personalizacion basica

### Schema Settings (Minimal)

Solo incluir configuracion de textos:

```json
{
  "settings": [
    {
      "type": "text",
      "id": "section_title",
      "label": "Section title",
      "default": "Calcular envio"
    },
    {
      "type": "text",
      "id": "region_label",
      "label": "Region select label",
      "default": "Selecciona tu region"
    },
    {
      "type": "text",
      "id": "city_label",
      "label": "City select label",
      "default": "Selecciona tu ciudad"
    },
    {
      "type": "text",
      "id": "loading_text",
      "label": "Loading message",
      "default": "Calculando tarifas..."
    },
    {
      "type": "text",
      "id": "error_text",
      "label": "Error message",
      "default": "No pudimos calcular el envio."
    },
    {
      "type": "text",
      "id": "no_rates_text",
      "label": "No rates message",
      "default": "No hay tarifas disponibles."
    }
  ],
  "presets": [
    {
      "name": "Shipping Estimator"
    }
  ]
}
```

## Workflow

1. Ejecutar `learn_shopify_api(api: "liquid")`
2. PREGUNTAR por datos geograficos (JSON o ruta de archivo)
3. Generar `snippets/shipping-geodata.liquid` con los datos
4. Generar `sections/shipping-estimator.liquid` usando template de referencia
5. Validar con `validate_theme`
6. Corregir errores y re-validar

## Templates

Ver [references/component-template.md](references/component-template.md) para:
- Estructura completa del section
- Custom Element JavaScript con flujo de calculo
- CSS minimal
- Schema completo

## Validacion

Despues de generar, SIEMPRE validar:

```
mcp__shopify-dev__validate_theme(
  conversationId: "<id>",
  absoluteThemePath: "<path>",
  filesCreatedOrUpdated: [
    { path: "sections/shipping-estimator.liquid" },
    { path: "snippets/shipping-geodata.liquid" }
  ]
)
```

Corregir errores y re-validar hasta que el codigo pase.

## Notas Importantes

- El componente es **country-agnostic**: funciona con cualquier estructura de pais
- El POST a `prepare_shipping_rates` DEBE hacerse ANTES del GET a `async_shipping_rates`
- El polling debe reintentar el GET hasta obtener tarifas (con limite de intentos)
- SIEMPRE restaurar el carrito original al finalizar, incluso si hay error
- Usar `try/catch/finally` para garantizar restauracion del carrito
