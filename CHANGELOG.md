# Changelog

This file documents notable changes to the project, release by release. The format is based on [Keep a Changelog](https://keepachangelog.com/), and this project adheres to [Semantic Versioning](https://semver.org/).

## [Unreleased]

## [0.1.0] - 2026-11-17

Initial release.

### Added

- Four scroll-driven effects via `data-nar-motion`: `fade`, `scale-up`, `slide-up`, `slide-down`
- Timing control with `data-nar-motion-start` and `data-nar-motion-end` (0–100, steps of 5)
- Optional `data-nar-scale-start` (50–90) for `scale-up` and `data-nar-slide-distance` (30–150 px) for `slide-up` / `slide-down`
- Opt-in `data-nar-motion-will-change`
- Respects `prefers-reduced-motion`: animations are disabled and content stays fully visible
- Progressive enhancement: browsers without scroll-driven animation support show content normally
- Zero-specificity selectors (`:where()`), so your own styles always win
- Demo page with every effect and option on one page
- Three example pages (landing page, portfolio, blog) showing the effects on real page layouts, laid out with [Narleaf Layout](https://github.com/withmehmet/narleaf-layout)
- Bundled fonts for the demo pages (Public Sans, IBM Plex Mono; SIL Open Font License 1.1)

[Unreleased]: https://github.com/withmehmet/narleaf-motion/compare/v0.1.0...HEAD
[0.1.0]: https://github.com/withmehmet/narleaf-motion/releases/tag/v0.1.0
