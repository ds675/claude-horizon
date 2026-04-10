# Localization Standards

## Core rule

**Every user-facing string must use the translation filter** — never hardcode English text in Liquid templates.

```liquid
<!-- Good -->
<h2>{{ 'sections.featured_collection.heading' | t }}</h2>
<button>{{ 'products.add_to_cart' | t }}</button>

<!-- Bad — hardcoded strings break localization -->
<h2>Featured Collection</h2>
<button>Add to cart</button>
```

## Translation filter

```liquid
{{ 'key' | t }}
{{ 'key' | t: variable: value }}
```

### With interpolated variables

```liquid
<!-- Template -->
<p>{{ 'products.price_range' | t: min: product.price_min | money, max: product.price_max | money }}</p>
<p>{{ 'general.pagination.page' | t: page: paginate.current_page, pages: paginate.pages }}</p>
```

```json
{
  "products": {
    "price_range": "From {{ min }} to {{ max }}"
  },
  "general": {
    "pagination": {
      "page": "Page {{ page }} of {{ pages }}"
    }
  }
}
```

## Locale file structure

```
locales/
├── en.default.json             # English content (required — source of truth)
├── en.default.schema.json      # Schema labels and names (required)
├── es.json                     # Other languages — add as needed
└── fr.json
```

### `en.default.json` key structure

Use a maximum of three levels, `snake_case` throughout:

```json
{
  "general": {
    "accessibility": {
      "skip_to_content": "Skip to content",
      "close": "Close",
      "new_tab": "opens in new tab"
    },
    "search_placeholder": "Search"
  },
  "products": {
    "add_to_cart": "Add to cart",
    "sold_out": "Sold out",
    "unavailable": "Unavailable",
    "quick_view": "Quick view"
  },
  "cart": {
    "title": "Cart",
    "empty": "Your cart is empty",
    "close": "Close cart"
  },
  "sections": {
    "featured_collection": {
      "heading": "Featured collection",
      "view_all": "View all"
    }
  }
}
```

### `en.default.schema.json` key structure

Used for schema `name`, `label`, `content`, and option `label` values:

```json
{
  "names": {
    "accordion": "Accordion",
    "hero_banner": "Hero Banner",
    "slide": "Slide"
  },
  "labels": {
    "heading": "Heading",
    "background_color": "Background color",
    "padding_top": "Padding top",
    "padding_bottom": "Padding bottom"
  },
  "headers": {
    "layout": "Layout",
    "colors": "Colors",
    "spacing": "Spacing",
    "content": "Content"
  },
  "options": {
    "alignment": {
      "left": "Left",
      "center": "Center",
      "right": "Right"
    }
  }
}
```

## Adding new keys

1. Add the English string to `en.default.json` (or `en.default.schema.json` for schema settings)
2. Use the key immediately in Liquid — do not wait for translations
3. Only add English — translators handle other languages
4. Never add keys to other locale files yourself

## Key naming rules

- `snake_case` always
- Descriptive and hierarchical: `products.add_to_cart` not `btn_text`
- Group by feature/section: `sections.[section_name].[key]`
- Max 3 levels deep
- Consistent terminology across the theme — use the same key pattern for the same concept

## Escaping translated output

Translated strings are not HTML-escaped by default. Escape when the value contains user-controlled content or when embedding in an attribute:

```liquid
<!-- Safe: translator controls the text -->
{{ 'products.add_to_cart' | t }}

<!-- Escape when the key uses a variable from user/merchant data -->
{{ 'products.product_name' | t: name: product.title | escape }}
```
