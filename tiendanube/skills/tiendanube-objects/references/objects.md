# TiendaNube Objects Reference

## store

Store information and URLs.

### Basic Info
| Attribute | Type | Description |
|-----------|------|-------------|
| `store.name` | string | Store name |
| `store.url` | string | Store URL |
| `store.logo` | string | Logo URL |
| `store.phone` | string | Phone number |
| `store.email` | string | Email address |
| `store.country` | string | ISO 3166-1 country code (AR, BR, MX) |
| `store.currency` | string | ISO 4217 currency code (ARS, BRL, MXN) |
| `store.domain` | string | Default domain (store.mitiendanube.com) |

### Social Media
| Attribute | Type | Description |
|-----------|------|-------------|
| `store.blog` | string | Blog URL |
| `store.facebook` | string | Facebook page URL |
| `store.twitter` | string | Twitter profile URL |
| `store.twitter_user` | string | Twitter username |
| `store.instagram` | string | Instagram profile URL |

### URLs
| Attribute | Type | Description |
|-----------|------|-------------|
| `store.products_url` | string | All products page URL |
| `store.cart_url` | string | Cart page URL |
| `store.contact_url` | string | Contact page URL |
| `store.search_url` | string | Search page URL |
| `store.checkout_url` | string | Checkout URL |
| `store.shipping_calculator_url` | string | Shipping calculator URL |

### Customer URLs
| Attribute | Type | Description |
|-----------|------|-------------|
| `store.customer_home_url` | string | Customer home |
| `store.customer_register_url` | string | Registration page |
| `store.customer_login_url` | string | Login page |
| `store.customer_logout_url` | string | Logout URL |
| `store.customer_reset_password_url` | string | Password reset |
| `store.customer_order_url` | string | Orders page |
| `store.customer_info_url` | string | Profile edit page |
| `store.customer_addresses_url` | string | Addresses page |

### Booleans
| Attribute | Type | Default | Description |
|-----------|------|---------|-------------|
| `store.has_custom_domain` | boolean | false | Has custom domain |
| `store.has_accounts` | boolean | true | Supports user accounts |
| `store.is_catalog` | boolean | false | Catalog only (no purchases) |
| `store.has_shipping` | boolean | false | Shipping methods enabled |
| `store.branches` | boolean | false | Physical locations enabled |
| `store.customer_accounts` | string | optional | 'optional' or 'mandatory' |

### Other
| Attribute | Type | Description |
|-----------|------|-------------|
| `store.contact_intro` | string | Contact page intro text |
| `store.live_chat` | string | Chat integration code |
| `store.analytics_account` | string | Google Analytics ID |
| `store.business_id` | string | Business ID (Brazil only) |
| `store.business_name` | string | Business name (Brazil only) |
| `afip` | string | AFIP Data Fiscal (Argentina only) |

---

## product

Product data and attributes.

### Basic Info
| Attribute | Type | Description |
|-----------|------|-------------|
| `product.id` | string | Product ID |
| `product.name` | string | Product name |
| `product.brand` | string | Brand name |
| `product.description` | string | HTML description |
| `product.sku` | string | SKU code |
| `product.handle` | string | URL slug |
| `product.canonical_url` | string | Canonical URL |
| `product.social_url` | string | Social sharing URL |

### Pricing (in cents)
| Attribute | Type | Description |
|-----------|------|-------------|
| `product.price` | string | Price (or promotional price) |
| `product.compare_at_price` | string/false | Original price if on sale |
| `product.min_price` | string | Minimum variant price |
| `product.max_price` | string | Maximum variant price |
| `product.display_price` | boolean | Has displayable price |
| `product.currency` | string | Currency code |

### Stock
| Attribute | Type | Description |
|-----------|------|-------------|
| `product.stock` | string | Stock quantity |
| `product.stock_control` | boolean | Stock is controlled |
| `product.available` | boolean | Is available |

### Physical
| Attribute | Type | Description |
|-----------|------|-------------|
| `product.weight` | string | Weight value |
| `product.weight_unit` | string | Weight unit (KG) |
| `product.requires_shipping` | boolean | Requires physical shipping |
| `product.free_shipping` | boolean | Has free shipping badge |

### Images
| Attribute | Type | Description |
|-----------|------|-------------|
| `product.images` | array | All Product_Image objects |
| `product.images_count` | string | Number of images |
| `product.featured_image` | object | Main Product_Image |
| `product.other_images` | array | Secondary images |

### Variants & Options
| Attribute | Type | Description |
|-----------|------|-------------|
| `product.variations` | array | Variation objects |
| `product.variants` | array | Product_Variant objects |
| `product.variants_object` | array | Variants with full data |
| `product.default_options` | array | Default variant option names |
| `product.selected_or_first_available_variant` | object | Default variant |

### Categories & Tags
| Attribute | Type | Description |
|-----------|------|-------------|
| `product.category` | object | Primary Category object |
| `product.tags` | array | Product tags |

### SEO
| Attribute | Type | Description |
|-----------|------|-------------|
| `product.seo_title` | string | SEO title |
| `product.seo_description` | string | SEO description |

### Promotions
| Attribute | Type | Description |
|-----------|------|-------------|
| `product.promotional_offer` | boolean | Has active promotion |
| `product.promotional_offer.script.is_percentage_off` | boolean | Is percentage discount |
| `product.promotional_offer.parameters.percent` | string | Discount percentage (multiply by 100) |
| `product.installments` | string | Max installments (Brazil) |

---

## product_image

| Attribute | Type | Description |
|-----------|------|-------------|
| `id` | string | Image ID |
| `name` | string | Image filename |
| `alt` | string | Alt text |
| `position` | string | Position (1 = main) |

**Usage:**
```twig
{{ image | product_image_url('medium') }}
```

---

## product_variant

| Attribute | Type | Description |
|-----------|------|-------------|
| `name` | string | Variant name |
| `option1` | string | First option value |
| `option2` | string | Second option value |
| `option3` | string | Third option value |
| `options` | array | All option values |
| `price` | string | Price in cents |
| `compare_at_price` | string | Original price in cents |
| `display_price` | boolean | Has displayable price |
| `currency` | string | Currency code |
| `sku` | string | Variant SKU |
| `weight` | string | Variant weight |
| `weight_unit` | string | Weight unit |
| `stock` | string | Stock quantity |
| `stock_control` | boolean | Stock is controlled |
| `available` | boolean | Is available |

---

## category

| Attribute | Type | Description |
|-----------|------|-------------|
| `category.id` | string | Category ID |
| `category.name` | string | Category name |
| `category.description` | string | Description |
| `category.url` | string | Category URL |
| `category.handle` | string | URL slug |
| `category.parent` | object | Parent Category |
| `category.subcategories` | array | Child Categories |
| `category.images` | array | Category images |
| `category.top` | object | Top-level Category |
| `category.products` | array | Products in category |
| `category.products_count` | string | Product count |
| `category.active` | boolean | Is current category |
| `category.seo_title` | string | SEO title |
| `category.seo_description` | string | SEO description |

---

## cart

| Attribute | Type | Description |
|-----------|------|-------------|
| `cart.total` | string | Total in cents |
| `cart.subtotal` | string | Subtotal in cents |
| `cart.currency` | string | Currency code |
| `cart.weight` | string | Total weight |
| `cart.items_count` | string | Number of line items |
| `cart.items` | array | Cart_Item objects |
| `cart.has_shippable_products` | boolean | Has physical products |
| `cart.has_non_shippable_products` | boolean | Has digital products |

### cart_item

| Attribute | Type | Description |
|-----------|------|-------------|
| `item.id` | string | Line item ID |
| `item.name` | string | Product name |
| `item.unit_price` | string | Unit price in cents |
| `item.quantity` | string | Quantity |
| `item.subtotal` | string | Line subtotal in cents |
| `item.product` | object | Product object |
| `item.url` | string | Product URL |
| `item.featured_image` | object | Product_Image |

### Free Shipping
| Attribute | Type | Description |
|-----------|------|-------------|
| `cart.free_shipping.cart_has_free_shipping` | boolean | Has free shipping |
| `cart.free_shipping.min_price_free_shipping.min_price` | string | Min amount for free shipping |
| `cart.free_shipping.min_price_free_shipping.min_price_raw` | string | Min amount (no formatting) |

---

## customer

| Attribute | Type | Description |
|-----------|------|-------------|
| `customer.id` | string | Customer ID |
| `customer.name` | string | Full name |
| `customer.email` | string | Email |
| `customer.phone` | string | Phone |
| `customer.password` | boolean | Has password set |
| `customer.tags` | array | Customer tags |
| `customer.total_spent` | string | Total spent in cents |
| `customer.total_spent_currency` | string | Currency code |
| `customer.default_address` | object | Primary Address |
| `customer.cpf_cnpj` | string | CPF/CNPJ (Brazil) |
| `customer.orders` | array | Order objects |
| `customer.addresses` | array | Address objects |

### address

| Attribute | Type | Description |
|-----------|------|-------------|
| `address.id` | string | Address ID |
| `address.name` | string | Address name |
| `address.address` | string | Street |
| `address.number` | string | Number |
| `address.floor` | string | Floor/Apt |
| `address.locality` | string | Locality |
| `address.zipcode` | string | Postal code |
| `address.city` | string | City |
| `address.province` | string | Province/State |
| `address.country` | string | Country |
| `address.phone` | string | Phone |

### order

Extends cart object with additional attributes:

| Attribute | Type | Description |
|-----------|------|-------------|
| `order.id` | string | Order ID |
| `order.number` | string | Order number |
| `order.date` | string | Creation date |
| `order.status` | string | open/closed/cancelled |
| `order.payment_status` | string | pending/authorized/paid/voided/refunded |
| `order.shipping_status` | string | unpacked/unfulfilled/fulfilled |
| `order.shipping_name` | string | Shipping method name |
| `order.payment_name` | string | Payment method name |
| `order.discount` | string | Discount in cents |
| `order.coupon` | string | Coupon code |
| `order.total` | string | Total in cents |
| `order.currency` | string | Currency code |
| `order.address` | object | Shipping Address |

---

## navigation

### navigation_item

| Attribute | Type | Description |
|-----------|------|-------------|
| `item.name` | string | Menu item name |
| `item.url` | string | URL |
| `item.subitems` | array | Nested items |
| `item.current` | boolean | Is current page |

### breadcrumb

| Attribute | Type | Description |
|-----------|------|-------------|
| `breadcrumb.name` | string | Breadcrumb text |
| `breadcrumb.url` | string | URL |
| `breadcrumb.last` | boolean | Is last item |

---

## page

Custom pages created in admin.

| Attribute | Type | Description |
|-----------|------|-------------|
| `page.id` | string | Page ID |
| `page.name` | string | Page title |
| `page.content` | HTML | Page content |
| `page.url` | string | Page URL |
| `page.handle` | string | URL slug |
| `page.seo_title` | string | SEO title |
| `page.seo_description` | string | SEO description |

---

## section

Homepage product sections (from sections.txt).

| Attribute | Type | Description |
|-----------|------|-------------|
| `section.id` | string | Section code |
| `section.name` | string | Section name |
| `section.description` | string | Description |
| `section.products` | array | Featured products |

---

## language

| Attribute | Type | Description |
|-----------|------|-------------|
| `language.id` | string | Language ID |
| `language.code` | string | Code (es_AR, pt_BR, etc.) |
| `language.name` | string | Language name |
| `language.active` | boolean | Is current language |
| `language.country` | string | Country code |

---

## pages (pagination)

For category/search results.

| Attribute | Type | Description |
|-----------|------|-------------|
| `pages.previous` | string | Previous page URL |
| `pages.next` | string | Next page URL |
| `pages.current` | string | Current page number |
| `pages.amount` | string | Total pages |
| `pages.numbers` | array | All page objects |

**pages.numbers item:**
| Attribute | Type | Description |
|-----------|------|-------------|
| `number` | string | Page number |
| `url` | string | Page URL |
| `selected` | boolean | Is current page |

---

## settings

Theme configuration values from settings.txt.

Access any setting by name:
```twig
{{ settings.primary_color }}
{{ settings.show_slider }}
{{ settings.welcome_message }}
```

---

## shipping options

In shipping_options.tpl:

| Attribute | Type | Description |
|-----------|------|-------------|
| `option.name` | string | Full name with delivery time |
| `option.short_name` | string | Method name only |
| `option.time` | string | Delivery time text |
| `option.date` | string | Exact delivery date |
| `option.show_price` | boolean | Has price |
| `option.cost` | string | Formatted cost |
| `option.cost.value` | string | Cost value only |
| `option.old_cost.value` | string | Original cost (if free) |
| `option.method` | string | Method type (branch/table) |
| `option.shipping_type` | string | delivery/pickup |
| `option.code` | string | Internal code |
| `option.payment_rules` | string | Payment conditions |
| `option.suboptions` | array | Sub-options (branches) |
| `option.warning['enable']` | boolean | Has warning |
| `option.warning['message']` | string | Warning message |

---

## contact_result

After contact form submission:

| Attribute | Type | Description |
|-----------|------|-------------|
| `name` | string | Submitted name |
| `last_name` | string | Submitted last name |
| `email` | string | Submitted email |
| `phone` | string | Submitted phone |
| `message` | string | Submitted message |
| `prodId` | string | Product ID (if inquiry) |
| `product` | object | Product object |
| `success` | boolean | Submission successful |

---

## fb_app

Facebook application info:

| Attribute | Type | Description |
|-----------|------|-------------|
| `id` | string | Facebook App ID |
| `namespace` | string | App namespace |
