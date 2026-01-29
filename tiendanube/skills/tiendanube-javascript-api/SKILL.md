---
name: tiendanube-javascript-api
description: JavaScript API reference for TiendaNube/Nuvemshop theme development. Use when working with cart operations (add, remove, change quantity), shipping calculator, product variants, newsletter forms, checkout customization, or any client-side functionality in store.js.tpl. Covers LS object methods, jQueryNuvem, events, data-store selectors, and Checkout SDK.
---

# TiendaNube JavaScript API

Reference for client-side JavaScript in TiendaNube themes. Source: linkedstore-v2.js

## Quick Reference

### Global Object: LS

The `LS` object is available on all storefront pages:

```javascript
LS.store.id          // Store ID
LS.store.url         // Store URL
LS.cart.subtotal     // Cart subtotal in cents
LS.cart.items        // Array of cart items
LS.lang              // Language code (e.g., 'pt_BR', 'es')
LS.currency.code     // Currency ISO code
LS.currency.display_short  // Currency symbol (e.g., 'R$')
LS.customer          // Customer ID or null
LS.theme.code        // Current theme code
LS.data.cart         // Internal cart data
```

## Complete LS Methods Reference

### Cart Operations

```javascript
// Add to cart (basic)
LS.addToCart(form, callback);

// Add to cart (enhanced with UI feedback)
LS.addToCartEnhanced($form, addText, addingText, errorText, callback);

// Update cart
LS.updateCart(async);
LS.updateCartEnhanced(callback);

// Change item quantity
LS.changeQuantity(itemId, quantity, async);

// Remove item
LS.removeItem(itemId, async);

// Increment/decrement quantity
LS.plusQuantity(itemId, async);
LS.minusQuantity(itemId, async);

// Cart UI
LS.toggleCart();           // Open/close cart panel
LS.blockCart();            // Block cart during operations
LS.setupCart();            // Initialize cart functionality
LS.resetItems();           // Reset cart items display
LS.refreshTotals();        // Refresh totals display

// Buy now (skip cart)
LS.buyNow(form);
```

### Shipping

```javascript
// Calculate shipping
LS.calculateShipping(container, zipcode);

// Update shipping on product page
LS.updateShippingProduct();

// Update shipping in cart
LS.updateCartShipping();

// Save selected shipping option
LS.saveCalculatedShipping(async);

// Save shipping country
LS.saveShippingCountry(country);

// Add shipping to total display
LS.addToTotal(shippingPrice);
```

### Product & Variants

```javascript
// Register variant change callback
LS.registerOnChangeVariant(callback);
// Usage: LS.registerOnChangeVariant(function(variant) { ... });

// Change variant programmatically
LS.changeVariant(variantId);

// Update price display
LS.updatePrice(price);

// Variant change event handlers
LS.onChangeVariants(callback);  // Alias for registerOnChangeVariant
```

### Newsletter & Forms

```javascript
// Newsletter subscription
LS.newsletter(formSelector, modalSelector, contactUrl, callback);

// Newsletter popup
LS.newsletterPopup(config);

// Home popup
LS.homePopup(config);

// Comment notification
LS.commentNotify(form);
```

### UI Components

```javascript
// QuickShop
LS.fillQuickshop(productId, container);

// Cross-selling
LS.fillCrossSelling(productIds, container);

// Product item slider
LS.productItemSlider(container);

// Zoom
LS.openMobileZoom(imageUrl);
LS.closeMobileZoom();

// Video
LS.loadVideo(container);

// Filters
LS.showFilters();
LS.instaFilterMobile();
LS.sortByMobile();
LS.productFiltersTypes;

// Scroll/Pagination
LS.infiniteScroll(config);
LS.hybridScroll(config);
LS.paginateMobile();
```

### Utilities

```javascript
// Currency formatting
LS.formatToCurrency(value);
LS.formatNumber(number, decimals);

// URL manipulation
LS.getUrlParams();
LS.paramsToUrl(params);
LS.urlAddParam(url, key, value);
LS.encodeURIParams(params);

// DOM utilities
LS.removeElement(selector);
LS.clone(element);
LS.template(templateString, data);
LS.debounce(func, wait);

// Search
LS.search(query);

// AJAX wrapper
LS.ajax(options);
```

### Events System

```javascript
// Subscribe to events
LS.on(eventName, callback);

// Trigger events
LS.trigger(eventName, data);

// Available events object
LS.events
```

### Auth & Cookies

```javascript
// Facebook login
LS.facebookLogin();

// Cookie consent
LS.allowCookiesUsage();
LS.denyCookiesUsage();
```

### Subscription Products

```javascript
LS.subscriptionSubmit($productContainer, errorCallback, event);
```

### Analytics

```javascript
LS.sendFrontendEvent(eventName, data);
```

### Admin

```javascript
LS.backToAdminEffect();  // Visual effect for admin preview
```

## jQuery: jQueryNuvem

Use `jQueryNuvem` instead of `$` to avoid conflicts:

```javascript
jQueryNuvem(document).on('click', '.js-addtocart', function(e) {
  e.preventDefault();
  // Handle click
});
```

For external scripts, use `useJquery()` Promise:

```javascript
useJquery().then((jq) => {
  console.log(`jQuery version: ${jq().jquery}`);
});
```

## Page-Specific Data

### Product Page

```javascript
LS.product.id              // Product ID
LS.product.name            // Product name
LS.product.tags            // Array of tags
LS.product.requires_shipping  // Boolean
LS.variants                // JSON encoded variants
```

### Category Page

```javascript
LS.category.id    // Category ID
LS.category.name  // Category name
```

### Thank You Page

```javascript
LS.order.id         // Order ID
LS.order.number     // Order number
LS.order.hash       // Order hash
LS.order.created_at // Creation date
LS.order.coupon     // Array of coupon codes
LS.order.discount   // Discount in cents
LS.order.total      // Total in cents
LS.order.gateway    // Payment gateway code
```

## Internal Classes & Services (from linkedstore-v2.js)

These are used internally but useful to understand architecture:

### Cart Services
- `CartService` - Main cart operations
- `CartItemsPriceManager` - Price updates
- `CartItemsPromotionManager` - Promotion handling
- `CartNotification` - Cart alerts
- `CartValidation` - Cart validation

### Shipping Services
- `ShippingService` - Shipping calculations
- `ShippingCacheService` - Shipping cache
- `ShippingFacade` - Shipping interface
- `ShippingCalculation` - Calculate shipping
- `ShippingAjax` - AJAX shipping calls

### Other Services
- `SessionStorageService` - Session storage wrapper
- `NewsletterPopupCookie` - Newsletter popup state

## HTML Selectors

See `references/data-store-selectors.md` for complete list of `data-store` attributes for app anchoring.

### Critical JS Hooks

Use `js-` prefixed classes for JavaScript functionality:

```html
<button class="js-addtocart js-prod-submit-form btn btn-primary">
  Add to cart
</button>

<div class="js-product-detail js-product-container" 
     data-variants="{{ product.variants_object | json_encode }}">
</div>
```

Common JS hooks:
- `.js-addtocart` - Add to cart button
- `.js-prod-submit-form` - Product form submit
- `.js-cart-quantity-input` - Quantity input
- `.js-cart-quantity-btn` - Quantity +/- buttons
- `.js-toggle-cart` - Open cart panel
- `.js-modal-close` - Close modal
- `.js-shipping-input` - Shipping zipcode input
- `.js-calculate-shipping` - Calculate shipping button
- `.js-shipping-method` - Shipping option radio
- `.js-branch-method` - Pickup branch option
- `.js-product-detail` - Product detail container
- `.js-product-container` - Product container with variants data
- `.js-variation-option` - Variant selector
- `.js-search-input` - Search input field
- `.js-search-suggest` - Search suggestions container
- `.js-alert-added-to-cart` - Add to cart notification
- `.js-ajax-cart-panel` - AJAX cart panel form
- `.js-cart-item` - Cart item container

## Checkout SDK

For checkout customization, use `window.SDKCheckout`:

```javascript
// Subscribe to cart updates
window.SDKCheckout.subscribeEvent('LINE_ITEMS_UPDATED', (event, data) => {
  console.log(data); // Array of cart items
});

// Get active gateway IDs
window.SDKCheckout.getPaymentIds();

// Hide payment options
window.SDKCheckout.hidePaymentOptions(['gateway_id']);

// Change payment benefit text
window.SDKCheckout.changePaymentBenefit({ 
  id: 'gateway_id', 
  value: '12x interest-free' 
});

// Add extra text to payment method
window.SDKCheckout.addPaymentContentText({ 
  id: 'gateway_id', 
  value: 'Custom message' 
});

// Hide specific installments
window.SDKCheckout.hideInstallments({ 
  id: 'gateway_id', 
  value: [3, 6] 
});
```

## Common Patterns

### Add to Cart with Notification

```javascript
jQueryNuvem(document).on('click', '.js-addtocart', function(e) {
  if (!jQueryNuvem(this).hasClass('contact')) {
    e.preventDefault();
    
    var callback = function() {
      if (window.innerWidth < 768) {
        jQueryNuvem('.js-toggle-cart').click();
      } else {
        jQueryNuvem('.js-alert-added-to-cart')
          .show()
          .toggleClass('notification-visible notification-hidden');
        setTimeout(function() {
          jQueryNuvem('.js-alert-added-to-cart')
            .toggleClass('notification-visible notification-hidden');
        }, 7000);
      }
    };
    
    var $form = jQueryNuvem(this).closest('form');
    LS.addToCartEnhanced(
      $form,
      '{{ "Add to cart" | translate }}',
      '{{ "Adding..." | translate }}',
      '{{ "Error" | translate }}',
      callback
    );
  }
});
```

### Cart Quantity Change

```javascript
jQueryNuvem(document).on('change', '.js-cart-quantity-input', function() {
  var itemId = jQueryNuvem(this).data('item-id');
  var quantity = jQueryNuvem(this).val();
  
  if (quantity < 1) {
    if (confirm('{{ "Remove item?" | translate }}')) {
      LS.removeItem(itemId, true);
    } else {
      jQueryNuvem(this).val(1);
    }
  } else {
    LS.changeQuantity(itemId, quantity, true);
  }
});
```

### Register Variant Change Callback

```javascript
// This is the key method for handling variant changes
LS.registerOnChangeVariant(function(variant) {
  // variant object contains:
  // - id: variant ID
  // - price: price in cents
  // - compare_at_price: original price if on sale
  // - stock: stock quantity or null
  // - sku: variant SKU
  // - name: variant name
  
  // Update price display
  jQueryNuvem('.js-price-display').text(
    LS.formatToCurrency(variant.price / 100)
  );
  
  // Update stock status
  if (variant.stock === 0) {
    jQueryNuvem('.js-addtocart').prop('disabled', true);
  }
  
  // Update shipping calculator
  LS.updateShippingProduct();
});
```

### Shipping Calculator

```javascript
window.urls = {
  shippingUrl: '{{ store.shipping_calculator_url | escape("js") }}'
};

// On shipping option change
jQueryNuvem(document).on('change', '.js-shipping-method', function() {
  var price = jQueryNuvem(this).data('price');
  LS.addToTotal(price);
  LS.saveCalculatedShipping(true);
});

// On branch/pickup change
jQueryNuvem(document).on('change', '.js-branch-method', function() {
  LS.saveCalculatedShipping(true);
});
```

### QuickShop Implementation

```javascript
jQueryNuvem(document).on('click', '.js-quickshop-trigger', function(e) {
  e.preventDefault();
  var productId = jQueryNuvem(this).data('product-id');
  var $container = jQueryNuvem('.js-quickshop-container');
  
  LS.fillQuickshop(productId, $container);
});
```

### Infinite Scroll

```javascript
LS.infiniteScroll({
  container: '.js-product-grid',
  item: '.js-product-item',
  pagination: '.js-pagination',
  loadMoreButton: '.js-load-more'
});
```

### Hybrid Scroll (Load More + Pagination)

```javascript
LS.hybridScroll({
  container: '.js-product-grid',
  pagesBeforePagination: 3  // After 3 "load more" clicks, show pagination
});
```

## Cookie Service

```javascript
cookieService.get('cookie_name');
cookieService.set('cookie_name', 'value');
cookieService.remove('cookie_name');
```

## Best Practices

1. **Wrap in closure** to avoid conflicts:
   ```javascript
   (function() {
     // Your code
   })();
   ```

2. **Use jQueryNuvem** instead of `$` or `jQuery`

3. **Never depend on theme jQuery** - use `useJquery()` for external scripts

4. **Use `data-store` selectors** for app anchoring, not CSS classes

5. **Prices are in cents** - divide by 100 for display

6. **Test on mobile** - many interactions differ by screen width

## Resources

- `references/data-store-selectors.md` - Complete list of HTML anchor selectors
- `references/ls-object-reference.md` - Full LS object documentation by page type
