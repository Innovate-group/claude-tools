# Translations.txt Guide

## Overview

The `translations.txt` file provides multi-language support for all text strings in the theme. It supports 4 languages:

- `es` - Spanish (Argentina) - **Base language**
- `pt` - Portuguese (Brazil)
- `en` - English
- `es_mx` - Spanish (Mexico)

## Syntax

```
es "Spanish text"
pt "Portuguese text"
en "English text"
es_mx "Mexican Spanish text"
```

**Rules:**
- Each translation on its own line
- Text must be in double quotes
- No trailing commas or semicolons
- Blank line between translation groups

## Usage in Templates

### Basic Translation
```twig
{{ 'Agregar al carrito' | translate }}
```

### With Variables
```twig
{{ 'Envío gratis a partir de {1}' | translate(minimum_amount | money) }}
```

**In translations.txt:**
```
es "Envío gratis a partir de {1}"
pt "Frete grátis a partir de {1}"
en "Free shipping on orders over {1}"
es_mx "Envío gratis a partir de {1}"
```

### Pluralization
```twig
{{ 'producto en tu carrito' | translate | pluralize(cart.items_count) }}
```

**In translations.txt:**
```
es "producto en tu carrito"
pt "produto no seu carrinho"
en "product in your cart"
es_mx "producto en tu carrito"

es "productos en tu carrito"
pt "produtos no seu carrinho"
en "products in your cart"
es_mx "productos en tu carrito"
```

**Note:** Singular form first, then plural form immediately after.

## Common Patterns

### Navigation
```
es "Inicio"
pt "Início"
en "Home"
es_mx "Inicio"

es "Productos"
pt "Produtos"
en "Products"
es_mx "Productos"

es "Contacto"
pt "Contato"
en "Contact"
es_mx "Contacto"

es "Ver más"
pt "Ver mais"
en "View more"
es_mx "Ver más"
```

### Product
```
es "Agregar al carrito"
pt "Adicionar ao carrinho"
en "Add to cart"
es_mx "Agregar al carrito"

es "Comprar ahora"
pt "Comprar agora"
en "Buy now"
es_mx "Comprar ahora"

es "Sin stock"
pt "Sem estoque"
en "Out of stock"
es_mx "Sin stock"

es "Últimas unidades"
pt "Últimas unidades"
en "Last units"
es_mx "Últimas unidades"

es "Descripción"
pt "Descrição"
en "Description"
es_mx "Descripción"
```

### Cart
```
es "Carrito de compras"
pt "Carrinho de compras"
en "Shopping cart"
es_mx "Carrito de compras"

es "Tu carrito está vacío"
pt "Seu carrinho está vazio"
en "Your cart is empty"
es_mx "Tu carrito está vacío"

es "Subtotal"
pt "Subtotal"
en "Subtotal"
es_mx "Subtotal"

es "Finalizar compra"
pt "Finalizar compra"
en "Checkout"
es_mx "Finalizar compra"
```

### Forms
```
es "Nombre"
pt "Nome"
en "Name"
es_mx "Nombre"

es "Email"
pt "E-mail"
en "Email"
es_mx "Email"

es "Mensaje"
pt "Mensagem"
en "Message"
es_mx "Mensaje"

es "Enviar"
pt "Enviar"
en "Send"
es_mx "Enviar"

es "Campo obligatorio"
pt "Campo obrigatório"
en "Required field"
es_mx "Campo obligatorio"
```

### Shipping
```
es "Calcular envío"
pt "Calcular frete"
en "Calculate shipping"
es_mx "Calcular envío"

es "Código postal"
pt "CEP"
en "Zip code"
es_mx "Código postal"

es "Envío gratis"
pt "Frete grátis"
en "Free shipping"
es_mx "Envío gratis"

es "Retiro en tienda"
pt "Retirada na loja"
en "Store pickup"
es_mx "Retiro en tienda"
```

### Account
```
es "Iniciar sesión"
pt "Entrar"
en "Log in"
es_mx "Iniciar sesión"

es "Crear cuenta"
pt "Criar conta"
en "Create account"
es_mx "Crear cuenta"

es "Cerrar sesión"
pt "Sair"
en "Log out"
es_mx "Cerrar sesión"

es "Mi cuenta"
pt "Minha conta"
en "My account"
es_mx "Mi cuenta"

es "Mis pedidos"
pt "Meus pedidos"
en "My orders"
es_mx "Mis pedidos"
```

### Errors & Messages
```
es "Ocurrió un error"
pt "Ocorreu um erro"
en "An error occurred"
es_mx "Ocurrió un error"

es "Producto agregado al carrito"
pt "Produto adicionado ao carrinho"
en "Product added to cart"
es_mx "Producto agregado al carrito"

es "No se encontraron resultados"
pt "Nenhum resultado encontrado"
en "No results found"
es_mx "No se encontraron resultados"
```

## Admin Panel Translations

Translations for settings.txt descriptions also go in translations.txt:

```
es "Mostrar carrusel de imágenes"
pt "Mostrar carrossel de imagens"
en "Show image carousel"
es_mx "Mostrar carrusel de imágenes"

es "Color principal de tu marca"
pt "Cor principal da sua marca"
en "Your brand's main color"
es_mx "Color principal de tu marca"
```

## HTML in Translations

You can include HTML, but be careful:

```
es "¿Necesitás ayuda? <a href='/contacto'>Contactanos</a>"
pt "Precisa de ajuda? <a href='/contacto'>Entre em contato</a>"
en "Need help? <a href='/contacto'>Contact us</a>"
es_mx "¿Necesitas ayuda? <a href='/contacto'>Contáctanos</a>"
```

**In templates with HTML:**
```twig
{{ 'Text with <strong>HTML</strong>' | translate | raw }}
```

## Validation Rules

1. **All 4 languages required** - Every string needs es, pt, en, es_mx
2. **Exact match required** - Template text must match es translation exactly
3. **Case sensitive** - "Agregar" ≠ "agregar"
4. **Punctuation matters** - "Ver más" ≠ "Ver más."
5. **No trailing spaces** - Clean whitespace

## Debugging

If translation not working:
1. Check exact match in translations.txt
2. Verify all 4 language versions exist
3. Check for invisible characters (copy-paste issues)
4. Ensure template uses `| translate` filter

## File Organization

Keep translations organized by section:

```
{# ============================================
   NAVIGATION
============================================ #}

es "Inicio"
pt "Início"
...

{# ============================================
   PRODUCT
============================================ #}

es "Agregar al carrito"
pt "Adicionar ao carrinho"
...
```

**Note:** Comments use `{# ... #}` or can be plain text lines starting with special characters, but it's best to keep it simple with blank lines as separators.
