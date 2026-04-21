# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Personal ePortfolio/resume website for Justin Golden (EE student at Georgia Tech), hosted on GitHub Pages. No build tools, frameworks, or dependencies — pure HTML, CSS, and vanilla JavaScript.

## Development

**No build step required.** Open `index.html` directly in a browser, or serve it locally:

```bash
python -m http.server 8000
# or
npx serve .
```

There are no linters, test suites, or npm scripts.

## Architecture

### Main site (`index.html` + `css/styles.css` + `js/main.js`)

`index.html` is a single-page site with six anchor sections: Hero (`#home`), About (`#about`), Career Goals (`#goals`), Projects (`#projects`), Resume (`#resume`), Contact (`#contact`).

`css/styles.css` is the full design system built on CSS custom properties:
- Georgia Tech palette: `--gt-navy: #003057`, `--gt-gold: #B3A369`, `--gt-gold-dark`
- Responsive breakpoints: 900px (tablet), 768px (mobile hamburger), 480px (single-column)
- All scroll-reveal state lives on `.visible` (not `.revealed`)

`js/main.js` handles: navbar scroll state, mobile hamburger, smooth scroll, active nav link via `IntersectionObserver` on `section[id]`, scroll-reveal animations, and project category filtering.

### Project subpages (`pages/*.html` + `css/project.css` + `js/project.js`)

Each project gets its own page under `pages/`. Current pages: `pages/led-ceiling.html`.

`css/project.css` adds all subpage-specific styles on top of `styles.css` (both are linked). Key layout classes:
- `.proj-hero` — dark navy gradient hero with `.proj-meta-badges`
- `.tech-sub` — two-column grid (content + image placeholder). Add `.reverse` to flip order via `direction: rtl` — this visually swaps columns without changing HTML source order. Sections must strictly alternate: A (no reverse), B (`.reverse`), C (no reverse), D (`.reverse`), etc.
- `.tech-sub.full-width` — single-column, used for BOM/wide content
- `.gallery-grid` — 3-col responsive grid; `.gallery-item[data-src]` items trigger the lightbox
- `.img-placeholder` / `.bom-placeholder` — styled dashed-border placeholders for missing media

`js/project.js` handles: navbar scroll state, mobile hamburger, scroll-reveal, and lightbox. The lightbox reads `data-src` and `data-alt` from `.gallery-item` elements — placeholder divs without `data-src` don't trigger it.

### Linking between pages

Subpages live one level deep (`pages/`), so all asset paths use `../` prefixes:
- Stylesheets: `../css/styles.css` and `../css/project.css`
- Scripts: `../js/project.js`
- Nav links: `../index.html#section`
- Assets: `../assets/filename`

The main site's project card "Details" links point to `pages/led-ceiling.html` (no `../` needed from root).

## Key Patterns

**Project filtering** — Cards in `#projectsGrid` have `data-category` (`work`, `personal`, `research`, `coursework`). Filter buttons use `data-filter` and toggle `.hidden`. The `coursework` button has the `hidden` attribute and is not currently shown — remove it to display.

**Scroll-reveal** — Add `data-reveal` to any element. JS sets inline `transitionDelay` (`i % 4 * 80ms`) and adds `.visible` on intersection. Works identically in both `main.js` and `project.js`.

**Active nav tracking** — Only applies on `index.html` (main.js). Matches `nav-link[data-section]` against `section[id]`. Subpages have no active-link tracking since they're not single-page.

**Hero photo** — `.hero-img-frame` is a fixed square (340px / 220px mobile) with `object-fit: cover; object-position: top center` for a circle-cropped headshot anchored to the top.

**About photo** — `.about-photo` has `width: 100%` and no fixed height, preserving the source aspect ratio naturally.

**Resume preview** — Inline HTML resume uses `.rdoc-*` classes. `.rdoc-entry-header` and `.rdoc-entry-sub` are flex rows that collapse to column on mobile. Download button links to `assets/resume.pdf`.

**Replacing gallery placeholders with real images** — Swap `.gallery-placeholder` div for `.gallery-item` div with `data-src="../assets/photo.jpg"` `data-alt="..."`, and put an `<img class="gallery-img">` inside. The lightbox activates automatically.
