# HTML Standards

## Core rules

- Use the most semantically correct native HTML element for the job
- All features must be "Baseline Widely Available" — no cutting-edge APIs in critical paths
- Progressive enhancement: semantic HTML first, CSS second, JS third
- Never break core functionality for users without JS

## Native interactive elements — prefer over JS-powered equivalents

| Use case | Native element | Avoid |
|----------|---------------|-------|
| Accordion / FAQ | `<details>` + `<summary>` | Custom JS toggles |
| Modal / overlay | `<dialog>` | Custom div overlays |
| Tooltip / menu | `popover` attribute | Custom JS tooltips |
| Search form | `<search>` container | Generic `<div>` |
| Form results | `<output>` | `<span>` or `<div>` |

### Accordion with `<details>`

```html
<details class="faq__item">
  <summary class="faq__question">
    {{ 'faq.question_1' | t }}
  </summary>
  <div class="faq__answer">
    {{ 'faq.answer_1' | t }}
  </div>
</details>
```

Use `<details>` for simple show/hide. Use a web component only when animation or cross-item behaviour (allow-one-open) is required.

### Modal with `<dialog>`

```html
<button
  class="product__quick-view"
  onclick="document.getElementById('ProductModal-{{ product.id }}-{{ section.id }}').showModal()"
>
  {{ 'products.quick_view' | t }}
</button>

<dialog id="ProductModal-{{ product.id }}-{{ section.id }}" class="product-modal">
  <form method="dialog">
    <button type="submit" class="modal__close" aria-label="{{ 'general.close' | t }}">
      {% render 'icon', icon: 'close' %}
    </button>
  </form>
  <div class="modal__content">
    {% render 'product-details', product: product %}
  </div>
</dialog>
```

### Popover

```html
<button popovertarget="CartMenu" class="header__cart">
  {{ 'cart.title' | t }} ({{ cart.item_count }})
</button>

<div id="CartMenu" popover class="cart-popover">
  {% render 'cart-drawer' %}
</div>
```

## ID naming convention

IDs must be unique. Section and block IDs are the only Shopify-guaranteed unique values — always suffix them:

```html
<!-- Section-scoped -->
<section id="HeroBanner-{{ section.id }}">

<!-- Block-scoped -->
<div id="ProductCard-{{ block.id }}">

<!-- Product within a section -->
<dialog id="ProductModal-{{ product.id }}-{{ section.id }}">
<form id="ProductForm-{{ product.id }}-{{ section.id }}">
```

## Semantic structure

```html
<!-- Good: semantic element -->
<search class="header__search">
  <form action="{{ routes.search_url }}" method="get">
    <input type="search" name="q" value="{{ search.terms | escape }}">
    <button type="submit">{{ 'general.search' | t }}</button>
  </form>
</search>

<!-- Good: semantic product card -->
<article class="product-card">
  <div class="product-card__media">
    {{ product.featured_image | image_url: width: 800 | image_tag: loading: 'lazy' }}
  </div>
  <div class="product-card__info">
    <h3 class="product-card__title">
      <a href="{{ product.url }}">{{ product.title | escape }}</a>
    </h3>
    <output class="product-card__price">{{ product.price | money }}</output>
  </div>
</article>
```

## Forms

Use native HTML5 validation attributes — do not reimplement in JS:

```html
<input
  type="email"
  name="email"
  required
  autocomplete="email"
  placeholder="{{ 'newsletter.email_placeholder' | t }}"
>

<input
  type="tel"
  name="phone"
  pattern="[0-9]{3}-[0-9]{3}-[0-9]{4}"
  autocomplete="tel"
>
```

Never use `maximum-scale=1.0` or `user-scalable=no` in viewport meta — this breaks low-vision accessibility.

## Images

Always include `loading="lazy"` on below-the-fold images. Always provide meaningful `alt` text:

```liquid
{{
  product.featured_image
  | image_url: width: 800
  | image_tag:
    loading: 'lazy',
    alt: product.featured_image.alt | default: product.title
}}
```

## Progressive enhancement

1. Start with semantic HTML that works without JS
2. Layer CSS for visual enhancement
3. Add JS for advanced interactions
4. Never rely on JS for critical conversion paths (add to cart must work without JS enhancement)

## What NOT to use custom JS for

- Show/hide toggles → use `<details>`
- Modal overlays → use `<dialog>`
- Tooltips → use `popover`
- Form validation → use native HTML5 attributes
- Tabs → use CSS `:target` or native behaviour where possible
