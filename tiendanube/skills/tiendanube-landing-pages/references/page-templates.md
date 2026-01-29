# Landing Page Templates Reference

## page.tpl - Complete Router Example

```twig
{#
  Page Router
  ===========
  Routes page handles to custom landing page templates.
  
  To add a new landing page:
  1. Create snipplet in snipplets/pages/page-{handle}.tpl
  2. Add elseif condition below with all language handle variants
  3. Add settings in config/settings.txt
  
  Registered Pages:
  - About: quienes-somos, about-us, sobre-nos
  - Contact: contacto, contact, contato
  - FAQ: preguntas-frecuentes, faq, perguntas-frequentes
  - Stores: tiendas, stores, lojas
#}

{% set has_custom_template = false %}

{# ============================================= #}
{#              LANDING PAGE ROUTER              #}
{# ============================================= #}

{# About Us #}
{% if page.handle == 'quienes-somos' 
   or page.handle == 'about-us' 
   or page.handle == 'sobre-nos'
   or page.handle == settings.page_about_handle %}
  {% set has_custom_template = true %}
  {% include 'snipplets/pages/page-about.tpl' %}

{# Contact #}
{% elseif page.handle == 'contacto' 
       or page.handle == 'contact' 
       or page.handle == 'contato'
       or page.handle == settings.page_contact_handle %}
  {% set has_custom_template = true %}
  {% include 'snipplets/pages/page-contact.tpl' %}

{# FAQ #}
{% elseif page.handle == 'preguntas-frecuentes' 
       or page.handle == 'faq' 
       or page.handle == 'perguntas-frequentes'
       or page.handle == settings.page_faq_handle %}
  {% set has_custom_template = true %}
  {% include 'snipplets/pages/page-faq.tpl' %}

{# Store Locator #}
{% elseif page.handle == 'tiendas' 
       or page.handle == 'stores' 
       or page.handle == 'lojas'
       or page.handle == 'locales'
       or page.handle == settings.page_stores_handle %}
  {% set has_custom_template = true %}
  {% include 'snipplets/pages/page-stores.tpl' %}

{# Lookbook / Collection #}
{% elseif page.handle == 'lookbook' 
       or page.handle == 'coleccion'
       or page.handle == settings.page_lookbook_handle %}
  {% set has_custom_template = true %}
  {% include 'snipplets/pages/page-lookbook.tpl' %}

{# ============================================= #}
{#           ADD NEW PAGES ABOVE THIS            #}
{# ============================================= #}

{% endif %}

{# ============================================= #}
{#             DEFAULT PAGE LAYOUT               #}
{# ============================================= #}

{% if not has_custom_template %}
<article class="page-default" data-store="page-content">
  <div class="container py-5">
    <div class="row justify-content-center">
      <div class="col-12 col-lg-10 col-xl-8">
        
        {# Page Title #}
        <header class="page-header mb-4">
          <h1 class="page-title">{{ page.name }}</h1>
        </header>
        
        {# Page Content #}
        <div class="page-body user-content">
          {{ page.content | raw }}
        </div>
        
      </div>
    </div>
  </div>
</article>
{% endif %}
```

---

## page-default.tpl - Default Page Snipplet

```twig
{#
  Default Page Layout
  Used when no custom template matches the handle
  Can also be included explicitly for simple pages
#}

<article class="page-default" data-store="page-content">
  <div class="container py-5">
    <div class="row justify-content-center">
      <div class="col-12 col-lg-10 col-xl-8">
        
        {# Breadcrumbs (optional) #}
        {% if settings.show_breadcrumbs %}
          {% include 'snipplets/breadcrumbs.tpl' %}
        {% endif %}
        
        {# Page Title #}
        <header class="page-header mb-4">
          <h1 class="page-title">{{ page.name }}</h1>
        </header>
        
        {# Page Content #}
        <div class="page-body user-content">
          {{ page.content | raw }}
        </div>
        
      </div>
    </div>
  </div>
</article>
```

---

## page-contact.tpl - Contact Page

```twig
{#
  Landing Page: Contact
  Handle: contacto | contact | contato
  Settings prefix: page_contact_
#}

{% set spacing_classes = [
  'section-mt-' ~ settings.page_contact_margin_top,
  'section-mb-' ~ settings.page_contact_margin_bottom,
  'section-pt-' ~ settings.page_contact_padding_top,
  'section-pb-' ~ settings.page_contact_padding_bottom
] | join(' ') %}

<article class="landing-page page-contact {{ spacing_classes }}" data-store="page-contact">
  
  {# Hero Section #}
  {% set has_hero_desktop = store.has_theme_image('page_contact_hero_desktop.jpg') %}
  {% set has_hero_mobile = store.has_theme_image('page_contact_hero_mobile.jpg') %}
  
  {% if has_hero_desktop or has_hero_mobile %}
  <section class="page-hero page-hero-small" data-store="page-contact-hero">
    <picture>
      {% if has_hero_mobile %}
        <source media="(max-width: 767px)" srcset="{{ 'page_contact_hero_mobile.jpg' | static_url }}">
      {% endif %}
      {% if has_hero_desktop %}
        <img 
          src="{{ 'page_contact_hero_desktop.jpg' | static_url }}" 
          alt="{{ settings.page_contact_hero_alt }}"
          class="img-fluid w-100"
        >
      {% endif %}
    </picture>
    <div class="page-hero-overlay">
      <div class="container">
        <h1 class="page-hero-title">{{ settings.page_contact_title | default(page.name) }}</h1>
      </div>
    </div>
  </section>
  {% else %}
  <section class="page-header-simple py-5 bg-light">
    <div class="container">
      <h1 class="text-center">{{ settings.page_contact_title | default(page.name) }}</h1>
    </div>
  </section>
  {% endif %}
  
  {# Main Content #}
  <section class="page-content py-5" data-store="page-contact-content">
    <div class="container">
      <div class="row">
        
        {# Contact Info Column #}
        <div class="col-12 col-lg-4 mb-4 mb-lg-0">
          <div class="contact-info">
            
            {% if settings.page_contact_address %}
            <div class="contact-item mb-4">
              <h4 class="contact-item-title">
                <i class="fas fa-map-marker-alt mr-2"></i>
                {{ 'Dirección' | translate }}
              </h4>
              <p>{{ settings.page_contact_address }}</p>
            </div>
            {% endif %}
            
            {% if settings.page_contact_phone %}
            <div class="contact-item mb-4">
              <h4 class="contact-item-title">
                <i class="fas fa-phone mr-2"></i>
                {{ 'Teléfono' | translate }}
              </h4>
              <p>
                <a href="tel:{{ settings.page_contact_phone | replace({' ': '', '-': ''}) }}">
                  {{ settings.page_contact_phone }}
                </a>
              </p>
            </div>
            {% endif %}
            
            {% if settings.page_contact_email %}
            <div class="contact-item mb-4">
              <h4 class="contact-item-title">
                <i class="fas fa-envelope mr-2"></i>
                {{ 'Email' | translate }}
              </h4>
              <p>
                <a href="mailto:{{ settings.page_contact_email }}">
                  {{ settings.page_contact_email }}
                </a>
              </p>
            </div>
            {% endif %}
            
            {% if settings.page_contact_hours %}
            <div class="contact-item mb-4">
              <h4 class="contact-item-title">
                <i class="fas fa-clock mr-2"></i>
                {{ 'Horario de atención' | translate }}
              </h4>
              <p>{{ settings.page_contact_hours | nl2br }}</p>
            </div>
            {% endif %}
            
            {# Social Links #}
            {% if store.facebook or store.instagram or store.twitter %}
            <div class="contact-social mt-4">
              <h4 class="contact-item-title">{{ 'Seguinos' | translate }}</h4>
              <div class="social-links">
                {% if store.facebook %}
                  <a href="{{ store.facebook }}" target="_blank" rel="noopener" class="social-link">
                    <i class="fab fa-facebook-f"></i>
                  </a>
                {% endif %}
                {% if store.instagram %}
                  <a href="{{ store.instagram }}" target="_blank" rel="noopener" class="social-link">
                    <i class="fab fa-instagram"></i>
                  </a>
                {% endif %}
                {% if store.twitter %}
                  <a href="{{ store.twitter }}" target="_blank" rel="noopener" class="social-link">
                    <i class="fab fa-twitter"></i>
                  </a>
                {% endif %}
              </div>
            </div>
            {% endif %}
            
          </div>
        </div>
        
        {# Contact Form Column #}
        <div class="col-12 col-lg-8">
          <div class="contact-form-wrapper">
            <h3 class="mb-4">{{ 'Envianos un mensaje' | translate }}</h3>
            
            {# TiendaNube Contact Form #}
            {% include 'snipplets/contact-form.tpl' %}
            
          </div>
        </div>
        
      </div>
    </div>
  </section>
  
  {# Map Section #}
  {% if settings.page_contact_show_map and settings.page_contact_map_embed %}
  <section class="page-map" data-store="page-contact-map">
    <div class="map-container">
      {{ settings.page_contact_map_embed | raw }}
    </div>
  </section>
  {% endif %}
  
</article>
```

**Settings for Contact Page:**
```
Página Contacto
	meta
		icon = envelope
		advanced = true
	collapse
		title = Hero / Banner
	subtitle
		subtitle = Imagen de escritorio
	image
		original = page_contact_hero_desktop.jpg
		title = Cargar imagen (1920x300 recomendado)
		width = 1920
		height = 300
	subtitle
		subtitle = Imagen de celulares
	image
		original = page_contact_hero_mobile.jpg
		title = Cargar imagen (768x400 recomendado)
		width = 768
		height = 400
	i18n_input
		name = page_contact_hero_alt
		description = Texto alternativo (SEO)
	i18n_input
		name = page_contact_title
		description = Título de la página
	collapse
		title = Información de contacto
	i18n_input
		name = page_contact_address
		description = Dirección
	text
		name = page_contact_phone
		description = Teléfono
		placeholder = +54 11 1234-5678
	text
		name = page_contact_email
		description = Email
		placeholder = contacto@tienda.com
	textarea
		name = page_contact_hours
		description = Horario de atención
	collapse
		title = Mapa
	checkbox
		name = page_contact_show_map
		description = Mostrar mapa
	textarea
		name = page_contact_map_embed
		description = Código embed de Google Maps
	collapse
		title = Espaciado
	dropdown
		name = page_contact_margin_top
		description = Margen superior
		values
			none = Sin margen
			small = Pequeño
			medium = Mediano
			large = Grande
	dropdown
		name = page_contact_margin_bottom
		description = Margen inferior
		values
			none = Sin margen
			small = Pequeño
			medium = Mediano
			large = Grande
	dropdown
		name = page_contact_padding_top
		description = Padding superior
		values
			none = Sin padding
			small = Pequeño
			medium = Mediano
			large = Grande
	dropdown
		name = page_contact_padding_bottom
		description = Padding inferior
		values
			none = Sin padding
			small = Pequeño
			medium = Mediano
			large = Grande
```

---

## page-faq.tpl - FAQ Page with Accordion

```twig
{#
  Landing Page: FAQ
  Handle: preguntas-frecuentes | faq | perguntas-frequentes
  Settings prefix: page_faq_
#}

{% set spacing_classes = [
  'section-mt-' ~ settings.page_faq_margin_top,
  'section-mb-' ~ settings.page_faq_margin_bottom,
  'section-pt-' ~ settings.page_faq_padding_top,
  'section-pb-' ~ settings.page_faq_padding_bottom
] | join(' ') %}

<article class="landing-page page-faq {{ spacing_classes }}" data-store="page-faq">
  
  {# Header #}
  <section class="page-header-simple py-5 bg-light">
    <div class="container">
      <h1 class="text-center">{{ settings.page_faq_title | default(page.name) }}</h1>
      {% if settings.page_faq_subtitle %}
        <p class="text-center text-muted mt-2">{{ settings.page_faq_subtitle }}</p>
      {% endif %}
    </div>
  </section>
  
  {# FAQ Content #}
  <section class="page-content py-5" data-store="page-faq-content">
    <div class="container">
      <div class="row justify-content-center">
        <div class="col-12 col-lg-10 col-xl-8">
          
          {# FAQ Accordion #}
          <div class="accordion faq-accordion" id="faqAccordion">
            
            {% for i in 1..10 %}
              {% set question = attribute(settings, 'page_faq_q' ~ i) %}
              {% set answer = attribute(settings, 'page_faq_a' ~ i) %}
              
              {% if question and answer %}
              <div class="card faq-item">
                <div class="card-header" id="faqHeading{{ i }}">
                  <h2 class="mb-0">
                    <button 
                      class="btn btn-link btn-block text-left {% if i > 1 %}collapsed{% endif %}" 
                      type="button" 
                      data-toggle="collapse" 
                      data-target="#faqCollapse{{ i }}" 
                      aria-expanded="{{ i == 1 ? 'true' : 'false' }}" 
                      aria-controls="faqCollapse{{ i }}"
                    >
                      {{ question }}
                      <i class="fas fa-chevron-down faq-icon"></i>
                    </button>
                  </h2>
                </div>
                
                <div 
                  id="faqCollapse{{ i }}" 
                  class="collapse {% if i == 1 %}show{% endif %}" 
                  aria-labelledby="faqHeading{{ i }}" 
                  data-parent="#faqAccordion"
                >
                  <div class="card-body">
                    {{ answer | raw }}
                  </div>
                </div>
              </div>
              {% endif %}
            {% endfor %}
            
          </div>
          
          {# Additional Content from Admin (optional) #}
          {% if page.content %}
          <div class="faq-additional-content mt-5 user-content">
            {{ page.content | raw }}
          </div>
          {% endif %}
          
          {# Contact CTA #}
          {% if settings.page_faq_show_contact_cta %}
          <div class="faq-contact-cta text-center mt-5 p-4 bg-light rounded">
            <h4>{{ '¿No encontraste lo que buscabas?' | translate }}</h4>
            <p class="mb-3">{{ 'Contactanos y te responderemos a la brevedad.' | translate }}</p>
            <a href="{{ store.contact_url }}" class="btn btn-primary">
              {{ 'Contactar' | translate }}
            </a>
          </div>
          {% endif %}
          
        </div>
      </div>
    </div>
  </section>
  
</article>

{# FAQ Schema Markup (JSON-LD) #}
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {% set first = true %}
    {% for i in 1..10 %}
      {% set question = attribute(settings, 'page_faq_q' ~ i) %}
      {% set answer = attribute(settings, 'page_faq_a' ~ i) %}
      {% if question and answer %}
        {% if not first %},{% endif %}
        {
          "@type": "Question",
          "name": {{ question | json_encode | raw }},
          "acceptedAnswer": {
            "@type": "Answer",
            "text": {{ answer | striptags | json_encode | raw }}
          }
        }
        {% set first = false %}
      {% endif %}
    {% endfor %}
  ]
}
</script>
```

**Settings for FAQ Page:**
```
Página Preguntas Frecuentes
	meta
		icon = question-circle
		advanced = true
	collapse
		title = Configuración general
	i18n_input
		name = page_faq_title
		description = Título de la página
	i18n_input
		name = page_faq_subtitle
		description = Subtítulo (opcional)
	checkbox
		name = page_faq_show_contact_cta
		description = Mostrar botón de contacto al final
	collapse
		title = Pregunta 1
	i18n_input
		name = page_faq_q1
		description = Pregunta
	textarea
		name = page_faq_a1
		description = Respuesta (HTML permitido)
	collapse
		title = Pregunta 2
	i18n_input
		name = page_faq_q2
		description = Pregunta
	textarea
		name = page_faq_a2
		description = Respuesta (HTML permitido)
	collapse
		title = Pregunta 3
	i18n_input
		name = page_faq_q3
		description = Pregunta
	textarea
		name = page_faq_a3
		description = Respuesta (HTML permitido)
	collapse
		title = Pregunta 4
	i18n_input
		name = page_faq_q4
		description = Pregunta
	textarea
		name = page_faq_a4
		description = Respuesta (HTML permitido)
	collapse
		title = Pregunta 5
	i18n_input
		name = page_faq_q5
		description = Pregunta
	textarea
		name = page_faq_a5
		description = Respuesta (HTML permitido)
	collapse
		title = Espaciado
	dropdown
		name = page_faq_margin_top
		description = Margen superior
		values
			none = Sin margen
			small = Pequeño
			medium = Mediano
			large = Grande
	dropdown
		name = page_faq_margin_bottom
		description = Margen inferior
		values
			none = Sin margen
			small = Pequeño
			medium = Mediano
			large = Grande
	dropdown
		name = page_faq_padding_top
		description = Padding superior
		values
			none = Sin padding
			small = Pequeño
			medium = Mediano
			large = Grande
	dropdown
		name = page_faq_padding_bottom
		description = Padding inferior
		values
			none = Sin padding
			small = Pequeño
			medium = Mediano
			large = Grande
```

---

## page-stores.tpl - Store Locator

```twig
{#
  Landing Page: Store Locator
  Handle: tiendas | stores | lojas | locales
  Settings prefix: page_stores_
#}

{% set spacing_classes = [
  'section-mt-' ~ settings.page_stores_margin_top,
  'section-mb-' ~ settings.page_stores_margin_bottom,
  'section-pt-' ~ settings.page_stores_padding_top,
  'section-pb-' ~ settings.page_stores_padding_bottom
] | join(' ') %}

<article class="landing-page page-stores {{ spacing_classes }}" data-store="page-stores">
  
  {# Header #}
  <section class="page-header-simple py-5 bg-light">
    <div class="container">
      <h1 class="text-center">{{ settings.page_stores_title | default(page.name) }}</h1>
      {% if settings.page_stores_subtitle %}
        <p class="text-center text-muted mt-2">{{ settings.page_stores_subtitle }}</p>
      {% endif %}
    </div>
  </section>
  
  {# Stores List #}
  <section class="page-content py-5" data-store="page-stores-content">
    <div class="container">
      <div class="row">
        
        {% for i in 1..6 %}
          {% set store_name = attribute(settings, 'page_stores_' ~ i ~ '_name') %}
          {% set store_address = attribute(settings, 'page_stores_' ~ i ~ '_address') %}
          {% set store_image = 'page_stores_' ~ i ~ '.jpg' %}
          
          {% if store_name and store_address %}
          <div class="col-12 col-md-6 col-lg-4 mb-4">
            <div class="store-card h-100">
              
              {# Store Image #}
              {% if store.has_theme_image(store_image) %}
              <div class="store-card-image">
                <img 
                  src="{{ store_image | static_url }}" 
                  alt="{{ store_name }}"
                  class="img-fluid w-100"
                  loading="lazy"
                >
              </div>
              {% endif %}
              
              <div class="store-card-body p-4">
                <h3 class="store-card-title">{{ store_name }}</h3>
                
                <p class="store-card-address">
                  <i class="fas fa-map-marker-alt text-primary mr-2"></i>
                  {{ store_address }}
                </p>
                
                {% set store_phone = attribute(settings, 'page_stores_' ~ i ~ '_phone') %}
                {% if store_phone %}
                <p class="store-card-phone">
                  <i class="fas fa-phone text-primary mr-2"></i>
                  <a href="tel:{{ store_phone | replace({' ': '', '-': ''}) }}">{{ store_phone }}</a>
                </p>
                {% endif %}
                
                {% set store_hours = attribute(settings, 'page_stores_' ~ i ~ '_hours') %}
                {% if store_hours %}
                <p class="store-card-hours">
                  <i class="fas fa-clock text-primary mr-2"></i>
                  {{ store_hours }}
                </p>
                {% endif %}
                
                {% set store_map = attribute(settings, 'page_stores_' ~ i ~ '_map_url') %}
                {% if store_map %}
                <a href="{{ store_map }}" target="_blank" rel="noopener" class="btn btn-outline-primary btn-sm mt-3">
                  <i class="fas fa-directions mr-1"></i>
                  {{ 'Cómo llegar' | translate }}
                </a>
                {% endif %}
              </div>
              
            </div>
          </div>
          {% endif %}
        {% endfor %}
        
      </div>
    </div>
  </section>
  
</article>
```

---

## CSS for Landing Pages

```scss
/* ================================
   Landing Pages - Base Styles
   ================================ */

.landing-page {
  
  /* Hero Sections */
  .page-hero {
    position: relative;
    overflow: hidden;
    
    img {
      width: 100%;
      height: auto;
      display: block;
    }
    
    &-overlay {
      position: absolute;
      top: 0;
      left: 0;
      right: 0;
      bottom: 0;
      background: rgba(0, 0, 0, 0.4);
      display: flex;
      align-items: center;
      justify-content: center;
    }
    
    &-title {
      color: #fff;
      text-align: center;
      font-size: 2.5rem;
      text-shadow: 0 2px 4px rgba(0, 0, 0, 0.3);
      
      @media (max-width: 767px) {
        font-size: 1.75rem;
      }
    }
    
    &-subtitle {
      color: rgba(255, 255, 255, 0.9);
      text-align: center;
      font-size: 1.25rem;
    }
    
    &-small img {
      max-height: 300px;
      object-fit: cover;
    }
  }
  
  /* Content Sections */
  .page-content {
    .user-content {
      img {
        max-width: 100%;
        height: auto;
      }
      
      a {
        color: var(--primary-color);
      }
    }
  }
}

/* FAQ Accordion */
.faq-accordion {
  .faq-item {
    border: none;
    border-bottom: 1px solid #eee;
    border-radius: 0 !important;
    
    .card-header {
      background: none;
      border: none;
      padding: 0;
      
      button {
        padding: 1.25rem 0;
        font-weight: 600;
        color: #333;
        text-decoration: none;
        display: flex;
        justify-content: space-between;
        align-items: center;
        
        &:hover {
          text-decoration: none;
          color: var(--primary-color);
        }
        
        .faq-icon {
          transition: transform 0.3s;
        }
        
        &:not(.collapsed) .faq-icon {
          transform: rotate(180deg);
        }
      }
    }
    
    .card-body {
      padding: 0 0 1.25rem;
      color: #666;
    }
  }
}

/* Store Cards */
.store-card {
  border: 1px solid #eee;
  border-radius: 8px;
  overflow: hidden;
  transition: box-shadow 0.3s;
  
  &:hover {
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  }
  
  &-image {
    height: 200px;
    overflow: hidden;
    
    img {
      width: 100%;
      height: 100%;
      object-fit: cover;
    }
  }
  
  &-title {
    font-size: 1.25rem;
    margin-bottom: 1rem;
  }
  
  &-address,
  &-phone,
  &-hours {
    font-size: 0.9rem;
    margin-bottom: 0.5rem;
    color: #666;
  }
}

/* Contact Info */
.contact-info {
  .contact-item-title {
    font-size: 1rem;
    font-weight: 600;
    margin-bottom: 0.5rem;
    
    i {
      color: var(--primary-color);
    }
  }
}

/* Map Container */
.map-container {
  iframe {
    width: 100%;
    height: 400px;
    border: none;
  }
}

/* Social Links */
.social-links {
  display: flex;
  gap: 1rem;
  
  .social-link {
    width: 40px;
    height: 40px;
    border-radius: 50%;
    background: var(--primary-color);
    color: #fff;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: opacity 0.3s;
    
    &:hover {
      opacity: 0.8;
      color: #fff;
    }
  }
}
```
