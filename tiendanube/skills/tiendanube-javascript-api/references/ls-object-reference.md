# LS Object Reference

Complete documentation of the `LS` JavaScript object available on TiendaNube storefronts.
Based on linkedstore-v2.js analysis.

## All Pages (Store Page)

```javascript
var LS = {
  store: {
    id: Number,        // Store ID
    url: String,       // Store URL
    gads_measurement_id: String,  // Google Ads ID
    has_google_analytics_track_product_ids: Boolean,
    has_preexisting_google_mc_catalog: Boolean
  },
  cart: {
    subtotal: Number,  // Cart subtotal in cents
    items: [
      {
        id: Number,              // Product Variant ID
        name: String,            // Variant name
        unit_price: Number,      // Price in cents
        quantity: Number,        // Quantity to purchase
        requires_shipping: Boolean  // Physical shipping required
      }
    ],
    has_shippable_products: Boolean,      // At least one physical product
    has_non_shippable_products: Boolean   // At least one digital product
  },
  lang: String,        // Language code (e.g., 'pt_BR', 'es', 'es_MX', 'en')
  currency: {
    code: String,              // ISO 4217 code (e.g., 'BRL', 'ARS', 'MXN')
    display_short: String,     // Symbol (e.g., 'R$', '$')
    display_long: String,      // Full format with currency
    cents_separator: String,   // Decimal separator (e.g., ',', '.')
    thousands_separator: String // Thousands separator (may be blank)
  },
  country: String,     // ISO 3166-1 country code
  customer: Number|null,  // Customer ID or null if not logged in
  theme: {
    code: String,      // Theme code
    name: String       // Theme name
  },
  data: {
    cart: {
      total: Number    // Cart total in cents (used internally)
    }
  },
  events: Object,      // Event emitter instance
  productsCount: Number // Total products count
};
```

## Complete Method Reference

### Cart Operations

| Method | Parameters | Description |
|--------|------------|-------------|
| `addToCart` | (form, callback) | Basic add to cart |
| `addToCartEnhanced` | ($form, addText, addingText, errorText, callback) | Add to cart with UI feedback |
| `updateCart` | (async) | Update cart contents |
| `updateCartEnhanced` | (callback) | Update cart with callback |
| `changeQuantity` | (itemId, quantity, async) | Change item quantity |
| `removeItem` | (itemId, async) | Remove item from cart |
| `plusQuantity` | (itemId, async) | Increment quantity by 1 |
| `minusQuantity` | (itemId, async) | Decrement quantity by 1 |
| `toggleCart` | () | Toggle cart panel visibility |
| `blockCart` | () | Block cart during operations |
| `setupCart` | () | Initialize cart functionality |
| `resetItems` | () | Reset cart items display |
| `refreshTotals` | () | Refresh totals display |
| `buyNow` | (form) | Buy now (direct checkout) |
| `onItemDeleteSuccess` | (callback) | Callback after item deletion |

### Shipping Operations

| Method | Parameters | Description |
|--------|------------|-------------|
| `calculateShipping` | (container, zipcode) | Calculate shipping options |
| `updateShippingProduct` | () | Update shipping on product page |
| `updateCartShipping` | () | Update shipping in cart |
| `saveCalculatedShipping` | (async) | Save selected shipping option |
| `saveShippingCountry` | (country) | Save shipping country |
| `addToTotal` | (shippingPrice) | Add shipping to displayed total |

### Product & Variants

| Method | Parameters | Description |
|--------|------------|-------------|
| `registerOnChangeVariant` | (callback) | Register variant change callback |
| `onChangeVariants` | (callback) | Alias for registerOnChangeVariant |
| `changeVariant` | (variantId) | Change variant programmatically |
| `updatePrice` | (price) | Update price display |
| `fillQuickshop` | (productId, container) | Load QuickShop content |
| `fillCrossSelling` | (productIds, container) | Load cross-selling products |

### Newsletter & Forms

| Method | Parameters | Description |
|--------|------------|-------------|
| `newsletter` | (formSelector, modalSelector, url, callback) | Newsletter subscription |
| `newsletterPopup` | (config) | Show newsletter popup |
| `homePopup` | (config) | Show home popup |
| `commentNotify` | (form) | Comment notification |

### UI Components

| Method | Parameters | Description |
|--------|------------|-------------|
| `productItemSlider` | (container) | Initialize product slider |
| `openMobileZoom` | (imageUrl) | Open mobile image zoom |
| `closeMobileZoom` | () | Close mobile zoom |
| `loadVideo` | (container) | Load video player |
| `showFilters` | () | Show filters panel |
| `instaFilterMobile` | () | Instagram-style filter |
| `sortByMobile` | () | Mobile sort options |
| `infiniteScroll` | (config) | Enable infinite scroll |
| `hybridScroll` | (config) | Enable hybrid scroll |
| `paginateMobile` | () | Mobile pagination |

### Utilities

| Method | Parameters | Description |
|--------|------------|-------------|
| `formatToCurrency` | (value) | Format as currency string |
| `formatNumber` | (number, decimals) | Format number with decimals |
| `getUrlParams` | () | Get URL parameters |
| `paramsToUrl` | (params) | Convert params to URL string |
| `urlAddParam` | (url, key, value) | Add parameter to URL |
| `encodeURIParams` | (params) | Encode URI parameters |
| `removeElement` | (selector) | Remove DOM element |
| `clone` | (element) | Clone DOM element |
| `template` | (string, data) | Template string replacement |
| `debounce` | (func, wait) | Debounce function |
| `search` | (query) | Search functionality |
| `ajax` | (options) | AJAX wrapper |

### Events

| Method | Parameters | Description |
|--------|------------|-------------|
| `on` | (eventName, callback) | Subscribe to event |
| `trigger` | (eventName, data) | Trigger event |

### Auth & Cookies

| Method | Parameters | Description |
|--------|------------|-------------|
| `facebookLogin` | () | Facebook login |
| `allowCookiesUsage` | () | Accept cookies |
| `denyCookiesUsage` | () | Deny cookies |

### Subscriptions

| Method | Parameters | Description |
|--------|------------|-------------|
| `subscriptionSubmit` | ($container, errorCb, event) | Submit subscription product |

### Analytics

| Method | Parameters | Description |
|--------|------------|-------------|
| `sendFrontendEvent` | (eventName, data) | Send analytics event |

### Other

| Method | Parameters | Description |
|--------|------------|-------------|
| `setup` | () | Main setup function |
| `backToAdminEffect` | () | Admin preview effect |
| `swapProvinces` | () | Swap province selector |

## Product Page

Additional properties on product pages:

```javascript
LS.product = {
  id: Number,                  // Product ID
  name: String,                // Product name
  tags: Array<String>,         // Array of product tags
  requires_shipping: Boolean   // Physical shipping required
};

LS.variants = String;  // JSON encoded product variants array
// Parse with: JSON.parse(LS.variants)
```

### Variants Structure

```javascript
// After parsing LS.variants:
[
  {
    id: Number,
    sku: String,
    name: String,
    price: Number,           // Price in cents
    compare_at_price: Number, // Original price in cents (if on sale)
    stock: Number|null,      // Stock quantity or null for unlimited
    image_id: Number|null,   // Associated image ID
    attributes: [
      { name: String, value: String }  // e.g., { name: "Size", value: "M" }
    ]
  }
]
```

## Category Page

Additional properties on category pages:

```javascript
LS.category = {
  id: Number,    // Category ID
  name: String   // Category name
};
```

## Checkout Page

Simplified LS object on checkout:

```javascript
var LS = {
  store: {
    id: Number,
    url: String
  },
  cart: {
    subtotal: Number,
    items: [
      {
        id: Number,
        name: String,
        unit_price: Number,
        quantity: Number
      }
    ]
  },
  customer: Number|null,
  lang: String,
  currency: String  // ISO 4217 code only
};
```

**Note:** Cannot access this variable from Payment Options JavaScript interface.

## Thank You Page

Additional properties on order confirmation:

```javascript
LS.order = {
  id: Number,           // Order ID
  number: String,       // Order number (displayed to customer)
  hash: String,         // Order hash for URLs
  created_at: String,   // ISO 8601 date string
  coupon: Array<String>, // Applied coupon codes
  discount: Number,     // Total discount in cents
  total: Number,        // Order total in cents
  total_in_usd: Number, // Order total in USD cents
  gateway: String       // Payment gateway code
};
```

## LS Methods

### Cart Operations

```javascript
// Add product to cart with enhanced UI feedback
LS.addToCartEnhanced(
  $form,           // jQuery form element containing product data
  addText,         // Button text while idle: "Add to cart"
  addingText,      // Button text while loading: "Adding..."
  errorText,       // Error message text
  callback         // Function called on success
);

// Change item quantity
LS.changeQuantity(itemId, newQuantity, async);
// itemId: Number - cart item's variant ID
// newQuantity: Number - new quantity value
// async: Boolean - if true, updates via AJAX

// Remove item from cart
LS.removeItem(itemId, async);

// Increment quantity by 1
LS.plusQuantity(itemId, async);

// Decrement quantity by 1
LS.minusQuantity(itemId, async);
```

### Shipping

```javascript
// Save selected shipping option
LS.saveCalculatedShipping(async);

// Update shipping options on product page (after variant change)
LS.updateShippingProduct();

// Add shipping cost to displayed total
LS.addToTotal(shippingPrice);
// shippingPrice: Number - price as float (not cents)
```

### Currency Formatting

```javascript
// Format number as currency string
LS.formatToCurrency(value);
// value: Number - value to format
// Returns: String - formatted with currency symbol
```

### Newsletter

```javascript
// Initialize newsletter form
LS.newsletter(
  formContainerSelector,  // e.g., '#newsletter-form'
  modalSelector,          // e.g., '#newsletter-modal' (optional)
  contactUrl,             // store.contact_url
  callback                // function(response) { }
);

// Response object:
{
  success: Boolean,
  message: String
}
```

### Subscription Products

```javascript
// Submit subscription product form
LS.subscriptionSubmit($productContainer, errorCallback, event);
// Returns: { changeCartSubmit: Boolean } or undefined
```

## Global Functions

### useJquery

For external scripts that need jQuery:

```javascript
useJquery().then(function(jq) {
  // jq is the jQuery function
  jq('.my-selector').addClass('active');
});
```

**Note:** Theme jQuery version varies. This ensures jQuery is available but doesn't guarantee version.

### Cookie Service

```javascript
// Get cookie value
cookieService.get('cookie_name');

// Set cookie
cookieService.set('cookie_name', 'value');

// Remove cookie
cookieService.remove('cookie_name');
```

## Common Cookies

| Cookie | Purpose |
|--------|---------|
| `shipping_zipcode` | Last used shipping zipcode |
| `first_product_added_successfully` | Cart notification flag |
| `cart_notification_shown` | Prevents repeated notifications |

## Data Attributes on HTML Elements

### Product Container

```html
<div class="js-product-detail js-product-container"
     data-variants="{{ product.variants_object | json_encode }}">
```

### Cart Item

```html
<div class="js-cart-item" data-item-id="{{ item.id }}">
  <input class="js-cart-quantity-input" 
         data-item-id="{{ item.id }}" 
         value="{{ item.quantity }}">
</div>
```

### Shipping Option

```html
<input class="js-shipping-method" 
       data-price="{{ option.price }}"
       data-code="{{ option.code }}">
```

### Payment Method

```html
<div class="js-info-payment-method"
     data-minimum-installment-value="{{ method.minimum_installment }}">
</div>
```

## Events

Custom events triggered by the platform:

```javascript
// Shipping options loaded
jQueryNuvem(document).on('shipping.options.checked', '.js-shipping-method', function(e) {
  // First shipping option was auto-selected
});
```

## Script Loading Events

For external apps:

- `onfirstinteraction` - Loads after first user interaction (scroll, click, tap)
- `onload` - Loads immediately with page (requires approval from TiendaNube)
