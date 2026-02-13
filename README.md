# @5mts/theme-utilities

Lightweight utility modules for WordPress themes.

## Installation

**JavaScript** (npm):

```bash
npm install github:5mts/theme-utilities
```

**PHP** (Composer):

Add the repository to your theme's `composer.json`:

```json
{
  "repositories": [
    {
      "type": "vcs",
      "url": "git@github.com:5mts/theme-utilities.git",
      "no-api": true
    }
  ]
}
```

Then require the package:

```bash
composer require 5mts/theme-utilities:dev-main
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

### SVG Logo Inliner (PHP)

Replaces the Site Logo block's `<img>` with inline SVG markup server-side. This enables full CSS control over the logo (fill color, hover effects, transitions) with no flash of unstyled content and no JavaScript required.

#### Usage

In your theme's `functions.php`:

```php
use FiveMTS\ThemeUtilities\SvgLogoInliner;
SvgLogoInliner::init();
```

That's it. When the Site Logo is an SVG, the block output will contain inline `<svg>` instead of `<img>`. If the logo is a PNG, JPG, or any other format, the block output is left unchanged.

#### What It Does

- Hooks into the `render_block_core/site-logo` filter
- Reads the SVG file directly from disk (no extra HTTP request)
- Copies `class`, `id`, and `style` attributes from the `<img>` to the `<svg>`
- Sets `aria-label` from the image's `alt` text and adds `role="img"` for accessibility
- Preserves the block's wrapping markup (`<div>`, `<a>`, etc.)

#### CSS Examples

```css
/* Change logo color */
.custom-logo {
  fill: currentColor;
  color: #333;
}

/* Logo color change on hover */
.custom-logo:hover {
  color: #0073aa;
}

/* Responsive logo sizing */
.custom-logo {
  width: 100%;
  height: auto;
}
```

## License

MIT
