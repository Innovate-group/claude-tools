# Data-Store Selectors Reference

HTML selectors for app anchoring in TiendaNube themes. Use `data-store` attributes instead of CSS classes for reliable integration.

## Navigation & Header

| Element | Selector | Notes |
|---------|----------|-------|
| Store header | `data-store="head"` | Main header container |
| Header desktop | `data-store="head-desktop"` | Desktop-specific header |
| Header mobile | `data-store="head-mobile"` | Mobile-specific header |
| Navigation | `data-store="navigation"` | Navigation links container |
| Account links | `data-store="account-links"` | Login, register, my account |
| Page title | `data-store="page-title"` | Title container (not text) |
| Footer | `data-store="footer"` | Footer container |
| Newsletter form | `data-store="newsletter-form"` | Newsletter signup form |

## Home Page

All home page components are optional.

| Element | Selector | Included In |
|---------|----------|-------------|
| Main slider | `data-store="home-slider"` | All layouts |
| Category banners | `data-store="home-banner-categories"` | All except Lifestyle |
| Promotional banners | `data-store="home-banner-promotional"` | All except Idea, Atlántico, Base |
| News banners | `data-store="home-banner-news"` | Río, Lima, Cali, Uyuni, Toluca |
| Featured banners | `data-store="home-banner-featured"` | Toluca only |
| Text banners | `data-store="home-banner-with-text"` | Lifestyle only |
| Image banners | `data-store="home-banner-with-image"` | Silent only |
| Service banners | `data-store="banner-services"` | All layouts |
| Image+text modules | `data-store="home-image-text-module"` | Simple, Trend, Cali, Morelia, Cubo, Bahia, Lifestyle, Base |
| Featured categories | `data-store="home-categories-featured"` | Multiple layouts |
| Welcome message | `data-store="home-welcome-message"` | Amazonas, Idea, Lima, Cali, Uyuni, Toluca, Morelia |
| Institutional message | `data-store="home-institutional-message"` | Multiple layouts |
| Featured products | `data-store="home-products-featured"` | All layouts |
| New products | `data-store="home-products-new"` | Multiple layouts |
| Sale products | `data-store="home-products-sale"` | Multiple layouts |
| Promotion products | `data-store="home-products-promotion"` | Toluca, Morelia |
| Best sellers | `data-store="home-products-best-seller"` | Toluca only |
| Coming soon | `data-store="home-products-coming-soon"` | Toluca, Lifestyle |
| Recent products | `data-store="home-products-recent"` | Lifestyle only |
| Product slider | `data-store="home-products-slider"` | Material, Idea |
| Main product | `data-store="home-product-main"` | Lima, Cali, Uyuni, Toluca, Morelia |
| Video | `data-store="home-video"` | All layouts |
| Brands | `data-store="home-brands"` | Amazonas, Atlántico, Lima, Cali, Uyuni, Toluca, Morelia |
| Testimonials | `data-store="home-testimonials"` | Lima, Cali, Uyuni, Toluca, Morelia |
| Newsletter module | `data-store="home-newsletter"` | Amazonas, Idea, Trend, Lima, Cali, Uyuni, Toluca, Morelia |
| Instagram feed | `data-store="home-instagram-feed"` | All layouts |
| Facebook page | `data-store="home-facebook-page"` | Simple, Trend, Lifestyle, Silent, Material |
| Twitter feed | `data-store="home-twitter-feed"` | Simple, Material, Trend |

## Product Listing

| Element | Selector | Notes |
|---------|----------|-------|
| Category banner | `data-store="category-banner"` | All layouts |
| Product grid | `data-store="category-grid-{{ category.id }}"` | Dynamic ID |
| Filters container | `data-store="filters-nav"` | Optional feature |
| Filter group | `data-store="filters-group"` | Each filter type |
| Product item | `data-store="product-item-{{ product.id }}"` | Dynamic ID |
| Product name | `data-store="product-item-name-{{ product.id }}"` | Dynamic ID |
| Product price | `data-store="product-item-price-{{ product.id }}"` | Dynamic ID |
| Product image | `data-store="product-item-image-{{ product.id }}"` | Dynamic ID |
| Product info | `data-store="product-item-info-{{ product.id }}"` | Dynamic ID |
| Labels container | `data-store="product-item-labels"` | Label group |
| Offer label | `data-store="product-item-offer-label"` | Discount label |
| Promotion label | `data-store="product-item-promotion-label"` | Promotion label |

## Product Detail

| Element | Selector | Notes |
|---------|----------|-------|
| Detail container | `data-store="product-detail"` | Main container |
| Product image | `data-store="product-image-{{ product.id }}"` | All images + thumbnails |
| Product info | `data-store="product-info-{{ product.id }}"` | All info except image |
| Product name | `data-store="product-name-{{ product.id }}"` | Name element |
| Product price | `data-store="product-price-{{ product.id }}"` | Price element |
| Stock info | `data-store="stock-product-{{ product.id }}-{{ stock }}"` | Stock: 0, number, or "infinite" |
| Product form | `data-store="product-form-{{ product.id }}"` | Form with variants |
| Description | `data-store="product-description-{{ product.id }}"` | Product description |
| Related products | `data-store="related-products"` | Related products section |
| Promotion info | `data-store="product-promotion-info"` | Promotion details |

## Shopping Cart

| Element | Selector | Notes |
|---------|----------|-------|
| Cart page | `data-store="cart-page"` | Full page cart |
| Cart form | `data-store="cart-form"` | Form (page and modal) |
| Cart item | `data-store="cart-item-{{ item.product.id }}"` | Dynamic product ID |
| Shipping calculator | `data-store="shipping-calculator"` | Also on product page |
| Shipping option | `data-store="shipping-calculator-item-{{ option.code }}"` | Dynamic code |
| Physical stores | `data-store="branches"` | Pickup locations |
| Store option | `data-store="branch-item-{{ branch.code }}"` | Dynamic code |
| Cart subtotal | `data-store="cart-subtotal"` | Subtotal container |
| Cart total | `data-store="cart-total"` | Total container |

## User Account

| Element | Selector | Notes |
|---------|----------|-------|
| Login form | `data-store="account-login"` | Login form |
| Register form | `data-store="account-register"` | Registration form |
| Orders list | `data-store="account-orders"` | Orders container |
| Order item | `data-store="account-order-item-{{ order.id }}"` | Order in list |
| Order detail | `data-store="account-order-detail-{{ order.id }}"` | Order details page |

## Contact

| Element | Selector | Notes |
|---------|----------|-------|
| Contact form | `data-store="contact-form"` | Contact page form |

## Usage Example

```javascript
// Find product detail container
const productDetail = document.querySelector('[data-store="product-detail"]');

// Find specific product item
const product123 = document.querySelector('[data-store="product-item-123"]');

// Find cart form
const cartForm = document.querySelector('[data-store="cart-form"]');

// Find all product items
const allProducts = document.querySelectorAll('[data-store^="product-item-"]');
```

## Important Notes

1. **Optional components** - Some selectors may not exist depending on store configuration
2. **Theme customization** - Custom FTP modifications may remove selectors
3. **Don't use CSS classes** - Never anchor to `js-` classes or other styling classes
4. **Request new selectors** - Contact storefronts@tiendanube.com for new anchor points
