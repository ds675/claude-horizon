# Snippets

## Existing snippets — check before creating new

| File | Purpose | Parameters |
|------|---------|------------|
| *(add yours here as you build)* | | |

## Rules
- Only create a snippet if markup is reused across 2+ sections
- Always pass variables explicitly via `render`:
  ```liquid
  {% render 'product-card', product: product, show_vendor: section.settings.show_vendor %}
  ```
- Document every parameter a snippet accepts — add to the table above
- No asset tags inside snippets — load assets in the parent section

## Adding a new snippet
1. Create `snippets/[name].liquid`
2. Add a row to the table above with file name, purpose, and accepted parameters
