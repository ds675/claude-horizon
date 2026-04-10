# Sections

## Existing sections — check before creating new

| File | Purpose | Blocks? |
|------|---------|---------|
| `custom-announcement-bar.liquid` | Two-column announcement bar (left text + desktop-only right text/link) | No |
| `carousel.liquid` | General purpose carousel | Yes — slides |
| `layered-slideshow.liquid` | Hero-style layered slideshow | Yes — slides |
| `header.liquid` | Site header | No |
| `footer.liquid` | Site footer | Yes |
| `footer-group.json` | Footer section group | — |
| `predictive-search.liquid` | Search UI overlay | No |

## Rules
- Scope all CSS to `#shopify-section-{{ section.id }}`
- Always pair with `assets/section-[name].css`
- Add JS file only if section has interactivity
- Add every new section to this table after creating it

## Extending existing sections
- Never edit the files above directly
- Add new settings via schema extension — see `.claude/schema.md`
