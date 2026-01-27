# Component Template

## Snippet: shipping-geodata.liquid

`snippets/shipping-geodata.liquid`

```liquid
{% comment %}
  Geodata for shipping estimator
  Structure: { country_code, regions: [{ name, code, cities: [{ name, code }] }] }
{% endcomment %}

<script type="application/json" data-shipping-geodata>
{
  "country_code": "XX",
  "regions": []
}
</script>
```

Reemplazar el JSON con los datos proporcionados por el usuario.

---

## Section: shipping-estimator.liquid

`sections/shipping-estimator.liquid`

```liquid
<div class="shipping-estimator" data-section-id="{{ section.id }}">
  {% if section.settings.section_title != blank %}
    <h3 class="shipping-estimator__title">{{ section.settings.section_title }}</h3>
  {% endif %}

  <shipping-estimator data-variant-id="{{ product.selected_or_first_available_variant.id }}">
    {% render 'shipping-geodata' %}

    <div class="shipping-estimator__form">
      <div class="shipping-estimator__field">
        <label for="shipping-region-{{ section.id }}">
          {{ section.settings.region_label }}
        </label>
        <select id="shipping-region-{{ section.id }}" data-region-select required>
          <option value="">{{ section.settings.region_label }}</option>
        </select>
      </div>

      <div class="shipping-estimator__field">
        <label for="shipping-city-{{ section.id }}">
          {{ section.settings.city_label }}
        </label>
        <select id="shipping-city-{{ section.id }}" data-city-select disabled required>
          <option value="">{{ section.settings.city_label }}</option>
        </select>
      </div>
    </div>

    <div class="shipping-estimator__loading" data-loading hidden>
      {{ section.settings.loading_text }}
    </div>

    <div class="shipping-estimator__error" data-error hidden>
      {{ section.settings.error_text }}
    </div>

    <div class="shipping-estimator__results" data-results hidden>
      <ul data-rates-list></ul>
    </div>

    <div class="shipping-estimator__no-rates" data-no-rates hidden>
      {{ section.settings.no_rates_text }}
    </div>
  </shipping-estimator>
</div>

{% style %}
  .shipping-estimator {
    padding: 1rem;
    border: 1px solid rgba(0, 0, 0, 0.1);
    border-radius: 4px;
  }

  .shipping-estimator__title {
    margin: 0 0 1rem;
    font-size: 1rem;
  }

  .shipping-estimator__form {
    display: flex;
    flex-direction: column;
    gap: 0.75rem;
  }

  .shipping-estimator__field label {
    display: block;
    margin-bottom: 0.25rem;
    font-size: 0.875rem;
  }

  .shipping-estimator__field select {
    width: 100%;
    padding: 0.5rem;
    border: 1px solid currentColor;
    border-radius: 4px;
    background: transparent;
    font-size: 1rem;
  }

  .shipping-estimator__field select:disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }

  .shipping-estimator__loading,
  .shipping-estimator__error,
  .shipping-estimator__no-rates {
    margin-top: 1rem;
    padding: 0.75rem;
    border-radius: 4px;
    font-size: 0.875rem;
  }

  .shipping-estimator__error {
    background: rgba(200, 0, 0, 0.1);
    color: #c00;
  }

  .shipping-estimator__results {
    margin-top: 1rem;
  }

  .shipping-estimator__results ul {
    list-style: none;
    padding: 0;
    margin: 0;
  }

  .shipping-estimator__rate {
    display: flex;
    justify-content: space-between;
    padding: 0.5rem 0;
    border-bottom: 1px solid rgba(0, 0, 0, 0.1);
  }

  .shipping-estimator__rate:last-child {
    border-bottom: none;
  }

  .shipping-estimator__rate-price {
    font-weight: 600;
  }

  @media (min-width: 750px) {
    .shipping-estimator__form {
      flex-direction: row;
    }

    .shipping-estimator__field {
      flex: 1;
    }
  }
{% endstyle %}

<script defer>
  class ShippingEstimator extends HTMLElement {
    constructor() {
      super();
      this.geodata = null;
      this.originalCart = null;
      this.maxPollingAttempts = 10;
      this.pollingInterval = 1000;
    }

    connectedCallback() {
      this.initElements();
      this.loadGeodata();
      this.bindEvents();
    }

    initElements() {
      this.regionSelect = this.querySelector('[data-region-select]');
      this.citySelect = this.querySelector('[data-city-select]');
      this.loadingEl = this.querySelector('[data-loading]');
      this.errorEl = this.querySelector('[data-error]');
      this.resultsEl = this.querySelector('[data-results]');
      this.ratesListEl = this.querySelector('[data-rates-list]');
      this.noRatesEl = this.querySelector('[data-no-rates]');
    }

    loadGeodata() {
      const script = this.querySelector('[data-shipping-geodata]');
      if (!script) return;

      try {
        this.geodata = JSON.parse(script.textContent);
        this.populateRegions();
      } catch (e) {
        console.error('Failed to parse geodata:', e);
      }
    }

    populateRegions() {
      if (!this.geodata?.regions) return;

      this.geodata.regions.forEach(region => {
        const option = document.createElement('option');
        option.value = region.code;
        option.textContent = region.name;
        this.regionSelect.appendChild(option);
      });
    }

    populateCities(regionCode) {
      const defaultLabel = this.citySelect.options[0].textContent;
      this.citySelect.innerHTML = `<option value="">${defaultLabel}</option>`;

      const region = this.geodata.regions.find(r => r.code === regionCode);
      if (!region?.cities) return;

      region.cities.forEach(city => {
        const option = document.createElement('option');
        option.value = city.code;
        option.textContent = city.name;
        this.citySelect.appendChild(option);
      });

      this.citySelect.disabled = false;
    }

    bindEvents() {
      this.regionSelect.addEventListener('change', (e) => {
        if (e.target.value) {
          this.populateCities(e.target.value);
        } else {
          this.citySelect.innerHTML = `<option value="">${this.citySelect.options[0]?.textContent || ''}</option>`;
          this.citySelect.disabled = true;
        }
        this.hideAllMessages();
      });

      this.citySelect.addEventListener('change', (e) => {
        if (e.target.value) {
          this.calculateShipping();
        }
      });
    }

    hideAllMessages() {
      this.loadingEl.hidden = true;
      this.errorEl.hidden = true;
      this.resultsEl.hidden = true;
      this.noRatesEl.hidden = true;
    }

    showLoading() {
      this.hideAllMessages();
      this.loadingEl.hidden = false;
    }

    showError() {
      this.hideAllMessages();
      this.errorEl.hidden = false;
    }

    showResults(rates) {
      this.hideAllMessages();

      if (!rates?.length) {
        this.noRatesEl.hidden = false;
        return;
      }

      this.ratesListEl.innerHTML = rates.map(rate => `
        <li class="shipping-estimator__rate">
          <span class="shipping-estimator__rate-name">${rate.name}</span>
          <span class="shipping-estimator__rate-price">${this.formatPrice(rate.price)}</span>
        </li>
      `).join('');
      this.resultsEl.hidden = false;
    }

    formatPrice(cents) {
      const price = parseFloat(cents);
      if (price === 0) return 'Gratis';

      return new Intl.NumberFormat(undefined, {
        style: 'currency',
        currency: Shopify.currency?.active || 'USD'
      }).format(price / 100);
    }

    async calculateShipping() {
      const variantId = this.dataset.variantId;
      const regionCode = this.regionSelect.value;
      const cityCode = this.citySelect.value;

      if (!variantId || !regionCode || !cityCode) return;

      const region = this.geodata.regions.find(r => r.code === regionCode);
      const city = region?.cities.find(c => c.code === cityCode);

      this.showLoading();

      try {
        // Step 1: Backup current cart
        this.originalCart = await this.fetchCart();

        // Step 2: Clear cart
        await this.clearCart();

        // Step 3: Add current variant
        await this.addToCart(variantId, 1);

        // Step 4: Prepare shipping rates (POST)
        await this.prepareShippingRates({
          country: this.geodata.country_code,
          province: region?.name || regionCode,
          city: city?.name || cityCode,
          zip: city?.code || ''
        });

        // Step 5: Poll for shipping rates (GET)
        const rates = await this.pollShippingRates();

        // Steps 6-7: Clear and restore cart
        await this.clearCart();
        await this.restoreCart();

        // Step 8: Render results
        this.showResults(rates);

      } catch (error) {
        console.error('Shipping calculation failed:', error);

        try {
          await this.clearCart();
          await this.restoreCart();
        } catch (e) {
          console.error('Failed to restore cart:', e);
        }

        this.showError();
      }
    }

    async fetchCart() {
      const res = await fetch('/cart.js');
      if (!res.ok) throw new Error('Failed to fetch cart');
      return res.json();
    }

    async clearCart() {
      const res = await fetch('/cart/clear.js', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' }
      });
      if (!res.ok) throw new Error('Failed to clear cart');
      return res.json();
    }

    async addToCart(variantId, quantity) {
      const res = await fetch('/cart/add.js', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          items: [{ id: parseInt(variantId), quantity }]
        })
      });
      if (!res.ok) throw new Error('Failed to add to cart');
      return res.json();
    }

    async prepareShippingRates(address) {
      const params = new URLSearchParams({
        'shipping_address[country]': address.country,
        'shipping_address[province]': address.province,
        'shipping_address[city]': address.city,
        'shipping_address[zip]': address.zip || ''
      });

      await fetch(`/cart/prepare_shipping_rates.json?${params}`, {
        method: 'POST'
      });

      // Wait briefly for carrier service to start calculation
      await new Promise(r => setTimeout(r, 1000));
    }

    async pollShippingRates() {
      for (let i = 0; i < this.maxPollingAttempts; i++) {
        const res = await fetch('/cart/async_shipping_rates.json');

        if (res.ok) {
          const data = await res.json();
          if (data.shipping_rates?.length) {
            return data.shipping_rates;
          }
        }

        await new Promise(r => setTimeout(r, this.pollingInterval));
      }

      return [];
    }

    async restoreCart() {
      if (!this.originalCart?.items?.length) return;

      const items = this.originalCart.items.map(item => ({
        id: item.variant_id,
        quantity: item.quantity,
        properties: item.properties
      }));

      await fetch('/cart/add.js', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ items })
      });
    }
  }

  if (!customElements.get('shipping-estimator')) {
    customElements.define('shipping-estimator', ShippingEstimator);
  }
</script>

{% schema %}
{
  "name": "Shipping Estimator",
  "settings": [
    {
      "type": "header",
      "content": "Content"
    },
    {
      "type": "text",
      "id": "section_title",
      "label": "Section title",
      "default": "Calcular envio"
    },
    {
      "type": "header",
      "content": "Labels"
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
      "type": "header",
      "content": "Messages"
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
      "default": "No pudimos calcular el envio. Intenta nuevamente."
    },
    {
      "type": "text",
      "id": "no_rates_text",
      "label": "No rates available message",
      "default": "No hay tarifas disponibles para esta ubicacion."
    }
  ],
  "presets": [
    {
      "name": "Shipping Estimator"
    }
  ]
}
{% endschema %}
```

---

## Variante con Assets Externos

Para proyectos mas grandes, separar JS y CSS en archivos externos:

### assets/shipping-estimator.js

Mover el contenido de `<script>` al archivo JS externo.

### assets/shipping-estimator.css

Mover el contenido de `{% style %}` al archivo CSS externo.

### Inclusion en section

```liquid
{{ 'shipping-estimator.css' | asset_url | stylesheet_tag }}

{%- comment -%} HTML aqui {%- endcomment -%}

<script src="{{ 'shipping-estimator.js' | asset_url }}" defer></script>

{% schema %}
...
{% endschema %}
```

---

## Archivos a Generar

| Archivo | Obligatorio | Descripcion |
|---------|-------------|-------------|
| `snippets/shipping-geodata.liquid` | Si | JSON con datos geograficos |
| `sections/shipping-estimator.liquid` | Si | Section principal |
| `assets/shipping-estimator.js` | Opcional | JS externo (si es complejo) |
| `assets/shipping-estimator.css` | Opcional | CSS externo (si es extenso) |
