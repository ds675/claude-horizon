# CSS Architecture

## Core rules
- Custom CSS only — no Tailwind, no Bootstrap, no utility classes
- Mobile-first — base styles for mobile, `min-width` queries for larger breakpoints
- Never write inline `style=""` attributes — always CSS variables
- Never hardcode colours, spacing, or font values — always custom properties

## Scoping
All section styles must be scoped to the section ID to prevent bleed:
```css
#shopify-section-{{ section.id }} .hero-banner { }
#shopify-section-{{ section.id }} .hero-banner__heading { }
```

## CSS variables
Two layers:

**1. Horizon global tokens** (already defined — use these first, don't redefine):
```css
var(--color-foreground)
var(--color-background)
var(--color-accent)
var(--font-heading-family)
var(--font-body-family)
var(--spacing-section-padding-top)
var(--spacing-section-padding-bottom)
```
Check Horizon's `assets/base.css` for the full list before declaring new ones.

**2. Section-scoped variables** (injected from schema via Liquid `<style>` block):
```css
/* Defined in section Liquid file */
#shopify-section-{{ section.id }} {
  --hero-bg: {{ section.settings.background_color }};
  --hero-min-height: {{ section.settings.min_height }}px;
}

/* Used in section CSS file */
.hero-banner {
  background-color: var(--hero-bg);
  min-height: var(--hero-min-height);
}
```

## File loading
One CSS file per section, loaded in the section's Liquid file:
```liquid
{{ 'section-hero-banner.css' | asset_url | stylesheet_tag }}
```

Shared component styles (used across 2+ sections) go in `assets/component-[name].css`.

## Breakpoints
Follow Horizon's breakpoint scale:
```css
/* Mobile first — no wrapping needed */
/* Tablet */
@media screen and (min-width: 750px) { }
/* Desktop */
@media screen and (min-width: 990px) { }
/* Wide */
@media screen and (min-width: 1400px) { }
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
