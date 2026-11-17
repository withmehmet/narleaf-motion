# Narleaf Motion

A CSS-only, scroll-triggered animation system. No JavaScript, no dependencies — a single CSS file and a handful of `data-*` attributes.

Built on the browser's native [scroll-driven animations](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_scroll-driven_animations) API. In browsers without support, content simply renders fully visible (progressive enhancement).

## Philosophy

**Sensible defaults, optional customization.** Make it work first, customize later.

```html
<div data-nar-motion="fade"></div>
```

That's all it takes. No additional attributes are required.

## Installation

Include `narleaf-motion.css` in your project:

```html
<link rel="stylesheet" href="narleaf-motion.css">
```

Or copy it directly into your project's CSS file.

## Usage

Add the `data-nar-motion` attribute to any element you want to animate:

```html
<div data-nar-motion="fade">...</div>
<div data-nar-motion="scale-up">...</div>
<div data-nar-motion="slide-up">...</div>
<div data-nar-motion="slide-down">...</div>
```

The animation starts automatically as the element scrolls into view — no JavaScript setup required.

## Animation types

| Value | Effect |
|---|---|
| `fade` | Opacity transitions from 0 to 1 |
| `scale-up` | Grows from a smaller scale up to full size |
| `slide-up` | Slides in from below |
| `slide-down` | Slides in from above |

An element can only be assigned one `data-nar-motion` value (a combination like `"fade slide-up"` is not supported). To combine multiple effects on the same element, wrap it and apply the two attributes separately.

## Naming architecture

Attributes are grouped into three namespaces so it's clear from the name alone which animation type each one applies to:

| Prefix | Scope |
|---|---|
| `data-nar-motion-*` | Applies to all animation types — timing/system settings |
| `data-nar-scale-*` | `scale-up` only |
| `data-nar-slide-*` | `slide-up` / `slide-down` only |

## Customization

All customization attributes are optional. Defaults are used when omitted.

### API summary

| Attribute | Scope | Default | Supported values |
|---|---|---|---|
| `data-nar-motion` | All | — (required) | `fade`, `scale-up`, `slide-up`, `slide-down` |
| `data-nar-motion-start` | All | `10` | 0, 5, 10 ... 100 (multiples of 5) |
| `data-nar-motion-end` | All | `50` | 0, 5, 10 ... 100 (multiples of 5) |
| `data-nar-scale-start` | `scale-up` only | `70` | 90, 80, 70, 60, 50 |
| `data-nar-slide-distance` | `slide-up` / `slide-down` only | `50` | 30, 40, 50 ... 150 (multiples of 10) |
| `data-nar-motion-will-change` | All | None (opt-in) | Takes no value; attribute is either present or absent |

Detailed explanations follow below.

### `data-nar-motion-start` and `data-nar-motion-end`

Think of the element's total time on screen (from when it starts entering the viewport to when it has fully exited) as a 0–100 scale. These two attributes define where on that scale the animation begins and ends.

```
0%                                                          100%
|——————————————— element's time on screen —————————————————|
element starts        [motion-start]      [motion-end]   element
entering               animation           animation      fully
                        starts              ends           exits
                                            (stays static from here)
```

```html
<div data-nar-motion="fade" data-nar-motion-start="20" data-nar-motion-end="60">...</div>
```

- **Default:** `motion-start` = 10, `motion-end` = 50
- **Supported values:** 0, 5, 10 ... 95, 100 (multiples of 5 only)
- After `motion-end`, the animation stays in its completed state and won't change again until the element exits the viewport.

**Technical note:** these percentages are not a fixed pixel distance — they're a proportion of that specific element's own `cover` view timeline progress (`animation-range-start/end: cover <percentage>`). As the element's height or the viewport size changes, the actual scroll distance behind the same percentage values changes too — so `start=10, end=50` corresponds to a different physical scroll distance on a short card than on a tall section. Using the same start/end values across differently-sized elements on the same page doesn't guarantee an identical visual "speed."

> ⚠️ **Rule:** `motion-start` should always be less than or equal to `motion-end` (`start ≤ end`). This rule is not enforced or validated in CSS — that's a deliberate choice to keep the system simple. If `start > end` (e.g. `start="70"`, `end="30"`), the result is undefined/unpredictable — this case isn't clearly defined in the CSS specification, so browsers may handle it differently (the animation may not progress at all, or may jump abruptly). Setting `start` and `end` equal (e.g. `start="100"`, `end="100"`) also produces a zero-width range — the animation appears to complete instantly with no smooth transition.

### `data-nar-scale-start`

Sets the **starting size of the `scale-up` animation, directly as a percentage**. Has no effect on `fade`.

```html
<div data-nar-motion="scale-up" data-nar-scale-start="50">...</div>
```

| Value | Starting size |
|---|---|
| `90` | 90% |
| `80` | 80% |
| `70` | 70% (default) |
| `60` | 60% |
| `50` | 50% |

The value is the result you see: `data-nar-scale-start="50"` means the element starts at 50% size and grows to full size. There's no extra inversion math involved.

- **Default:** `70`
- **Supported values:** 90, 80, 70, 60, 50

### `data-nar-slide-distance`

Sets the slide distance, in pixels, for the `slide-up` and `slide-down` animations. Has no effect on other animation types.

```html
<div data-nar-motion="slide-up" data-nar-slide-distance="100">...</div>
```

- **Default:** `50` (50px)
- **Supported values:** 30, 40, 50 ... 150 (multiples of 10)

### `data-nar-motion-will-change`

Applies `will-change: opacity, scale, translate`. Left as opt-in for performance reasons — overusing it can increase memory consumption, so it's recommended only on elements that genuinely need it.

```html
<div data-nar-motion="fade" data-nar-motion-will-change>...</div>
```

### Conflicts with other CSS

The system animates using the standalone `translate` and `scale` CSS properties — as opposed to the older `transform: translateX(...) scale(...)` syntax. If you define your own `translate` or `scale` property on the same element (for a hover effect, say), it may conflict with or override Narleaf Motion's animation.

**Solution:** apply the animation to a wrapper, separate from the inner element that uses its own `translate`/`scale`:

```html
<!-- Not recommended: both a Narleaf Motion animation and your own scale/translate on the same element -->
<div data-nar-motion="scale-up" class="my-hover-effect">...</div>

<!-- Recommended: Narleaf Motion on the outer wrapper, your own effect on the inner element -->
<div data-nar-motion="scale-up">
  <div class="my-hover-effect">
    ...
  </div>
</div>
```

This way, the two elements manage their own `translate`/`scale` values independently, and neither overwrites the other.

## Accessibility

The system automatically respects the user's OS-level "reduce motion" (`prefers-reduced-motion: reduce`) preference. When enabled, all animations are disabled and content renders fully visible immediately, with no shift or delay.

## Browser support

The system works fully in browsers that support `animation-timeline: view()`: Chromium-based browsers (Chrome, Edge, Opera; 115+) and Safari 26+. Firefox support is behind a flag at the time of writing.

In unsupported browsers (the `@supports not (...)` block takes over), no animation is applied — elements are fully opaque and in their normal position from the start. In other words, a user on an unsupported browser sees no missing content or broken layout — the animation effect simply doesn't happen.

## Things to know

Because the system runs on `animation-timeline: view()`, whether an animation triggers depends entirely on **scroll position**. This has a few practical consequences:

- **Elements visible on initial load:** elements already inside the viewport when the page loads are evaluated against their current position on the scroll timeline. As a result, the animation's starting state may differ from that of elements the user later scrolls into view.
- **Nested vertical scroll:** the `view()` timeline automatically references the element's nearest scrollable ancestor. So if an element sits inside a `section` with its own vertical scroll, the animation follows that section's scroll rather than the page's — no extra configuration needed.
- **Horizontal scroll containers aren't supported:** the system tracks the vertical (block) axis by default. If the element's container scrolls horizontally (e.g. a carousel), the animation won't trigger as expected.
- **A brief "flash" is possible if the CSS loads late:** if `narleaf-motion.css` loads after the HTML content, or asynchronously, the user may briefly see the fully visible page before it snaps to the animation's "reset" state (e.g. `opacity: 0`). To avoid this, load the file early, in `<head>`, in a render-blocking way (i.e. without `async`/`defer`).

### Minimum supported browser versions

Browser support tracks native `animation-timeline: view()` support directly. Since version numbers change over time, rather than keeping a fixed table here, you can check current status at:

- [MDN — animation-timeline: view()](https://developer.mozilla.org/en-US/docs/Web/CSS/animation-timeline/view)
- [Can I Use — animation-timeline: view()](https://caniuse.com/mdn-css_properties_animation-timeline_view)

In browsers not listed there, or without support, the system automatically falls back (see [Browser support](#browser-support)) — content renders without animation but fully visible.

## Demo

**Live demo:** [withmehmet.github.io/narleaf-motion](https://withmehmet.github.io/narleaf-motion/)

Or open the `index.html` file in the repo in a browser to explore all animation types and parameter combinations locally.

**Examples:** Narleaf Motion on real page layouts (all content is fictional).

- [Landing page](https://withmehmet.github.io/narleaf-motion/examples/landing.html)
- [Portfolio](https://withmehmet.github.io/narleaf-motion/examples/portfolio.html)
- [Blog](https://withmehmet.github.io/narleaf-motion/examples/blog.html)

The source of each example is in the `examples/` folder.

The demo page uses [Public Sans](https://github.com/uswds/public-sans) and [IBM Plex Mono](https://github.com/IBM/plex), bundled in `fonts/` (no external requests). Both are licensed under the SIL Open Font License 1.1; see the `OFL.txt` file in each font's folder. The fonts are not part of the Narleaf Motion library.

The demo and example pages are laid out with [Narleaf Layout](https://github.com/withmehmet/narleaf-layout) (`narleaf-core.css`, MIT). It is only used by the demo pages; the Narleaf Motion library itself does not depend on it.

## Why CSS-only?

- No extra library/package to install, no bundle size increase
- Doesn't use scroll event listeners or `requestAnimationFrame` — animations are handled by the browser's native scroll-driven animation system
- Works regardless of framework — React, Vue, or plain HTML, it doesn't matter

## Contributing

Bug reports and pull requests are welcome. Please open an issue to discuss before proposing a change.

## Development process

Narleaf Motion's design decisions and architectural boundaries were set by a human; the code was written and tested with the help of AI assistants.

## License

MIT © withmehmet — see [LICENSE](LICENSE)
