# Horizon Theme

Shopify theme built on [Horizon](https://github.com/Shopify/horizon).
Custom sections, blocks, and snippets extend Horizon — core files are never modified.

## Stack

- Liquid, Custom CSS, Vanilla JS (web components)
- No Tailwind, no Bootstrap, no JS frameworks
- No build step — `{% stylesheet %}` and `{% javascript %}` tags are used in-file
- All user-facing text uses Shopify's `| t` translation filter

## Rules index

- `.claude/conventions.md` — naming, file structure, ID conventions
- `.claude/liquid.md` — Liquid patterns, `{% doc %}`, `{% stylesheet %}`, `{% javascript %}`
- `.claude/css.md` — CSS architecture, BEM, variables, modern CSS
- `.claude/javascript.md` — JS patterns, Component framework, async, JSDoc
- `.claude/schema.md` — schema conventions, translation keys, setting organization
- `.claude/html.md` — semantic HTML, native elements, progressive enhancement
- `.claude/accessibility.md` — WCAG standards, global and per-component patterns
- `.claude/localization.md` — translation requirements, `| t` filter, locale files
- `.claude/blocks.md` — theme block development, static blocks, nested blocks
- `.claude/workflow.md` — how to approach every task

## Non-negotiables

- Never modify Horizon core files
- Always check existing sections, blocks, and snippets before creating new ones
- CSS variables bridge schema settings to styles — inject inline on the element, not in a `<style>` block
- All user-facing text must use `{{ 'key' | t }}` — never hardcode strings
- Accessibility is not optional — follow WCAG AA for every component
