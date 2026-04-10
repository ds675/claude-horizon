# Schema Conventions

## Core rules

- Study the closest existing Horizon section schema before writing a new one
- Reference sections: `carousel.liquid`, `layered-slideshow.liquid`
- Setting IDs must be `snake_case` and self-describing
- Every section must have a `presets` block
- Group settings with `header` type dividers
- Schema names and labels must use translation keys — never hardcode English strings

## Translation keys

All `name`, `label`, `content`, and `default` string values in schemas must reference keys from `locales/en.default.schema.json`:

```json
{
  "name": "t:names.accordion",
  "settings": [
    {
      "type": "text",
      "id": "heading",
      "label": "t:labels.heading"
    },
    {
      "type": "header",
      "content": "t:headers.spacing"
    }
  ],
  "presets": [
    { "name": "t:names.accordion" }
  ]
}
```

If a key doesn't exist in `locales/en.default.schema.json`, add it.

## Label guidelines

- Keep labels under 30 characters
- The setting type provides context — write "Columns" not "Number of columns"
- No verb phrases on checkboxes — "Show vendor" not "Enable vendor display"
- Use title case: "Show Vendor" not "show vendor"

## Setting organization order

**1. Resource pickers first** (required for section functionality)

```json
{ "type": "collection", "id": "collection", "label": "t:labels.collection" },
{ "type": "product", "id": "featured_product", "label": "t:labels.product" }
```

**2. Visual impact order**

```json
{ "type": "header", "content": "t:headers.layout" },
{ "type": "range", "id": "columns_desktop", ... },

{ "type": "header", "content": "t:headers.content" },
{ "type": "text", "id": "heading", ... },

{ "type": "header", "content": "t:headers.colors" },
{ "type": "color", "id": "background_color", ... },

{ "type": "header", "content": "t:headers.spacing" },
{ "type": "range", "id": "padding_top", ... },
{ "type": "range", "id": "padding_bottom", ... }
```

## Standard spacing group (required on every section)

```json
{
  "type": "header",
  "content": "t:headers.spacing"
},
{
  "type": "range",
  "id": "padding_top",
  "label": "t:labels.padding_top",
  "min": 0, "max": 100, "step": 4, "unit": "px",
  "default": 40
},
{
  "type": "range",
  "id": "padding_bottom",
  "label": "t:labels.padding_bottom",
  "min": 0, "max": 100, "step": 4, "unit": "px",
  "default": 40
}
```

## Standard color group

```json
{
  "type": "header",
  "content": "t:headers.colors"
},
{
  "type": "color",
  "id": "background_color",
  "label": "t:labels.background_color",
  "default": "#ffffff"
},
{
  "type": "color",
  "id": "text_color",
  "label": "t:labels.text_color",
  "default": "#000000"
}
```

## Conditional settings — `visible_if`

Use `visible_if` to hide settings that only apply when a parent setting has a certain value:

```json
{
  "visible_if": "{{ block.settings.layout_direction == 'group--vertical' }}",
  "type": "select",
  "id": "alignment",
  "label": "t:labels.alignment",
  "default": "flex-start",
  "options": [
    { "value": "flex-start", "label": "t:options.alignment.left" },
    { "value": "center", "label": "t:options.alignment.center" },
    { "value": "flex-end", "label": "t:options.alignment.right" }
  ]
}
```

## Presets block (required)

```json
"presets": [
  {
    "name": "t:names.section_name"
  }
]
```

## Blocks pattern

```json
"blocks": [
  {
    "type": "@theme",
    "name": "Theme blocks"
  },
  {
    "type": "@app"
  }
]
```

For section-specific block types:

```json
"blocks": [
  {
    "type": "slide",
    "name": "t:names.slide",
    "limit": 6,
    "settings": [
      { "type": "image_picker", "id": "image", "label": "t:labels.image" },
      { "type": "text", "id": "heading", "label": "t:labels.heading" }
    ]
  }
]
```

## Extending Horizon schema

1. Never remove or rename existing setting IDs — it breaks merchant-saved data
2. Add new settings in a clearly labelled new group
3. Add new translation keys to `locales/en.default.schema.json` before using them

## Valid setting types

Input: `text`, `textarea`, `number`, `range`, `color`, `checkbox`, `select`, `radio`, `collection`, `product`, `blog`, `page`, `image_picker`, `font_picker`, `video`, `richtext`, `url`, `html`
Sidebar: `header`, `paragraph`
