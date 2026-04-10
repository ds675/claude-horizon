# Conventions

## File naming
| Type | Pattern | Example |
|------|---------|---------|
| Section | `sections/[name].liquid` | `sections/hero-banner.liquid` |
| Section CSS | `assets/section-[name].css` | `assets/section-hero-banner.css` |
| Section JS | `assets/section-[name].js` | `assets/section-hero-banner.js` |
| Snippet | `snippets/[name].liquid` | `snippets/product-card.liquid` |
| Component CSS | `assets/component-[name].css` | `assets/component-button.css` |
| Section group | `sections/[name]-group.json` | `sections/footer-group.json` |

- Always kebab-case
- Section name must be consistent across all three files (liquid, css, js)

## CSS naming
BEM strictly:
```
block__element--modifier
hero-banner__heading--large
product-card__image--placeholder
```

## JS naming
- Variables and functions: camelCase
- Classes: PascalCase
- Data attributes: `data-kebab-case`
- Constants: UPPER_SNAKE_CASE

## Schema setting IDs
- snake_case always
- Descriptive: `heading_size` not `size`, `show_divider` not `divider`

## Folder structure
```
├── assets/          # CSS, JS, images, fonts
├── sections/        # Section Liquid files + group JSONs
├── snippets/        # Reusable partials
├── layout/          # theme.liquid (do not edit)
├── templates/       # JSON templates
├── locales/         # Translation strings
└── .claude/         # Claude Code rules (this folder)
```
