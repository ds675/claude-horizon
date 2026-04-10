# Accessibility Standards

Target: WCAG 2.1 AA compliance on every component.

## Global requirements

### Page language
```html
<html lang="{{ request.locale.iso_code }}">
```

### Viewport — never block zoom
```html
<!-- Good -->
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<!-- Bad — blocks low-vision users -->
<meta name="viewport" content="..., maximum-scale=1.0, user-scalable=no">
```

### Skip link (required in layout)
```html
<body>
  <a href="#main" class="skip-link">{{ 'accessibility.skip_to_content' | t }}</a>
  ...
  <main id="main" tabindex="-1">
```

```css
.skip-link {
  position: absolute;
  top: -40px;
  left: 6px;
  background: #000;
  color: #fff;
  padding: 8px;
  text-decoration: none;
  border-radius: 4px;
  z-index: 1000;
}
.skip-link:focus { top: 6px; }
```

### iframes
Always include a descriptive `title` attribute:
```html
<iframe title="{{ 'video.product_demo_title' | t }}" src="...">
```

Avoid `title` attribute on all other elements — use visible text, `aria-label`, or `aria-describedby` instead.

## Focus management

- Use `tabindex="0"` for custom interactive elements
- Never use positive `tabindex` values
- Trap focus inside modals and drawers
- Always use `:focus-visible` (not `:focus`) for focus rings in CSS
- Return focus to the trigger element when a modal/drawer closes

```css
.button:focus-visible {
  outline: 2px solid rgb(var(--color-focus));
  outline-offset: 2px;
}
```

## Colour and contrast

- Normal text: minimum 4.5:1 contrast ratio (WCAG AA)
- Large text (18px+ regular or 14px+ bold): minimum 3:1
- Never rely on colour alone to convey information — use an icon, label, or pattern alongside
- Test with high-contrast mode

## Motion

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

---

## Per-component patterns

### Accordion

```html
<div class="accordion__item">
  <h3 class="accordion__heading" id="heading-{{ block.id }}">
    <button
      class="accordion__trigger"
      aria-expanded="false"
      aria-controls="panel-{{ block.id }}"
    >
      {{ block.settings.heading | escape }}
    </button>
  </h3>
  <div
    id="panel-{{ block.id }}"
    class="accordion__panel"
    role="region"
    aria-labelledby="heading-{{ block.id }}"
    hidden
  >
    {{ block.settings.content }}
  </div>
</div>
```

- Trigger must be a `<button>` — not a `<div>` or `<span>`
- `aria-expanded` on trigger: `"true"` / `"false"`
- `aria-controls` on trigger → panel `id`
- Use `hidden` attribute on panel (toggle via JS, not `display: none`)
- `aria-labelledby` on panel → heading `id`
- Avoid `role="region"` when there are more than 6 accordion items on the page
- Keyboard: Enter/Space toggle, Escape closes + returns focus to trigger

### Modal (`<dialog>`)

```html
<dialog
  id="ProductModal-{{ product.id }}-{{ section.id }}"
  aria-labelledby="ModalTitle-{{ product.id }}-{{ section.id }}"
>
  <h2 id="ModalTitle-{{ product.id }}-{{ section.id }}">{{ product.title | escape }}</h2>
  <form method="dialog">
    <button type="submit" aria-label="{{ 'general.close' | t }}">
      {% render 'icon', icon: 'close' %}
    </button>
  </form>
  <div class="modal__content">...</div>
</dialog>
```

- Use native `<dialog>` — it provides built-in focus trap, backdrop, and `Escape` close
- `aria-labelledby` references the modal heading
- Close button requires `aria-label` when it's icon-only
- On close, return focus to the element that opened the modal

### Carousel

```html
<div
  class="carousel"
  role="region"
  aria-label="{{ 'carousel.label' | t }}"
  aria-roledescription="carousel"
>
  <div class="carousel__track" aria-live="polite">
    {% for block in section.blocks %}
      <div
        class="carousel__slide"
        role="group"
        aria-roledescription="slide"
        aria-label="{{ 'carousel.slide_of' | t: index: forloop.index, total: section.blocks.size }}"
      >
        ...
      </div>
    {% endfor %}
  </div>
  <button aria-label="{{ 'carousel.previous' | t }}" class="carousel__prev">
    {% render 'icon', icon: 'arrow-left' %}
  </button>
  <button aria-label="{{ 'carousel.next' | t }}" class="carousel__next">
    {% render 'icon', icon: 'arrow-right' %}
  </button>
</div>
```

### Forms

```html
<label for="CustomerEmail">{{ 'customer.email' | t }}</label>
<input
  id="CustomerEmail"
  type="email"
  name="customer[email]"
  autocomplete="email"
  required
  aria-required="true"
>

<!-- Error state -->
<input
  id="CustomerEmail"
  type="email"
  aria-invalid="true"
  aria-describedby="CustomerEmailError"
>
<p id="CustomerEmailError" class="form__error" role="alert">
  {{ 'customer.email_invalid' | t }}
</p>
```

- Every input must have a visible `<label>` — never label-less inputs
- Error messages use `role="alert"` and are linked via `aria-describedby`
- Required fields: `required` + `aria-required="true"`
- Invalid fields: `aria-invalid="true"` + description via `aria-describedby`

### Buttons and links

- Icon-only buttons must have `aria-label`
- Links that open a new tab must indicate it: `aria-label="{{ title }} ({{ 'general.new_tab' | t }})"`
- Disabled state: use `disabled` attribute on `<button>`, not just a CSS class
- Loading state: `aria-busy="true"` while in progress

```html
<!-- Icon-only button -->
<button
  class="cart__close"
  aria-label="{{ 'cart.close' | t }}"
>
  {% render 'icon', icon: 'close' %}
</button>
```

### Images

- Every image needs a meaningful `alt` attribute
- Decorative images: `alt=""`
- Never use `alt="image"` or `alt="photo"` — describe the content

### Product cards

```html
<article class="product-card" aria-label="{{ product.title | escape }}">
  <a href="{{ product.url }}" tabindex="-1" aria-hidden="true">
    {{ product.featured_image | image_url: width: 800 | image_tag: loading: 'lazy', alt: '' }}
  </a>
  <h3 class="product-card__title">
    <a href="{{ product.url }}">{{ product.title | escape }}</a>
  </h3>
  {% unless product.available %}
    <span class="product-card__badge">{{ 'products.sold_out' | t }}</span>
  {% endunless %}
</article>
```

### Navigation dropdowns

```html
<nav aria-label="{{ 'navigation.main' | t }}">
  <ul role="list">
    <li>
      <button
        aria-expanded="false"
        aria-controls="Dropdown-{{ link.handle }}"
      >
        {{ link.title | escape }}
      </button>
      <ul id="Dropdown-{{ link.handle }}" role="list" hidden>
        {% for child in link.links %}
          <li><a href="{{ child.url }}">{{ child.title | escape }}</a></li>
        {% endfor %}
      </ul>
    </li>
  </ul>
</nav>
```

### Tabs

```html
<div role="tablist" aria-label="{{ 'tabs.label' | t }}">
  <button
    role="tab"
    id="Tab-1-{{ section.id }}"
    aria-selected="true"
    aria-controls="Panel-1-{{ section.id }}"
    tabindex="0"
  >
    {{ 'tabs.tab_1' | t }}
  </button>
  <button
    role="tab"
    id="Tab-2-{{ section.id }}"
    aria-selected="false"
    aria-controls="Panel-2-{{ section.id }}"
    tabindex="-1"
  >
    {{ 'tabs.tab_2' | t }}
  </button>
</div>
<div
  id="Panel-1-{{ section.id }}"
  role="tabpanel"
  aria-labelledby="Tab-1-{{ section.id }}"
>
  ...
</div>
```

Keyboard: Arrow keys navigate between tabs, Tab key moves into the panel.

---

## Checklist for every interactive component

- [ ] All interactive elements reachable by keyboard
- [ ] Focus indicators visible on all interactive elements (`:focus-visible`)
- [ ] Correct ARIA roles, states, and properties
- [ ] `aria-label` or `aria-labelledby` on all icon-only buttons and regions
- [ ] Colour contrast meets WCAG AA (4.5:1 normal text, 3:1 large text)
- [ ] Motion respects `prefers-reduced-motion`
- [ ] Error messages use `role="alert"` and linked via `aria-describedby`
- [ ] Modals trap focus; return focus on close
- [ ] All images have meaningful `alt` text
