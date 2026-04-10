# Task Workflow

## Before writing any code
1. Read the task requirements fully
2. Check `sections/CLAUDE.md` — can an existing section handle this?
3. Check `snippets/CLAUDE.md` — is the UI pattern already a snippet?
4. Check Horizon core files for reference patterns — do not copy, use as reference
5. If uncertain about a pattern, ask before writing

## Creating a new section — order of operations
1. `sections/[name].liquid` — structure, schema, asset references
2. `assets/section-[name].css` — styles scoped to section ID
3. `assets/section-[name].js` — only if interactivity is needed
4. Update `sections/CLAUDE.md` — add new section to the index

## Every section file must contain (in order)
1. Asset tags (CSS, JS)
2. `<style>` block — CSS variable injection from schema
3. Section HTML
4. `{% schema %}`

## Checklist before finishing any section
- [ ] CSS scoped to `#shopify-section-{{ section.id }}`
- [ ] All schema settings connected to CSS variables
- [ ] No hardcoded colours, spacing, or font values
- [ ] Blank content guarded in Liquid
- [ ] All user output escaped with `| escape`
- [ ] Schema has `presets` block
- [ ] Spacing settings included in schema
- [ ] JS reads values from `data-` attributes only
- [ ] Null checks in JS before DOM access
- [ ] Mobile-first CSS

## What NOT to do
- Do not touch `layout/theme.liquid`
- Do not modify Horizon core sections (carousel, header, footer, layered-slideshow, predictive-search)
- Do not introduce npm, webpack, or any build tooling
- Do not use Tailwind, Bootstrap, or utility CSS classes
- Do not use jQuery or any JS library
- Do not create a snippet for markup used in only one place
- Do not hardcode merchant-facing content — it must come from schema settings
- Do not write Liquid logic inside `<script>` tags
