# Liquid Patterns

## Core rules

- Use `render` — never `include`
- Use `{% liquid %}` blocks for multi-line logic — keep markup clean
- Always escape user-facing text output: `{{ field | escape }}`
- All user-facing strings must use the translation filter: `{{ 'key' | t }}`
- Guard blank content before rendering — never render empty elements
- Never loop a collection without a `limit:` or `paginate`

## Settings shorthand

When a section or block has multiple settings references, always assign the settings object to a short variable at the top of the file. Then assign individual settings from it. This reduces repetition and keeps the markup clean.

```liquid
{% liquid
  assign s = section.settings
  assign color_scheme = s.color_scheme
  assign heading = s.heading
  assign padding_top = s.padding_top
%}
```

For blocks, use `b = block.settings`:

```liquid
{% liquid
  assign b = block.settings
  assign heading = b.heading
  assign content = b.content
%}
```

Only skip the shorthand if a section/block uses a single setting in one place.

## Inline variables

Prefer inlining Liquid directly in HTML attributes rather than declaring intermediate variables. Only assign a variable if the same complex expression is used in 2+ places or the logic would otherwise harm readability.

```liquid
<!-- Good: inline -->
<div
  class="component component--{{ section.settings.style }}"
  style="color: {{ section.settings.text_color }};"
>
  {{ section.settings.heading | escape }}
</div>

<!-- Avoid: unnecessary assignment -->
{% liquid
  assign component_class = 'component component--' | append: section.settings.style
  assign text_color = section.settings.text_color
%}
<div class="{{ component_class }}" style="color: {{ text_color }};">
  {{ section.settings.heading | escape }}
</div>
```

## CSS variable injection

Inject schema settings as CSS custom properties on the element's `style` attribute — not in a separate `<style>` block. This keeps CSS in `{% stylesheet %}` and avoids per-ID selector bloat.

```liquid
<!-- Good: inline on element -->
<section
  class="hero-banner"
  style="
    --hero-bg: {{ section.settings.background_color }};
    --hero-min-height: {{ section.settings.min_height }}px;
    --hero-padding: {{ section.settings.padding_top }}px {{ section.settings.padding_bottom }}px;
  "
>

<!-- Used in {% stylesheet %} -->
.hero-banner {
  background-color: var(--hero-bg);
  min-height: var(--hero-min-height);
  padding: var(--hero-padding);
}
```

## Passing values to JS

Use `data-` attributes — never embed Liquid inside `{% javascript %}` or `<script>` tags:

```liquid
<hero-banner
  data-autoplay="{{ section.settings.autoplay }}"
  data-speed="{{ section.settings.autoplay_speed | times: 1000 }}"
  data-section-id="{{ section.id }}"
>
```

## `{% stylesheet %}` tag

Write all section/block CSS inside `{% stylesheet %}` — it is deduplicated by Shopify on the same page. Never create separate `.css` files for sections or blocks.

```liquid
{% stylesheet %}
  .hero-banner {
    background-color: var(--hero-bg);
  }

  .hero-banner__heading {
    font-size: var(--hero-heading-size);
  }
{% endstylesheet %}
```

## `{% javascript %}` tag

Write all section/block JS inside `{% javascript %}` — it is deduplicated by Shopify. Never create separate `.js` files for sections or blocks.

```liquid
{% javascript %}
  import { Component } from '@theme/component';

  class HeroBanner extends Component {
    connectedCallback() {
      super.connectedCallback();
      this.init();
    }

    init() {
      this.autoplay = this.dataset.autoplay === 'true';
    }
  }

  customElements.define('hero-banner', HeroBanner);
{% endjavascript %}
```

## `{% doc %}` — snippet documentation

Every snippet must include a `{% doc %}` block immediately before the Liquid logic. Parameter types: `{object}`, `{string}`, `{number}`, `{boolean}`. Use brackets for optional params: `[param]`.

```liquid
{% doc %}
  Product Card

  Renders a product card with image, title, price, and optional vendor.

  @param {object} product - Product object (required)
  @param {boolean} [show_vendor] - Display vendor name (default: false)
  @param {string} [image_ratio] - Image aspect ratio: 'adapt', 'square', 'portrait' (default: 'adapt')
  @param {string} [card_class] - Additional CSS classes

  @example
  {% render 'product-card', product: product, show_vendor: true, image_ratio: 'square' %}
{% enddoc %}
```

## Snippet parameter validation

Always set defaults and guard required params at the top of every snippet:

```liquid
{% liquid
  assign product = product | default: empty
  assign show_vendor = show_vendor | default: false
  assign image_ratio = image_ratio | default: 'adapt'

  unless product != empty
    echo '<!-- Error: product-card requires a product parameter -->'
    break
  endunless
%}
```

## Blank content guards

Always guard before rendering:

```liquid
{% unless section.settings.heading == blank %}
  <h2 class="hero-banner__heading">{{ section.settings.heading | escape }}</h2>
{% endunless %}
```

## Snippets

- Check `snippets/CLAUDE.md` before creating a new one
- Only extract to a snippet if the markup is reused in 2+ places
- Pass variables explicitly via `render` — snippets cannot access parent scope
- No `{% stylesheet %}` or `{% javascript %}` tags inside snippets — load those in the parent

```liquid
{% render 'product-card', product: product, show_vendor: section.settings.show_vendor %}
```

## Section wrapper

Shopify automatically wraps sections in `<div id="shopify-section-{{ section.id }}">`.
Do not add it manually. Scope CSS to the section class, not the wrapper ID.

## Valid Liquid reference

**Control flow:** `if/unless/case`, `for ... limit: N`, `paginate`
**Assignment:** `assign`, `capture`, `increment`, `decrement`
**Blocks:** `liquid`, `comment`, `raw`
**Output filters:** `escape`, `truncate`, `handleize`, `replace`, `split`, `upcase`, `downcase`, `capitalize`
**Money:** `money`, `money_with_currency`
**Media:** `image_url`, `image_tag`, `asset_url`, `inline_asset_content`
**Translation:** `t`

Never invent filters, tags, or objects that do not exist in Shopify Liquid.
