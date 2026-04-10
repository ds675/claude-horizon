# JavaScript Patterns

## Core rules
- Vanilla JS only — no jQuery, no Alpine, no libraries
- ES6+ always — use `const`, `let`, arrow functions, template literals
- Never attach to `document` or `window` unless unavoidable
- Always guard against null before accessing DOM elements
- Use `defer` on script tags — never `document.write`

## Web component pattern
Follow Horizon's pattern — every interactive section is a custom element:
```js
class HeroBanner extends HTMLElement {
  connectedCallback() {
    this.banner = this.querySelector('.hero-banner');
    if (!this.banner) return;
    this.init();
  }

  init() {
    this.autoplay = this.dataset.autoplay === 'true';
    this.speed = parseInt(this.dataset.speed, 10) || 5000;
    if (this.autoplay) this.startAutoplay();
  }

  startAutoplay() {
    this.timer = setInterval(() => this.next(), this.speed);
  }

  disconnectedCallback() {
    clearInterval(this.timer);
  }
}

customElements.define('hero-banner', HeroBanner);
```

## Reading Liquid values
Always via `data-` attributes — never inline Liquid in `<script>`:
```liquid
<hero-banner
  data-autoplay="{{ section.settings.autoplay }}"
  data-speed="{{ section.settings.autoplay_speed | times: 1000 }}"
>
```

## Null guarding
```js
connectedCallback() {
  this.track = this.querySelector('.carousel__track');
  this.slides = this.querySelectorAll('.carousel__slide');
  if (!this.track || !this.slides.length) return;
  this.init();
}
```

## Event delegation
```js
this.addEventListener('click', (e) => {
  const btn = e.target.closest('[data-action]');
  if (!btn) return;
  this.handleAction(btn.dataset.action);
});
```

## File loading
```liquid
{{ 'section-hero-banner.js' | asset_url | script_tag }}
```
One JS file per section. Shared utilities go in `assets/global.js` only if used in 3+ sections.
