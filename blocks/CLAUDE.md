# Blocks

## Existing blocks — check before creating new

| File | Purpose | Nestable? |
|------|---------|-----------|
| *(add yours here as you build)* | | |

## Rules

- Every block must have a `{% doc %}` block documenting purpose and accepted parameters
- `{{ block.shopify_attributes }}` is required on the root element of every block
- CSS variables injected inline on the element's `style` attribute — not in a `<style>` block
- Styles in `{% stylesheet %}` — never a separate `.css` file
- Add every new block to the table above after creating it

## Adding a new block

1. Create `blocks/[name].liquid`
2. Follow the structure in `.claude/blocks.md`
3. Add a row to the table above with file name, purpose, and whether it accepts nested blocks
