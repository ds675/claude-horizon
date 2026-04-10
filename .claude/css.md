# CSS Architecture

## Core rules

- Custom CSS only — no Tailwind, no Bootstrap, no utility frameworks
- Mobile-first — base styles for mobile, `min-width` queries for larger breakpoints
- All CSS lives in `{% stylesheet %}` tags inside the Liquid file — no separate `.css` files
- CSS variables bridge schema settings to styles — injected inline on the element, not in a `<style>` block
- Never hardcode colours, spacing, or font values — always custom properties
- Use logical properties (`padding-inline`, `margin-block`, `border-inline-end`) for RTL support

## Specificity rules

- **Never** use IDs as CSS selectors
- **Avoid** element selectors — use a single `.class` wherever possible (`0 1 0` specificity)
- **Avoid** `!important` — if you must use it, add a comment explaining why
- Maximum specificity: `0 4 0` (four classes chained) — never exceed this
- **Avoid** complex selectors — a selector must be readable at a glance
- Avoid overusing pseudo-selectors like `:has()`, `:where()`, `:nth-child()`

### `:has()` performance note

`:has()` is useful but re-evaluates on every DOM update — a real cost in Shopify themes where cart, variant selection, and filters mutate the DOM constantly.

```css
/* Avoid: broad subtree traversal */
.ancestor:has(.foo) { }

/* Good: constrain with direct child combinator */
.ancestor:has(> .foo) { }

/* Better: add a server-rendered class instead of relying on :has() */
.filter-label.disabled { } /* set by Liquid, not :has(input[disabled]) */
```

## CSS variables — two layers

### 1. Horizon global tokens (use first — do not redefine)

```css
var(--color-foreground)
var(--color-background)
var(--color-accent)
var(--font-heading-family)
var(--font-body-family)
var(--spacing-section-padding-top)
var(--spacing-section-padding-bottom)
```

Check `assets/base.css` for the full token list before declaring new variables.

### 2. Component-scoped variables (injected from schema)

Inject on the element's `style` attribute in Liquid — not in a `<style>` block:

```liquid
<section
  class="hero-banner"
  style="
    --hero-bg: {{ section.settings.background_color }};
    --hero-min-height: {{ section.settings.min_height }}px;
    --hero-text: {{ section.settings.text_color }};
  "
>
```

```css
/* In {% stylesheet %} */
.hero-banner {
  background-color: var(--hero-bg);
  min-height: var(--hero-min-height);
  color: var(--hero-text);
}
```

### Namespace your variables

Always prefix component variables with the component name to avoid collisions:

```css
/* Good */
.accordion {
  --accordion-padding: var(--spacing-md);
  --accordion-border: 1px solid rgb(var(--color-foreground) / 0.15);
}

/* Bad: generic names bleed through */
.accordion {
  --padding: var(--spacing-md);
  --border: 1px solid ...;
}
```

### Semantic color tokens

```css
:root {
  --color-text-primary: rgb(var(--color-foreground));
  --color-text-secondary: rgb(var(--color-foreground) / 0.75);
  --color-text-disabled: rgb(var(--color-foreground) / 0.38);
  --color-interactive-default: rgb(var(--color-accent));
  /* color-mix is iOS <16.2 only — limit to progressive enhancement */
  --color-interactive-hover: color-mix(in srgb, rgb(var(--color-accent)) 90%, black);
}
```

### Spacing + typography scale

```css
:root {
  --space-3xs: 0.25rem;   /* 4px */
  --space-2xs: 0.5rem;    /* 8px */
  --space-xs:  0.75rem;   /* 12px */
  --space-sm:  1rem;      /* 16px */
  --space-md:  1.5rem;    /* 24px */
  --space-lg:  2rem;      /* 32px */
  --space-xl:  3rem;      /* 48px */
  --space-2xl: 4rem;      /* 64px */
  --space-3xl: 6rem;      /* 96px */

  --font-size-xs:   0.75rem;   /* 12px */
  --font-size-sm:   0.875rem;  /* 14px */
  --font-size-base: 1rem;      /* 16px */
  --font-size-lg:   1.125rem;  /* 18px */
  --font-size-xl:   1.25rem;   /* 20px */
  --font-size-2xl:  1.5rem;    /* 24px */
  --font-size-3xl:  1.875rem;  /* 30px */
}
```

## Scoping

Scope CSS to the section or block class, not the Shopify wrapper ID. Use the section type as the class:

```liquid
{% liquid assign section_class = 'section-' | append: section.type %}
<section class="{{ section_class }}">
```

```css
.section-hero-banner { }
.section-hero-banner__heading { }
```

## BEM structure

```css
.hero-banner { }
.hero-banner__heading { }
.hero-banner__image { }
.hero-banner__cta { }
.hero-banner--full-height { }
.hero-banner__heading--large { }
```

Single element level only — never nest (`__wrapper__button` is invalid BEM).
Modifier always applied alongside base class: `<button class="button button--secondary">`.

## Breakpoints

Mobile-first. Follow Horizon's breakpoint scale:

```css
/* Base: mobile — no media query needed */

@media screen and (min-width: 576px) { }   /* Small */
@media screen and (min-width: 750px) { }   /* Tablet */
@media screen and (min-width: 990px) { }   /* Desktop */
@media screen and (min-width: 1200px) { }  /* Large */
@media screen and (min-width: 1400px) { }  /* Wide */
```

Use nested media queries — write them inside the selector:

```css
.hero-banner {
  padding: var(--space-lg) 0;

  @media screen and (min-width: 990px) {
    padding: var(--space-3xl) 0;
  }
}
```

## CSS nesting rules

- Only use `&` for state-based selectors: `&:hover`, `&:focus-visible`, `&:disabled`
- Only use `&` for modifier-to-modifier relationships: `.button--integrated { &.button--text { } }`
- Never nest beyond one level (except media queries)
- Use parent-modifier nesting when multiple children need to change together:

```css
/* Good: modifier affecting multiple children */
.product-card--compact {
  .product-card__image { aspect-ratio: 1; }
  .product-card__title { font-size: var(--font-size-sm); }
}
```

## Container queries

Prefer container queries for truly responsive components:

```css
.product-grid {
  container-type: inline-size;
}

@container (min-width: 400px) {
  .product-card {
    display: grid;
    grid-template-columns: 1fr 1fr;
  }
}
```

## Logical properties

Use logical properties for baseline RTL support:

```css
/* Good */
.element {
  padding-inline: 2rem;
  padding-block: 1rem;
  margin-inline: auto;
  border-inline-end: 1px solid var(--color-border);
  text-align: start;
  inset: 0;
}

/* Avoid */
.element {
  padding: 1rem 2rem;
  margin: 0 auto;
  border-right: 1px solid var(--color-border);
  text-align: left;
}
```

## Modern CSS usage

```css
/* Fluid spacing */
padding: clamp(1rem, 4vw, 3rem);

/* Intrinsic sizing */
width: min(100%, 800px);

/* Avoid vh on mobile — use dvh */
min-height: 100dvh;

/* color-mix: limit to progressive enhancement (no iOS <16.2) */
background: color-mix(in srgb, rgb(var(--color-accent)) 90%, white);
```

## Layout patterns

```css
/* Grid for page-level layouts */
.section-content {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: var(--space-lg);
}

/* Flexbox for component internals */
.product-card {
  display: flex;
  flex-direction: column;
  gap: var(--space-sm);
}

/* Aspect ratio for media */
.product-card__image {
  aspect-ratio: 4 / 3;
  object-fit: cover;
}
```

## Property order

Within a declaration, use this order:

```css
.component {
  /* 1. Layout & positioning */
  position: relative;
  display: flex;
  flex-direction: column;

  /* 2. Box model */
  width: 100%;
  margin: 0;
  padding: var(--space-md);
  border: 1px solid rgb(var(--color-border));

  /* 3. Typography */
  font-family: var(--font-body-family);
  font-size: var(--font-size-base);

  /* 4. Visual */
  background: rgb(var(--color-surface));
  color: var(--color-text-primary);

  /* 5. Animation */
  transition: transform 0.2s ease;
}
```

## Accessibility in CSS

```css
/* Always respect reduced motion */
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}

/* Use :focus-visible — not :focus — for keyboard indicators */
.button:focus-visible {
  outline: 2px solid rgb(var(--color-focus));
  outline-offset: 2px;
}
```

## Animation performance

```css
/* Animate only transform and opacity */
.product-card {
  transition: transform 0.2s ease;
}

.product-card:hover {
  transform: translateY(-2px);
}

/* Use will-change only during animation, reset after */
.product-card:hover { will-change: transform; }
.product-card:not(:hover) { will-change: auto; }
```

## `:is()` selector

```css
/* Use comma list for unrelated selectors */
.facets__label,
.facets__clear-all,
.clear-filter { }

/* Use :is() for shared parent-child selectors */
:is(.parent, .parent-2) .child { }
.parent:is(.child-1, .child-2) { }
```

## Defensive CSS

```css
.product-card {
  overflow-wrap: break-word;
  min-width: 0; /* Allows flex items to shrink below content size */
  aspect-ratio: 1 / 1;
  background: rgb(var(--color-surface-secondary)); /* Fallback for missing images */
}
```
