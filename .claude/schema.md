# Schema Conventions

## Core rules
- Always study the closest existing Horizon section schema before writing a new one
- Reference sections: `carousel.liquid`, `layered-slideshow.liquid`
- Setting IDs must be snake_case and self-describing
- Every new section must have a `presets` block
- Group related settings under `header` type dividers

## Standard settings every section includes

### Spacing group (required on every section)
```json
{
  "type": "header",
  "content": "Spacing"
},
{
  "type": "range",
  "id": "padding_top",
  "label": "Padding top",
  "min": 0, "max": 100, "step": 4, "unit": "px",
  "default": 40
},
{
  "type": "range",
  "id": "padding_bottom",
  "label": "Padding bottom",
  "min": 0, "max": 100, "step": 4, "unit": "px",
  "default": 40
}
```

### Color group
```json
{
  "type": "header",
  "content": "Colors"
},
{
  "type": "color",
  "id": "background_color",
  "label": "Background color",
  "default": "#ffffff"
},
{
  "type": "color",
  "id": "text_color",
  "label": "Text color",
  "default": "#000000"
}
```

## Presets block (required)
```json
"presets": [
  {
    "name": "Section name"
  }
]
```

## Blocks pattern
```json
"blocks": [
  {
    "type": "slide",
    "name": "Slide",
    "limit": 6,
    "settings": [
      {
        "type": "image_picker",
        "id": "image",
        "label": "Image"
      },
      {
        "type": "text",
        "id": "heading",
        "label": "Heading"
      }
    ]
  }
]
```

## Extending Horizon schema
1. Copy the original schema as a comment reference
2. Add new settings in a clearly labelled group
3. Never remove or rename existing setting IDs — it breaks merchant data
