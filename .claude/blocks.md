# Theme Blocks

## What are theme blocks

Theme blocks live in `blocks/` and are reusable, nestable components that can be:
- Added by merchants in the theme editor
- Placed statically by developers in templates
- Nested inside other blocks using `{% content_for 'blocks' %}`

Check `blocks/CLAUDE.md` before creating a new one.

## Basic block structure

Every block file follows this exact structure:

```liquid
{% doc %}
  Text Block

  Renders styled text with alignment control.

  @example
  {% content_for 'block', type: 'text', id: 'text-1' %}
{% enddoc %}

<div
  class="text text--{{ block.settings.text_style }}"
  style="--text-align: {{ block.settings.alignment }};"
  {{ block.shopify_attributes }}
>
  {{ block.settings.text }}
</div>

{% stylesheet %}
  .text {
    text-align: var(--text-align);
  }
{% endstylesheet %}

{% schema %}
{
  "name": "t:names.text",
  "settings": [
    {
      "type": "text",
      "id": "text",
      "label": "t:labels.text",
      "default": "Text"
    }
  ],
  "presets": [{ "name": "t:names.text" }]
}
{% endschema %}
```

**Required on every block:**
- `{% doc %}` block documenting purpose and `@example`
- `{{ block.shopify_attributes }}` on the root element (required for theme editor)
- CSS variables injected inline on the element's `style` attribute
- `{% stylesheet %}` (not a separate `.css` file)
- `{% schema %}` with `name` and `presets`

## Static blocks

Static blocks are placed by developers directly in templates/sections — merchants can configure them but not remove or reorder them:

```liquid
<!-- Basic static block -->
{% content_for 'block', type: 'breadcrumb', id: 'product-breadcrumb' %}

<!-- With default settings -->
{%
  content_for 'block',
    type: 'product-gallery',
    id: 'main-gallery',
    settings: {
      enable_zoom: true,
      thumbnails_position: 'bottom'
    }
%}
```

## Dynamic block areas

Allow merchants to add any theme block at a location:

```liquid
<div class="product-extra-content">
  {% content_for 'blocks' %}
</div>
```

Accept only specific block types:

```json
"blocks": [
  { "type": "@theme" },
  { "type": "@app" }
]
```

## Critical: single `content_for 'blocks'` per file

There can only be **one** `{% content_for 'blocks' %}` call per Liquid file. If you need to output blocks in multiple conditional branches, capture first:

```liquid
<!-- Good: capture once, output multiple times -->
{% capture blocks_content %}
  {% content_for 'blocks' %}
{% endcapture %}

{% if condition %}
  <div class="layout-a">{{ blocks_content }}</div>
{% else %}
  <div class="layout-b">{{ blocks_content }}</div>
{% endif %}

<!-- Bad: will throw "Duplicate entries for 'content_for blocks'" -->
{% if condition %}
  {% content_for 'blocks' %}
{% else %}
  {% content_for 'blocks' %}
{% endif %}
```

## Nested blocks (container block)

```liquid
{% doc %}
  Group Block

  A layout container that renders its child blocks in a row or column.

  @example
  {% content_for 'block', type: 'group', id: 'column-1' %}
{% enddoc %}

<div
  class="group group--{{ block.settings.layout_direction }}"
  style="
    --group-padding: {{ block.settings.padding }}px;
    --group-alignment: {{ block.settings.alignment }};
  "
  {{ block.shopify_attributes }}
>
  {% content_for 'blocks' %}
</div>
```

## Accessing block data

```liquid
{{ block.id }}                                  {%- # Unique ID -%}
{{ block.type }}                                {%- # Block type name -%}
{{ block.settings.heading | escape }}           {%- # Setting value -%}
{{ block.settings.image | image_url: width: 800 | image_tag }}
{{ block.shopify_attributes }}                  {%- # Required for editor -%}
```

## Schema for blocks targeting

```json
{
  "blocks": [
    { "type": "@theme" },
    { "type": "@app" }
  ]
}
```

Restrict to specific types when needed:

```json
{
  "blocks": [
    { "type": "text", "name": "t:names.text" },
    { "type": "image", "name": "t:names.image" }
  ]
}
```

## Presets with nested blocks

```json
{
  "presets": [
    {
      "name": "t:names.two_column",
      "category": "Layout",
      "settings": { "layout_direction": "horizontal" },
      "blocks": [
        { "type": "text", "settings": { "text": "Column 1" } },
        { "type": "text", "settings": { "text": "Column 2" } }
      ]
    }
  ]
}
```

When blocks in a preset are declared as an object (not an array), include `block_order`:

```json
{
  "blocks": {
    "title": { "type": "product-title" },
    "price": { "type": "price" }
  },
  "block_order": ["title", "price"]
}
```

## After creating a new block

Update `blocks/CLAUDE.md` — add the block to the index table.
