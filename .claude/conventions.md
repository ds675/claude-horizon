# Conventions

## File naming

| Type | Pattern | Example |
|------|---------|---------|
| Section | `sections/[name].liquid` | `sections/hero-banner.liquid` |
| Block | `blocks/[name].liquid` | `blocks/product-card.liquid` |
| Snippet | `snippets/[name].liquid` | `snippets/price.liquid` |
| Section group | `sections/[name]-group.json` | `sections/footer-group.json` |

- Always kebab-case
- No separate CSS/JS files — styles go in `{% stylesheet %}`, scripts in `{% javascript %}` within the Liquid file
- Shared assets (images, icons, fonts) live in `assets/` — it is a flat directory, no subdirectories

## CSS naming — BEM strictly

```
block__element--modifier
hero-banner__heading--large
product-card__image--placeholder
```

- Single element level only — never nest (`__wrapper__button` is wrong)
- Modifier always goes with its base class (`button button--secondary`, never `button--secondary` alone)
- Multi-word parts separated with a single dash: `product-card__button-label`

## JS naming

- Variables and functions: `camelCase`
- Classes: `PascalCase`
- Data attributes: `data-kebab-case`
- Constants: `UPPER_SNAKE_CASE`
- Private class fields: `#camelCase`

## HTML ID naming

Use `CamelCase` with a section or block ID suffix — Shopify section/block IDs are the only guaranteed-unique values:

```html
<!-- Section-scoped -->
<section id="HeroBanner-{{ section.id }}">

<!-- Block-scoped -->
<div id="ProductCard-{{ block.id }}">

<!-- Element within a section -->
<dialog id="ProductModal-{{ product.id }}-{{ section.id }}">
<form id="ProductForm-{{ product.id }}-{{ section.id }}">
```

## Schema setting IDs

- `snake_case` always
- Descriptive: `heading_size` not `size`, `show_divider` not `divider`

## Folder structure

```
├── assets/          # Images, fonts — flat directory only
├── blocks/          # Reusable theme blocks
├── sections/        # Section Liquid files + group JSONs
├── snippets/        # Reusable partials (2+ section reuse threshold)
├── layout/          # theme.liquid (do not edit)
├── templates/       # JSON templates
├── locales/         # Translation strings
└── .claude/         # AI agent rules (this folder)
```
