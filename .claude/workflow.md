# Task Workflow

## Before writing any code

1. Read the task requirements fully
2. Check `sections/CLAUDE.md` — can an existing section handle this?
3. Check `blocks/CLAUDE.md` — is this better as a reusable block?
4. Check `snippets/CLAUDE.md` — is the UI pattern already a snippet?
5. Check Horizon core files for reference patterns — use as reference, never copy/modify
6. If uncertain about a pattern, ask before writing

## Creating a new section — order of operations

1. `sections/[name].liquid` — structure, `{% stylesheet %}`, `{% javascript %}`, schema
2. Update `sections/CLAUDE.md` — add section to the index

## Creating a new block — order of operations

1. `blocks/[name].liquid` — `{% doc %}`, HTML, `{% stylesheet %}`, schema
2. Update `blocks/CLAUDE.md` — add block to the index

## Every section file must contain (in order)

1. `{% stylesheet %}` — component CSS
2. `{% javascript %}` — component JS (only if interactivity is needed)
3. CSS variable injection inline on the section element's `style` attribute
4. Section HTML
5. `{% schema %}`

## Every block file must contain (in order)

1. `{% doc %}` — LiquidDoc documentation
2. Block HTML with `{{ block.shopify_attributes }}` on root element
3. `{% stylesheet %}`
4. `{% schema %}`

## Checklist before finishing any section or block

- [ ] CSS scoped via section/block class — not by `#shopify-section-{{ section.id }}`
- [ ] All schema settings wired to CSS variables, injected inline on the element
- [ ] No hardcoded colours, spacing, or font sizes
- [ ] Blank content guarded in Liquid
- [ ] All user text output escaped with `| escape`
- [ ] All user-facing strings use `{{ 'key' | t }}` — no hardcoded English
- [ ] Schema names and labels use translation keys
- [ ] Schema has `presets` block
- [ ] Spacing settings included in schema
- [ ] JS reads values from `data-` attributes only (no Liquid in `{% javascript %}`)
- [ ] Null checks before all DOM access in JS
- [ ] Mobile-first CSS
- [ ] Accessibility: correct ARIA roles/states, keyboard support, `:focus-visible`
- [ ] `sections/CLAUDE.md` or `blocks/CLAUDE.md` updated

## What NOT to do

- Do not touch `layout/theme.liquid`
- Do not modify Horizon core sections (carousel, header, footer, layered-slideshow, predictive-search)
- Do not create separate `.css` or `.js` files for sections/blocks — use `{% stylesheet %}` / `{% javascript %}`
- Do not introduce npm, webpack, or any build tooling
- Do not use Tailwind, Bootstrap, or utility CSS classes
- Do not use jQuery or any JS library
- Do not create a snippet for markup used in only one place
- Do not hardcode merchant-facing content — it must come from schema settings
- Do not write Liquid logic inside `{% javascript %}` — use `data-` attributes
- Do not remove or rename existing schema setting IDs — it breaks merchant data
- Do not hardcode strings — use `{{ 'key' | t }}` for all user-facing text
