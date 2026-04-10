# Liquid Patterns

## Core rules
- Use `render` — never `include`
- Use `{% liquid %}` blocks for multi-line logic — keep HTML markup clean
- Always escape user-facing output: `{{ field | escape }}`
- Always guard blank content before rendering:
  ```liquid
  {% unless section.settings.heading == blank %}
    <h2>{{ section.settings.heading | escape }}</h2>
  {% endunless %}
  ```
- Never loop a collection without a limit or pagination

## CSS variable injection
Always bridge schema settings to CSS via a `<style>` block at the top of the section:
```liquid
<style>
  #shopify-section-{{ section.id }} {
    --section-bg: {{ section.settings.background_color }};
    --section-padding: {{ section.settings.padding_top }}px {{ section.settings.padding_bottom }}px;
    --heading-size: {{ section.settings.heading_size }}px;
  }
</style>
```

## Passing values to JS
Use `data-` attributes — never embed Liquid inside `<script>` tags:
```liquid
<div
  class="carousel"
  data-autoplay="{{ section.settings.autoplay }}"
  data-speed="{{ section.settings.speed }}"
  data-section-id="{{ section.id }}"
></div>
```

## Asset loading
```liquid
{{ 'section-hero-banner.css' | asset_url | stylesheet_tag }}
{{ 'section-hero-banner.js' | asset_url | script_tag }}
```

## Snippets
- Check `snippets/` before creating a new one
- Only extract to a snippet if the markup is reused in 2+ places
- Pass variables explicitly via `render`:
  ```liquid
  {% render 'product-card', product: product, show_vendor: true %}
  ```

## Section wrapper
Shopify wraps sections in `<div id="shopify-section-{{ section.id }}">` automatically.
Do not add it manually — use the ID for CSS scoping only.
