# @5mts/theme-utilities

Lightweight utility modules for WordPress themes.

## Installation

```bash
npm install github:5mts/theme-utilities
```

## Modules

### Scroll Detector

Adds scroll-related state to the `<html>` element via data attributes or classes. Useful for styling sticky headers, back-to-top buttons, and scroll-aware UI.

#### Basic Usage

```js
import { initScrollDetector } from '@5mts/theme-utilities';

initScrollDetector();
```

This adds the following data attributes to `<html>`:

| Attribute | Values | Description |
|-----------|--------|-------------|
| `data-scroll-dir` | `up` / `down` | Current scroll direction |
| `data-at-top` | `1` / `0` | Within 50px of page top |
| `data-at-bottom` | `1` / `0` | Within 50px of page bottom |
| `data-near-top` | `1` / `0` | Within 50% of viewport height from top |
| `data-near-bottom` | `1` / `0` | Within 50% of viewport height from bottom |

#### CSS Examples

```css
/* Hide header on scroll down, show on scroll up */
.site-header {
  transition: transform 0.3s ease;
}
[data-scroll-dir="down"] .site-header {
  transform: translateY(-100%);
}

/* Transparent header at top of page */
[data-at-top="1"] .site-header {
  background: transparent;
}

/* Show back-to-top button when not near top */
.back-to-top {
  opacity: 0;
  transition: opacity 0.3s;
}
[data-near-top="0"] .back-to-top {
  opacity: 1;
}
```

#### Custom Configuration

```js
import { initScrollDetector } from '@5mts/theme-utilities';

const cleanup = initScrollDetector({
  // Thresholds
  hideAfterPx: 120,       // scroll distance before hiding header (default: 120)
  upIntentPx: 18,         // upward travel needed to reveal (default: 18)
  minDeltaPx: 2,          // ignore movements smaller than this (default: 2)
  edgeThresholdPx: 50,    // distance for at-top/at-bottom (default: 50)
  zoneThreshold: 0.5,     // viewport fraction for near-top/near-bottom (default: 0.5)

  // Features - set to false to disable
  scrollDir: true,
  atTop: true,
  atBottom: true,
  nearTop: true,
  nearBottom: false,  // disable this one

  // Output mode
  mode: 'data',  // 'data' for data-attributes, 'class' for classes

  // Target element (default: document.documentElement)
  target: document.documentElement,
});

// Later, to remove listener and clean up attributes:
cleanup();
```

#### Class Mode

Use `mode: 'class'` to add classes instead of data attributes:

```js
initScrollDetector({ mode: 'class' });
```

This adds classes like:
- `scroll-dir-up` / `scroll-dir-down`
- `at-top` / `at-bottom` (present when true)
- `near-top` / `near-bottom` (present when true)

```css
/* Class-based styling */
.scroll-dir-down .site-header {
  transform: translateY(-100%);
}

.at-top .site-header {
  background: transparent;
}
```

## License

MIT
