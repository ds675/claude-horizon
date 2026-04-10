# JavaScript Patterns

## Core rules

- Vanilla JS only — no jQuery, no Alpine, no libraries
- ES6+ — `const`, `let`, arrow functions, template literals, private class fields
- Zero external dependencies — use native browser APIs
- `for (const item of items)` over `items.forEach()`
- Prefer `const` over `let` unless reassignment is genuinely needed
- Always use `async/await` — never `.then()` chaining
- Never attach event listeners to `document` or `window` unless unavoidable
- All JS lives in `{% javascript %}` tags inside the Liquid file — no separate `.js` files
- All values from schema are read via `data-` attributes — never embed Liquid in JS

## Component framework

Horizon provides a `Component` base class. Every interactive section or block extends it:

```javascript
import { Component } from '@theme/component';

/**
 * @typedef {Object} HeroBannerRefs
 * @property {HTMLElement} track - Slide track element
 * @property {HTMLButtonElement[]} dots - Navigation dot buttons
 */

/**
 * @extends {Component<HeroBannerRefs>}
 */
class HeroBanner extends Component {
  connectedCallback() {
    super.connectedCallback();
    if (!this.refs.track) return;
    this.init();
  }

  disconnectedCallback() {
    super.disconnectedCallback();
    clearInterval(this.#timer);
  }

  init() {
    this.#autoplay = this.dataset.autoplay === 'true';
    this.#speed = parseInt(this.dataset.speed, 10) || 5000;
    if (this.#autoplay) this.#startAutoplay();
  }

  #timer = null;
  #autoplay = false;
  #speed = 5000;

  #startAutoplay() {
    this.#timer = setInterval(() => this.next(), this.#speed);
  }

  next() {
    // implementation
  }
}

customElements.define('hero-banner', HeroBanner);
```

### Refs system

The `Component` base class scans for `ref="name"` attributes in the element's DOM and populates `this.refs`. Use `ref="items[]"` (with `[]`) for arrays:

```liquid
<hero-banner data-autoplay="true" data-speed="5000">
  <div ref="track" class="hero-banner__track">
    {% for block in section.blocks %}
      <div ref="slides[]" class="hero-banner__slide">...</div>
    {% endfor %}
  </div>
  <nav>
    {% for block in section.blocks %}
      <button ref="dots[]" on:click="/goTo/{{ forloop.index0 }}">{{ forloop.index }}</button>
    {% endfor %}
  </nav>
</hero-banner>
```

### Event binding

Use `on:event="/methodName"` attribute syntax — the Component framework wires these automatically. Pass arguments after the method name with `/`:

```liquid
<button on:click="/handleAddToCart">Add to cart</button>
<button on:click="/goTo/0">Slide 1</button>
```

For standard DOM events, use event delegation inside the component when binding in `on:` is not feasible:

```javascript
this.addEventListener('click', (e) => {
  const btn = e.target.closest('[data-action]');
  if (!btn) return;
  this.handleAction(btn.dataset.action);
});
```

## Reading schema values via `data-` attributes

Never embed Liquid inside `{% javascript %}`. Pass all values through `data-` attributes:

```liquid
<accordion-section
  data-open-first="{{ section.settings.open_first_item }}"
  data-allow-multiple="{{ section.settings.allow_multiple_open }}"
  data-speed="{{ section.settings.animation_speed | times: 1000 }}"
>
```

```javascript
connectedCallback() {
  super.connectedCallback();
  this.openFirst = this.dataset.openFirst === 'true';
  this.allowMultiple = this.dataset.allowMultiple === 'true';
}
```

## Null guarding

Use early returns, not nested conditionals:

```javascript
// Good
connectedCallback() {
  super.connectedCallback();
  if (!this.refs.track) return;
  if (!this.refs.slides?.length) return;
  this.init();
}

// Avoid
connectedCallback() {
  super.connectedCallback();
  if (this.refs.track) {
    if (this.refs.slides && this.refs.slides.length) {
      this.init();
    }
  }
}
```

## Async / await

Always use `async/await`. Always handle errors with `try/catch`. Use `AbortController` to cancel in-flight requests on disconnect:

```javascript
/** @type {AbortController|null} */
#controller = null;

async loadData(url) {
  this.#controller?.abort();
  this.#controller = new AbortController();

  try {
    const response = await fetch(url, { signal: this.#controller.signal });

    if (!response.ok) {
      throw new Error(`HTTP ${response.status}: ${response.statusText}`);
    }

    return await response.json();
  } catch (error) {
    if (error.name === 'AbortError') return null;
    console.error('Fetch error:', error);
    throw error;
  }
}

disconnectedCallback() {
  super.disconnectedCallback();
  this.#controller?.abort();
}
```

## JSDoc typing

Annotate every public method, complex parameter, and typedef:

```javascript
/**
 * @typedef {Object} ProductData
 * @property {string} id - Product identifier
 * @property {number} price - Price in cents
 * @property {boolean} [available] - Optional availability flag
 */

/**
 * Updates the price display
 * @param {ProductData} product
 * @param {HTMLElement} container
 * @returns {Promise<void>}
 */
const updateProductDisplay = async (product, container) => {
  if (!(container instanceof HTMLElement)) return;
  // implementation
};
```

## Private class fields

Use `#` for all internal state and methods — keeps the public API minimal:

```javascript
class AccordionSection extends Component {
  #openItems = new Set();

  #toggle(item) {
    if (this.#openItems.has(item)) {
      this.#close(item);
    } else {
      this.#open(item);
    }
  }

  #open(item) {
    item.hidden = false;
    this.#openItems.add(item);
  }

  #close(item) {
    item.hidden = true;
    this.#openItems.delete(item);
  }
}
```

## Custom events

Children emit custom events — parents never call children directly. Use `bubbles: true` and typed `detail`:

```javascript
/**
 * @typedef {Object} VariantSelectDetail
 * @property {string} variantId
 * @property {number} price
 * @property {boolean} available
 */

this.dispatchEvent(
  new CustomEvent('variant:select', {
    bubbles: true,
    detail: { variantId, price, available }
  })
);
```

Parents listen on the element or `document` for cross-component communication:

```javascript
document.addEventListener('cart:updated', this.#handleCartUpdate.bind(this));
```

## URL manipulation

Always use `URL` and `URLSearchParams` APIs — never string concatenation:

```javascript
const updateFilters = (filters) => {
  const url = new URL(window.location.href);

  for (const [key, value] of Object.entries(filters)) {
    if (value) {
      url.searchParams.set(key, value);
    } else {
      url.searchParams.delete(key);
    }
  }

  history.pushState({ urlParameters: url.searchParams.toString() }, '', url.toString());
};
```

## Debouncing

```javascript
/**
 * @param {Function} func
 * @param {number} wait - milliseconds
 * @returns {Function}
 */
const debounce = (func, wait) => {
  let timeout;
  return function (...args) {
    clearTimeout(timeout);
    timeout = setTimeout(() => func.apply(this, args), wait);
  };
};

// Usage in a component
#debouncedSearch = debounce(this.performSearch.bind(this), 300);
```

## Ternary and early return patterns

```javascript
// Ternary for simple binary state
const label = isLoading ? 'Loading...' : 'Add to cart';

// One-liner early returns
if (isOutOfStock) return;

// Return boolean directly
const isAvailable = product.available && product.price > 0;
return isAvailable;
```

## File placement

All section/block JS goes in `{% javascript %}` inside the Liquid file. The `Component` framework deduplicates it on the page. Never create a standalone `.js` file for a section or block.
