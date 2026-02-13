# Component Template

## Snippet: zip-codes-json.liquid

`snippets/zip-codes-json.liquid`

```liquid
{% comment %}
  Zip code to province/city mapping for shipping estimator.
  Structure: { "zip_code": { "province": "...", "city": "..." } }
{% endcomment %}

<script type="application/json" data-zip-codes>
{
  "1000": { "province": "Buenos Aires", "city": "Capital Federal" },
  "5000": { "province": "Cordoba", "city": "Cordoba" }
}
</script>
```

Reemplazar el JSON con los datos proporcionados por el usuario.

---

## Snippet PDP: shipping-estimator.liquid

`snippets/shipping-estimator.liquid`

```liquid
{% comment %}
  Renders a shipping cost estimator widget for the product page.
  Fetches shipping rates from Shopify's cart API using a postal code (CP).

  Accepts:
  - product: {Object} Product Liquid object (required)

  Usage:
  {% render 'shipping-estimator', product: product %}
{% endcomment %}

{%- liquid
  assign current_variant = product.selected_or_first_available_variant
  if current_variant == blank
    break
  endif
-%}

<style>
  .shipping-estimator {
    font-family: var(--font-century-gothic), sans-serif;
    width: 100%;
    margin-top: 16px;
  }

  .shipping-estimator__title {
    font-size: 14px;
    font-weight: 400;
    color: #000;
    margin: 0 0 8px;
    line-height: 22px;
  }

  .shipping-estimator__form {
    display: flex;
    align-items: center;
    gap: 0;
  }

  .shipping-estimator__input-wrapper {
    position: relative;
    flex: 1;
  }

  .shipping-estimator__input {
    width: 100%;
    height: 44px;
    border: 1px solid #ccc;
    border-right: none;
    padding: 0 40px 0 16px;
    font-family: inherit;
    font-size: 14px;
    color: #3d3d3d;
    outline: none;
    box-sizing: border-box;
    background: transparent;
  }

  .shipping-estimator__input::placeholder {
    color: #919191;
  }

  .shipping-estimator__input:focus {
    border-color: #3d3d3d;
    outline: none;
    box-shadow: none;
  }

  .shipping-estimator__submit {
    height: 44px;
    min-width: 87px;
    border: 1px solid #ccc;
    padding: 0 16px;
    font-family: inherit;
    font-size: 14px;
    color: #3d3d3d;
    background: transparent;
    cursor: pointer;
    line-height: 22px;
    white-space: nowrap;
  }

  .shipping-estimator__submit:hover {
    background: #f5f5f5;
  }

  .shipping-estimator__submit:disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }

  .shipping-estimator__help {
    margin-top: 8px;
  }

  .shipping-estimator__help a {
    font-size: 12px;
    color: #000;
    text-decoration: underline;
    line-height: 20px;
  }

  .shipping-estimator__loading {
    display: flex;
    align-items: center;
    gap: 8px;
    margin-top: 12px;
    font-size: 14px;
    color: #626262;
  }

  .shipping-estimator__spinner {
    width: 20px;
    height: 20px;
    border: 2px solid #ccc;
    border-top-color: #3d3d3d;
    border-radius: 50%;
    animation: se-spin 0.6s linear infinite;
  }

  @keyframes se-spin {
    to { transform: rotate(360deg); }
  }

  .shipping-estimator__error {
    margin-top: 12px;
    font-size: 12px;
    color: #c00;
    line-height: 16px;
  }

  .shipping-estimator__results-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 14px;
    color: #000;
    line-height: 22px;
    margin-bottom: 8px;
  }

  .shipping-estimator__results-cp {
    font-weight: 700;
  }

  .shipping-estimator__change-cp {
    font-size: 14px;
    color: #000;
    text-decoration: underline;
    cursor: pointer;
    background: none;
    border: none;
    font-family: inherit;
    padding: 0;
    line-height: 22px;
  }

  .shipping-estimator__warning {
    border: 1px solid #f8821a;
    padding: 8px 16px;
    font-size: 12px;
    color: #f8821a;
    line-height: 16px;
  }

  [data-results-state] > .shipping-estimator__results-header {
    margin-bottom: 8px;
  }

  [data-results-state] > .shipping-estimator__warning,
  [data-results-state] > [data-delivery-section],
  [data-results-state] > [data-pickup-section] {
    margin-top: 16px;
  }

  .shipping-estimator__section-header {
    display: flex;
    align-items: center;
    gap: 8px;
    margin-bottom: 8px;
  }

  .shipping-estimator__section-icon {
    width: 24px;
    height: 24px;
    flex-shrink: 0;
  }

  .shipping-estimator__section-icon svg {
    width: 24px;
    height: 24px;
    display: block;
  }

  .shipping-estimator__section-title {
    font-size: 14px;
    color: #000;
    line-height: 22px;
    font-weight: 400;
  }

  .shipping-estimator__rates {
    display: flex;
    flex-direction: column;
    gap: 8px;
  }

  .shipping-estimator__rate {
    display: flex;
    align-items: center;
    justify-content: space-between;
    background: #f9f9f9;
    padding: 8px 16px;
    gap: 16px;
  }

  .shipping-estimator__rate-info {
    display: flex;
    flex-direction: column;
    gap: 4px;
    min-width: 0;
  }

  .shipping-estimator__rate-name {
    font-size: 12px;
    font-weight: 700;
    color: #626262;
    line-height: 16px;
  }

  .shipping-estimator__rate-description {
    font-size: 12px;
    font-weight: 400;
    color: #626262;
    line-height: 16px;
  }

  .shipping-estimator__rate-delivery {
    font-size: 12px;
    font-weight: 400;
    color: #626262;
    line-height: 16px;
  }

  .shipping-estimator__rate-pricing {
    display: flex;
    flex-direction: column;
    align-items: flex-end;
    gap: 4px;
    flex-shrink: 0;
  }

  .shipping-estimator__rate-price {
    font-size: 15px;
    font-weight: 400;
    color: #626262;
    line-height: 16px;
    white-space: nowrap;
  }

  .shipping-estimator__rate-price--free {
    font-size: 12px;
    font-weight: 700;
    color: #2ec54a;
  }

  .shipping-estimator__rate-price--original {
    font-size: 12px;
    font-weight: 400;
    color: #626262;
    text-decoration: line-through;
    line-height: 16px;
    white-space: nowrap;
  }

  .shipping-estimator [hidden] {
    display: none !important;
  }

  @media (max-width: 749px) {
    .shipping-estimator__form {
      flex-wrap: wrap;
    }

    .shipping-estimator__input-wrapper {
      flex: 1 1 0%;
      min-width: 0;
    }

    .shipping-estimator__submit {
      flex-shrink: 0;
    }
  }
</style>

<shipping-estimator
  class="shipping-estimator"
  data-variant-id="{{ current_variant.id }}"
>
  {% render 'zip-codes-json' %}
  <div data-form-state>
    <p class="shipping-estimator__title">Medios de envío</p>
    <form class="shipping-estimator__form" data-shipping-form>
      <div class="shipping-estimator__input-wrapper">
        <input
          type="text"
          class="shipping-estimator__input"
          data-zip-input
          placeholder="Tu código postal"
          inputmode="numeric"
          maxlength="8"
          autocomplete="postal-code"
          required
        >
      </div>
      <button type="submit" class="shipping-estimator__submit" data-submit-btn>Calcular</button>
    </form>
    <div class="shipping-estimator__help">
      <a href="https://www.correoargentino.com.ar/formularios/cpa" target="_blank" rel="noopener noreferrer">No sé mi código postal</a>
    </div>
  </div>

  <div data-results-state hidden>
    <div class="shipping-estimator__results-header">
      <span>Entregas para el CP: <span class="shipping-estimator__results-cp" data-results-cp></span></span>
      <button type="button" class="shipping-estimator__change-cp" data-change-cp>Cambiar CP</button>
    </div>
    <div class="shipping-estimator__warning">
      IMPORTANTE: Corroborar bien tus medidas antes de comprar.<br>
      Encontrá tu talle adecuado en nuestra tabla de talles.
    </div>
    <div data-delivery-section hidden>
      <div class="shipping-estimator__section">
        <div class="shipping-estimator__section-header">
          <span class="shipping-estimator__section-icon" aria-hidden="true">
            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none">
              <path fill-rule="evenodd" clip-rule="evenodd" d="M21.94 8.55L20.89 7.5C20.39 6.99 19.71 6.7 19 6.69H15V6.11C15 4.59 13.77 3.36 12.25 3.36H4C3.59 3.36 3.25 3.7 3.25 4.11C3.25 4.52 3.59 4.86 4 4.86H12.22C12.91 4.86 13.47 5.42 13.47 6.11V12.44C13.47 12.85 13.81 13.19 14.22 13.19C14.63 13.19 14.97 12.85 14.97 12.44V8.19H18.97C19.3 8.19 19.62 8.32 19.85 8.56L20.9 9.56C21.04 9.71 21.15 9.89 21.2 10.09H17C16.59 10.09 16.25 10.43 16.25 10.84C16.25 11.25 16.59 11.59 17 11.59H21.25V15.84C21.26 16.18 21.14 16.51 20.9 16.76C20.67 17 20.34 17.14 20 17.14H18.89C18.64 15.99 17.62 15.16 16.45 15.16C15.27 15.16 14.25 15.99 14 17.14H8.66C8.39 15.95 7.29 15.13 6.07 15.21C4.85 15.29 3.88 16.25 3.77 17.47C3.66 18.69 4.46 19.8 5.65 20.09C6.84 20.39 8.06 19.77 8.53 18.64H14.14C14.53 19.57 15.44 20.17 16.45 20.17C17.45 20.17 18.36 19.57 18.75 18.64H20C21.52 18.64 22.75 17.41 22.75 15.89V10.5C22.75 9.77 22.46 9.07 21.94 8.55Z" fill="black"/>
              <path d="M10.75 9C10.74 8.59 10.41 8.26 10 8.25H2C1.59 8.25 1.25 8.59 1.25 9C1.25 9.41 1.59 9.75 2 9.75H10C10.41 9.74 10.74 9.41 10.75 9Z" fill="black"/>
              <path d="M10 11.25H5C4.59 11.25 4.25 11.59 4.25 12C4.25 12.41 4.59 12.75 5 12.75H10C10.41 12.75 10.75 12.41 10.75 12C10.75 11.59 10.41 11.25 10 11.25Z" fill="black"/>
            </svg>
          </span>
          <span class="shipping-estimator__section-title">Envío a domicilio</span>
        </div>
        <div class="shipping-estimator__rates" data-delivery-rates></div>
      </div>
    </div>
    <div data-pickup-section hidden>
      <div class="shipping-estimator__section">
        <div class="shipping-estimator__section-header">
          <span class="shipping-estimator__section-icon" aria-hidden="true">
            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none">
              <path d="M16.82 15.49C16.46 15.3 16.01 15.44 15.82 15.81C15.64 16.17 15.79 16.61 16.15 16.81C17.03 17.26 17.53 17.81 17.53 18.34C17.53 19.47 15.17 20.73 12.01 20.73C8.85 20.73 6.48 19.48 6.48 18.38C6.48 17.86 6.98 17.3 7.86 16.85C8.12 16.74 8.31 16.49 8.33 16.21C8.35 15.93 8.21 15.65 7.97 15.51C7.73 15.36 7.42 15.36 7.18 15.52C5.97 15.97 5.12 17.08 5 18.38C5 20.6 8 22.27 12 22.27C16 22.27 19 20.6 19 18.38C18.89 17.07 18.04 15.95 16.82 15.49Z" fill="black"/>
              <path fill-rule="evenodd" clip-rule="evenodd" d="M11.25 18.82V14.5C7.96 14.1 5.54 11.21 5.74 7.89C5.94 4.58 8.68 2 12 2C15.32 2 18.06 4.58 18.26 7.89C18.46 11.21 16.04 14.1 12.75 14.5V18.82C12.75 19.23 12.41 19.57 12 19.57C11.59 19.57 11.25 19.23 11.25 18.82Z" fill="black"/>
            </svg>
          </span>
          <span class="shipping-estimator__section-title">Retirar por</span>
        </div>
        <div class="shipping-estimator__rates" data-pickup-rates></div>
      </div>
    </div>
  </div>

  <div class="shipping-estimator__loading" data-loading hidden>
    <span class="shipping-estimator__spinner"></span>
    <span>Calculando tarifas...</span>
  </div>

  <div class="shipping-estimator__error" data-error hidden></div>
</shipping-estimator>

<script>
  if (!customElements.get('shipping-estimator')) {
    customElements.define('shipping-estimator', class extends HTMLElement {
      connectedCallback() {
        this.formState = this.querySelector('[data-form-state]');
        this.resultsState = this.querySelector('[data-results-state]');
        this.form = this.querySelector('[data-shipping-form]');
        this.input = this.querySelector('[data-zip-input]');
        this.submitBtn = this.querySelector('[data-submit-btn]');
        this.changeCpBtn = this.querySelector('[data-change-cp]');
        this.resultsCp = this.querySelector('[data-results-cp]');
        this.loadingEl = this.querySelector('[data-loading]');
        this.errorEl = this.querySelector('[data-error]');
        this.deliverySection = this.querySelector('[data-delivery-section]');
        this.pickupSection = this.querySelector('[data-pickup-section]');

        try {
          this.zipCodes = JSON.parse(this.querySelector('[data-zip-codes]').textContent);
        } catch (e) {
          this.zipCodes = {};
        }
        this.deliveryRates = this.querySelector('[data-delivery-rates]');
        this.pickupRates = this.querySelector('[data-pickup-rates]');

        this.form.addEventListener('submit', this.onSubmit.bind(this));
        this.changeCpBtn.addEventListener('click', this.onChangeCp.bind(this));
        if (this.dataset.context !== 'cart') {
          document.addEventListener('variant:changed', this.onVariantChanged.bind(this));
        }

        var saved = localStorage.getItem('shipping_estimator_zip');
        if (saved) this.input.value = saved;
      }

      onSubmit(e) {
        e.preventDefault();
        var zip = this.input.value.trim();
        if (!zip) return;
        localStorage.setItem('shipping_estimator_zip', zip);
        this.fetchRates(zip);
      }

      onChangeCp() {
        this.resultsState.hidden = true;
        this.errorEl.hidden = true;
        this.formState.hidden = false;
        this.input.focus();
        this.input.select();
      }

      onVariantChanged() {
        var variantInput = document.querySelector('input[name="id"]');
        if (variantInput) {
          this.dataset.variantId = variantInput.value;
          if (!this.resultsState.hidden) {
            this.resultsState.hidden = true;
            this.formState.hidden = false;
          }
        }
      }

      isCartMode() {
        return this.dataset.context === 'cart';
      }

      async fetchRates(zip) {
        if (!this.isCartMode()) {
          var variantId = parseInt(this.dataset.variantId);
          if (!variantId) return;
        }

        this.hideAll();
        this.loadingEl.hidden = false;
        this.submitBtn.disabled = true;

        var addToCartBtns = this.isCartMode() ? [] : document.querySelectorAll('[name="add"], .product-form__submit');
        addToCartBtns.forEach(function(btn) { btn.disabled = true; });

        var cartBackup = null;

        try {
          if (!this.isCartMode()) {
            cartBackup = await this.request('GET', '/cart.js');
            await this.request('POST', '/cart/clear.js');
            await this.request('POST', '/cart/add.js', { items: [{ id: variantId, quantity: 1 }] });
          }

          var zipData = this.getZipData(zip);

          this.addressParams = new URLSearchParams({
            'shipping_address[country]': 'Argentina',
            'shipping_address[province]': zipData.province,
            'shipping_address[city]': zipData.city,
            'shipping_address[zip]': zip,
            'shipping_address[address1]': 'test 123'
          });

          var prepareParams = new URLSearchParams(this.addressParams);
          prepareParams.set('include_carrier_rates', 'true');

          var rates = [];
          for (var attempt = 0; attempt < 3; attempt++) {
            await fetch('/cart/prepare_shipping_rates.json?' + prepareParams.toString(), {
              method: 'POST',
              headers: { 'Content-Type': 'application/x-www-form-urlencoded' }
            });
            await this.delay(1500);

            rates = await this.pollRates();
            if (rates.length > 0) break;
          }

          if (!this.isCartMode()) {
            await this.request('POST', '/cart/clear.js');
            await this.restoreCart(cartBackup);
          }

          this.renderResults(rates, zip);
        } catch (err) {
          console.error('Shipping estimator error:', err);

          if (!this.isCartMode()) {
            try {
              await this.request('POST', '/cart/clear.js');
              if (cartBackup) await this.restoreCart(cartBackup);
            } catch (restoreErr) {
              console.error('Cart restore error:', restoreErr);
            }
          }

          this.hideAll();
          this.formState.hidden = false;
          this.errorEl.hidden = false;
          this.errorEl.textContent = 'No pudimos calcular los costos de envío. Intentá de nuevo.';
        } finally {
          this.submitBtn.disabled = false;
          addToCartBtns.forEach(function(btn) { btn.disabled = false; });
        }
      }

      getZipData(zip) {
        var entry = this.zipCodes[String(zip)];
        return entry || { province: 'Buenos Aires', city: '' };
      }

      async request(method, url, body) {
        var opts = { method: method, headers: { 'Content-Type': 'application/json' } };
        if (body) opts.body = JSON.stringify(body);
        var res = await fetch(url, opts);
        if (!res.ok) throw new Error(url + ' failed: ' + res.status);
        return res.json();
      }

      async pollRates() {
        for (var i = 0; i < 10; i++) {
          try {
            var res = await fetch('/cart/async_shipping_rates.json?' + this.addressParams.toString());
            var text = await res.text();
            if (text && text !== 'null') {
              var data = JSON.parse(text);
              if (data && data.shipping_rates && data.shipping_rates.length > 0) {
                return data.shipping_rates;
              }
            }
          } catch (e) {}
          await this.delay(1000);
        }
        return [];
      }

      async restoreCart(backup) {
        if (!backup || !backup.items || !backup.items.length) return;
        var items = backup.items.map(function(item) {
          return { id: item.variant_id, quantity: item.quantity, properties: item.properties };
        });
        await this.request('POST', '/cart/add.js', { items: items });
      }

      delay(ms) {
        return new Promise(function(resolve) { setTimeout(resolve, ms); });
      }

      renderResults(rates, zip) {
        this.hideAll();
        this.resultsCp.textContent = zip;

        var pickupKeywords = ['retir', 'pick', 'sucursal', 'punto'];
        var delivery = [];
        var pickup = [];

        rates.forEach(function(rate) {
          var nameLower = rate.name.toLowerCase();
          var isPickup = pickupKeywords.some(function(kw) { return nameLower.indexOf(kw) !== -1; });
          if (isPickup) {
            pickup.push(rate);
          } else {
            delivery.push(rate);
          }
        });

        if (delivery.length > 0) {
          this.deliverySection.hidden = false;
          this.deliveryRates.innerHTML = delivery.map(this.renderRateCard.bind(this)).join('');
        } else {
          this.deliverySection.hidden = true;
        }

        if (pickup.length > 0) {
          this.pickupSection.hidden = false;
          this.pickupRates.innerHTML = pickup.map(this.renderRateCard.bind(this)).join('');
        } else {
          this.pickupSection.hidden = true;
        }

        if (rates.length === 0) {
          this.formState.hidden = false;
          this.errorEl.hidden = false;
          this.errorEl.textContent = 'No encontramos medios de envío para este código postal.';
          return;
        }

        this.formState.hidden = true;
        this.resultsState.hidden = false;
      }

      renderRateCard(rate) {
        var price = parseFloat(rate.price);
        var isFree = price === 0;
        var deliveryHtml = '';

        if (rate.delivery_range) {
          var min = rate.delivery_range.min;
          var max = rate.delivery_range.max;
          if (min && max) {
            deliveryHtml = '<span class="shipping-estimator__rate-delivery">' +
              this.formatDeliveryRange(min, max) + '</span>';
          }
        }

        var priceHtml = '';
        if (isFree) {
          priceHtml = '<span class="shipping-estimator__rate-price shipping-estimator__rate-price--free">GRATIS</span>';
          if (rate.original_price && parseFloat(rate.original_price) > 0) {
            priceHtml += '<span class="shipping-estimator__rate-price--original">' +
              this.formatPrice(rate.original_price) + '</span>';
          }
        } else {
          priceHtml = '<span class="shipping-estimator__rate-price">' + this.formatPrice(rate.price) + '</span>';
        }

        var desc = rate.description || rate.name;
        var descHtml = '<span class="shipping-estimator__rate-description">' + this.escapeHtml(desc) + '</span>';

        return '<div class="shipping-estimator__rate">' +
          '<div class="shipping-estimator__rate-info">' +
            '<span class="shipping-estimator__rate-name">' + this.escapeHtml(rate.name) + '</span>' +
            descHtml +
            deliveryHtml +
          '</div>' +
          '<div class="shipping-estimator__rate-pricing">' + priceHtml + '</div>' +
        '</div>';
      }

      formatPrice(priceStr) {
        var cents = parseFloat(priceStr);
        if (isNaN(cents)) return priceStr;
        var amount = cents / 100;
        return '$' + amount.toLocaleString('es-AR', { minimumFractionDigits: 0, maximumFractionDigits: 0 });
      }

      formatDeliveryRange(minDays, maxDays) {
        var days = ['domingo', 'lunes', 'martes', 'miércoles', 'jueves', 'viernes', 'sábado'];
        var now = new Date();
        var minDate = new Date(now);
        minDate.setDate(now.getDate() + minDays);
        var maxDate = new Date(now);
        maxDate.setDate(now.getDate() + maxDays);

        var pad = function(n) { return n < 10 ? '0' + n : '' + n; };
        var minDay = days[minDate.getDay()];
        var maxDay = days[maxDate.getDay()];
        var minStr = pad(minDate.getDate()) + '/' + pad(minDate.getMonth() + 1);
        var maxStr = pad(maxDate.getDate()) + '/' + pad(maxDate.getMonth() + 1);

        return 'Llega entre el ' + minDay + ' ' + minStr + ' y el ' + maxDay + ' ' + maxStr;
      }

      escapeHtml(str) {
        var div = document.createElement('div');
        div.appendChild(document.createTextNode(str));
        return div.innerHTML;
      }

      hideAll() {
        this.loadingEl.hidden = true;
        this.errorEl.hidden = true;
        this.deliverySection.hidden = true;
        this.pickupSection.hidden = true;
      }
    });
  }
</script>
```

---

## Snippet Cart: shipping-estimator-cart.liquid

`snippets/shipping-estimator-cart.liquid`

**IMPORTANTE**: Este snippet DEBE incluir su propio bloque `<style>` con todos los estilos,
prefijados con `.cart--shipping-estimator`, porque los estilos del PDP NO estan disponibles
en paginas que no son de producto.

```liquid
{% comment %}
  Renders the shipping estimator for the cart drawer.
  Uses the same Web Component as the PDP version but in cart mode
  (no cart backup/clear/restore needed).

  Usage:
  {% render 'shipping-estimator-cart' %}
{% endcomment %}

<style>
  .cart--shipping-estimator .shipping-estimator {
    font-family: var(--font-century-gothic), sans-serif;
    width: 100%;
  }

  .cart--shipping-estimator .shipping-estimator__title {
    font-size: 14px;
    font-weight: 400;
    color: #000;
    margin: 0 0 8px;
    line-height: 22px;
  }

  .cart--shipping-estimator .shipping-estimator__form {
    display: flex;
    align-items: center;
    gap: 0;
  }

  .cart--shipping-estimator .shipping-estimator__input-wrapper {
    position: relative;
    flex: 1;
  }

  .cart--shipping-estimator .shipping-estimator__input {
    width: 100%;
    height: 44px;
    border: 1px solid #ccc;
    border-right: none;
    padding: 0 16px;
    font-family: inherit;
    font-size: 14px;
    color: #3d3d3d;
    outline: none;
    box-sizing: border-box;
    background: transparent;
  }

  .cart--shipping-estimator .shipping-estimator__input::placeholder {
    color: #919191;
  }

  .cart--shipping-estimator .shipping-estimator__input:focus {
    border-color: #3d3d3d;
    outline: none;
    box-shadow: none;
  }

  .cart--shipping-estimator .shipping-estimator__submit {
    height: 44px;
    min-width: 87px;
    border: 1px solid #ccc;
    padding: 0 16px;
    font-family: inherit;
    font-size: 14px;
    color: #3d3d3d;
    background: transparent;
    cursor: pointer;
    line-height: 22px;
    white-space: nowrap;
  }

  .cart--shipping-estimator .shipping-estimator__submit:hover {
    background: #f5f5f5;
  }

  .cart--shipping-estimator .shipping-estimator__submit:disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }

  .cart--shipping-estimator .shipping-estimator__help {
    margin-top: 8px;
  }

  .cart--shipping-estimator .shipping-estimator__help a {
    font-size: 12px;
    color: #000;
    text-decoration: underline;
    line-height: 20px;
  }

  .cart--shipping-estimator .shipping-estimator__loading {
    display: flex;
    align-items: center;
    gap: 8px;
    margin-top: 12px;
    font-size: 14px;
    color: #626262;
  }

  .cart--shipping-estimator .shipping-estimator__spinner {
    width: 20px;
    height: 20px;
    border: 2px solid #ccc;
    border-top-color: #3d3d3d;
    border-radius: 50%;
    animation: se-spin 0.6s linear infinite;
  }

  @keyframes se-spin {
    to { transform: rotate(360deg); }
  }

  .cart--shipping-estimator .shipping-estimator__error {
    margin-top: 12px;
    font-size: 12px;
    color: #c00;
    line-height: 16px;
  }

  .cart--shipping-estimator .shipping-estimator__results-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 14px;
    color: #000;
    line-height: 22px;
    margin-bottom: 8px;
  }

  .cart--shipping-estimator .shipping-estimator__results-cp {
    font-weight: 700;
  }

  .cart--shipping-estimator .shipping-estimator__change-cp {
    font-size: 14px;
    color: #000;
    text-decoration: underline;
    cursor: pointer;
    background: none;
    border: none;
    font-family: inherit;
    padding: 0;
    line-height: 22px;
  }

  .cart--shipping-estimator .shipping-estimator__section-header {
    display: flex;
    align-items: center;
    gap: 8px;
    margin-bottom: 8px;
  }

  .cart--shipping-estimator .shipping-estimator__section-icon {
    width: 24px;
    height: 24px;
    flex-shrink: 0;
  }

  .cart--shipping-estimator .shipping-estimator__section-icon svg {
    width: 24px;
    height: 24px;
    display: block;
  }

  .cart--shipping-estimator .shipping-estimator__section-title {
    font-size: 14px;
    color: #000;
    line-height: 22px;
    font-weight: 400;
  }

  .cart--shipping-estimator .shipping-estimator__rates {
    display: flex;
    flex-direction: column;
    gap: 8px;
  }

  .cart--shipping-estimator .shipping-estimator__rate {
    display: flex;
    align-items: center;
    justify-content: space-between;
    background: #f9f9f9;
    padding: 8px 16px;
    gap: 16px;
  }

  .cart--shipping-estimator .shipping-estimator__rate-info {
    display: flex;
    flex-direction: column;
    gap: 4px;
    min-width: 0;
  }

  .cart--shipping-estimator .shipping-estimator__rate-name {
    font-size: 12px;
    font-weight: 700;
    color: #626262;
    line-height: 16px;
  }

  .cart--shipping-estimator .shipping-estimator__rate-description {
    font-size: 12px;
    font-weight: 400;
    color: #626262;
    line-height: 16px;
  }

  .cart--shipping-estimator .shipping-estimator__rate-delivery {
    font-size: 12px;
    font-weight: 400;
    color: #626262;
    line-height: 16px;
  }

  .cart--shipping-estimator .shipping-estimator__rate-pricing {
    display: flex;
    flex-direction: column;
    align-items: flex-end;
    gap: 4px;
    flex-shrink: 0;
  }

  .cart--shipping-estimator .shipping-estimator__rate-price {
    font-size: 15px;
    font-weight: 400;
    color: #626262;
    line-height: 16px;
    white-space: nowrap;
  }

  .cart--shipping-estimator .shipping-estimator__rate-price--free {
    font-size: 12px;
    font-weight: 700;
    color: #2ec54a;
  }

  .cart--shipping-estimator .shipping-estimator__rate-price--original {
    font-size: 12px;
    font-weight: 400;
    color: #626262;
    text-decoration: line-through;
    line-height: 16px;
    white-space: nowrap;
  }

  .cart--shipping-estimator .shipping-estimator [hidden] {
    display: none !important;
  }

  /* Cart-specific: scrollable results */
  .cart--shipping-estimator [data-results-state]:not([hidden]) {
    max-height: 250px;
    overflow-y: auto;
  }

  .cart--shipping-estimator [data-results-state] > [data-delivery-section],
  .cart--shipping-estimator [data-results-state] > [data-pickup-section] {
    margin-top: 16px;
  }
</style>

<shipping-estimator
  class="shipping-estimator"
  data-context="cart"
  id="ShippingEstimatorCart"
  style="display:none;"
>
  {% render 'zip-codes-json' %}
  <div data-form-state>
    <p class="shipping-estimator__title">Medios de envío</p>
    <form class="shipping-estimator__form" data-shipping-form>
      <div class="shipping-estimator__input-wrapper">
        <input
          type="text"
          class="shipping-estimator__input"
          data-zip-input
          placeholder="Tu código postal"
          inputmode="numeric"
          maxlength="8"
          autocomplete="postal-code"
          required
        >
      </div>
      <button type="submit" class="shipping-estimator__submit" data-submit-btn>Calcular</button>
    </form>
    <div class="shipping-estimator__help">
      <a href="https://www.correoargentino.com.ar/formularios/cpa" target="_blank" rel="noopener noreferrer">No sé mi código postal</a>
    </div>
  </div>

  <div data-results-state hidden>
    <div class="shipping-estimator__results-header">
      <span>Entregas para el CP: <span class="shipping-estimator__results-cp" data-results-cp></span></span>
      <button type="button" class="shipping-estimator__change-cp" data-change-cp>Cambiar CP</button>
    </div>
    <div data-delivery-section hidden>
      <div class="shipping-estimator__section">
        <div class="shipping-estimator__section-header">
          <span class="shipping-estimator__section-icon" aria-hidden="true">
            <!-- delivery truck SVG icon -->
          </span>
          <span class="shipping-estimator__section-title">Envío a domicilio</span>
        </div>
        <div class="shipping-estimator__rates" data-delivery-rates></div>
      </div>
    </div>
    <div data-pickup-section hidden>
      <div class="shipping-estimator__section">
        <div class="shipping-estimator__section-header">
          <span class="shipping-estimator__section-icon" aria-hidden="true">
            <!-- pickup location SVG icon -->
          </span>
          <span class="shipping-estimator__section-title">Retirar por</span>
        </div>
        <div class="shipping-estimator__rates" data-pickup-rates></div>
      </div>
    </div>
  </div>

  <div class="shipping-estimator__loading" data-loading hidden>
    <span class="shipping-estimator__spinner"></span>
    <span>Calculando tarifas...</span>
  </div>

  <div class="shipping-estimator__error" data-error hidden></div>
</shipping-estimator>
```

**Nota**: El snippet del cart NO incluye `<script>` porque reutiliza el Custom Element
registrado por el snippet PDP. Si la tienda solo usa el estimador en el cart (sin PDP),
incluir el JavaScript aqui tambien.

---

## Integracion con Vue Cart Drawer

Si el carrito usa Vue, el snippet se mueve al componente via DOM:

### En el template Liquid del carrito (ig-cart-vue.liquid)

```liquid
{% render 'shipping-estimator-cart' %}
```

### En el componente Vue (ModalCart.vue)

```html
<template>
  <div class="cart--footer">
    <div class="cart--shipping-estimator" ref="shippingEstimatorSlot"></div>
    <!-- resto del footer -->
  </div>
</template>

<script lang="ts" setup>
import { ref, onMounted } from 'vue'

const shippingEstimatorSlot = ref<HTMLElement | null>(null)

onMounted(() => {
    const el = document.getElementById('ShippingEstimatorCart')
    if (el && shippingEstimatorSlot.value) {
        shippingEstimatorSlot.value.appendChild(el)
        el.style.display = ''
    }
})
</script>
```

### Estilos en ModalCart.vue (SCSS)

```scss
.cart--footer {
  .cart--shipping-estimator {
    width: 100%;
    border-bottom: 1px solid $color-border;
    padding-bottom: 1rem;
  }

  // Limit results height in cart drawer
  .cart--shipping-estimator :deep([data-results-state]:not([hidden])) {
    max-height: 250px;
    overflow-y: auto;
  }
}
```

---

## Key Patterns Summary

| Pattern | Description |
|---------|-------------|
| Dual mode | PDP (backup/restore) vs Cart (direct) via `data-context` |
| CSS duplication | Cart snippet MUST have own styles, prefixed for scoping |
| DOM relocation | Cart renders hidden, moved to Vue via `appendChild` |
| localStorage | Persists last zip code across sessions |
| Retry logic | Up to 3 full attempts (prepare + poll) for rates |
| Rate classification | Delivery vs pickup via keyword matching on rate name |
| Custom Element guard | `if (!customElements.get('shipping-estimator'))` prevents double registration |
| variant:changed | PDP listens for variant changes, resets results |
| Add-to-cart lock | PDP disables add buttons during calculation |
