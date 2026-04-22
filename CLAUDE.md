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
- `.tech-sub` — two-column grid (content + image). Add `.reverse` to flip order via `direction: rtl` — this visually swaps columns without changing HTML source order. Sections must strictly alternate: A (no reverse), B (`.reverse`), C (no reverse), D (`.reverse`), etc.
- `.tech-sub.full-width` — single-column, used for BOM/wide content
- `.gallery-grid` — 3-col responsive grid; `.gallery-item[data-src]` items trigger the lightbox
- `.img-placeholder` / `.bom-placeholder` — styled dashed-border placeholders for missing media

`js/project.js` handles: navbar scroll state, mobile hamburger, scroll-reveal, and lightbox. The lightbox reads `data-src` and `data-alt` from `.gallery-item` elements — placeholder divs without `data-src` don't trigger it.

### Linking between pages

Subpages live one level deep (`pages/`), so all asset paths use `../` prefixes:
- Stylesheets: `../css/styles.css` and `../css/project.css`
- Scripts: `../js/project.js`
- Nav links: `../index.html#section`
- Assets: `../assets/filename` or `../assets/led-ceiling-pics/filename`

The main site's project card "Details" links point to `pages/led-ceiling.html` (no `../` needed from root).

## Key Patterns

**Project filtering** — Cards in `#projectsGrid` have `data-category` (`work`, `personal`, `research`, `coursework`). Filter buttons use `data-filter` and toggle `.hidden`. The `coursework` button has the `hidden` attribute and is not currently shown — remove it to display.

**Scroll-reveal** — Add `data-reveal` to any element. JS sets inline `transitionDelay` (`i % 4 * 80ms`) and adds `.visible` on intersection. Works identically in both `main.js` and `project.js`.

**Active nav tracking** — Only applies on `index.html` (main.js). Matches `nav-link[data-section]` against `section[id]`. Subpages have no active-link tracking since they're not single-page.

**Hero photo** — `.hero-img-frame` is a fixed square (340px / 220px mobile) with `object-fit: cover; object-position: top center` for a circle-cropped headshot anchored to the top.

**About photo** — `.about-photo` has `width: 100%` and no fixed height, preserving the source aspect ratio naturally.

**Resume preview** — Inline HTML resume uses `.rdoc-*` classes. `.rdoc-entry-header` and `.rdoc-entry-sub` are flex rows that collapse to column on mobile. Download button links to `assets/resume.pdf`.

**Replacing gallery placeholders with real images** — Swap `.gallery-placeholder` div for `.gallery-item` div with `data-src="../assets/photo.jpg"` `data-alt="..."`, and put an `<img class="gallery-img">` inside. The lightbox activates automatically.

**Replacing tech-sub image placeholders with real images** — Swap the `.img-placeholder` div for `<img class="tech-sub-img" src="../assets/..." alt="..." />`. The `.tech-sub-img` class sets `width: 100%; object-fit: contain; border-radius: var(--radius)`.

**Rotating a tech-sub image 90°** — Wrap the `<img>` in `<div class="tech-sub-img-rotated-wrap">`. The wrapper uses `display: flex; overflow: hidden; clip-path: inset(0 round 12px)` to contain the rotated layout bleed and apply rounded corners. The image inside uses `transform: rotate(90deg); height: 100%; max-width: none; flex-shrink: 0`. Fine-tune vertical position with `margin-top` on the wrapper (negative values move it up).

**Vertically stretching a tech-sub image to match text height** — Add class `tech-sub-img-vstretch` to the `<img>`. This sets `align-self: stretch; height: 100%; object-fit: fill`, making the image grow to the grid row height (driven by the text column) and fill it without maintaining aspect ratio.

**Cropping a tech-sub image edge** — Use an inline `style="clip-path: inset(0 0 0 X%)"` on the `<img>` to trim a percentage off the left (or adjust the four values for top/right/bottom/left). Non-destructive — the original file is unchanged. Include `round 12px` inside the `clip-path` value to preserve border-radius when cropping (e.g. `clip-path: inset(0 0 22% 0 round 12px)`).

**Embedding a YouTube video in the gallery** — Replace `.gallery-video-placeholder` with `<div class="gallery-video-embed"><iframe src="https://www.youtube.com/embed/[ID]" ...></iframe></div>`. The `.gallery-video-embed` wrapper spans all 3 gallery columns, uses `aspect-ratio: 16/9`, and positions the iframe absolutely to fill it. At 900px it spans 2 columns; at 480px it spans 1.

**Embedding a Google Sheets BOM** — Replace `.bom-placeholder` with `<div class="bom-embed"><iframe src="https://docs.google.com/spreadsheets/d/e/[ID]/pubhtml?gid=[SHEET_ID]&single=true&widget=true&headers=false"></iframe></div>`. The iframe is 480px tall, full width, no border.

**Hero image (replacing placeholder)** — Swap `.proj-hero-img-placeholder` for `<img class="proj-hero-img" src="../assets/..." alt="..." data-reveal />`. The `.proj-hero-img` class sets `width: 100%; height: 380px; object-fit: cover; border-radius: var(--radius-lg)`.

**`.proj-meta-badges`** — Flexbox pill-shaped badges in the hero. Each badge is `<span class="proj-meta-badge"><i class="fa-..."></i> Label</span>`. Pills have a semi-transparent white background, thin border, and gold icon color. No hover state.

**`.back-bar`** — Sticky bar below the navbar (`top: var(--nav-h)`, `z-index: 900`) present on all subpages. Contains a `.back-btn` link back to `../index.html#projects`.

**Subpage navbar links** — Unlike `index.html`, subpage nav links are plain `<a href="../index.html#section">` anchors (no `data-section` attribute). There is no active-link tracking on subpages.

**`direction: rtl` breakpoint** — `.tech-sub.reverse` uses `direction: rtl` to swap columns. At the 900px breakpoint this is reset to `ltr` because the layout collapses to single-column. Children inside `.tech-sub` always have `direction: ltr` set explicitly to prevent text mirroring.

**Responsive breakpoints (project pages)** — 900px: tech-sub → single column, gallery → 2 cols; 768px: hero/section padding reduced, placeholder heights shrink; 480px: gallery → 1 col.

**Lightbox close triggers** — Click `.lightbox-close` button, click the dark overlay background (checks `e.target === lightbox`), or press Escape. On open, `document.body.style.overflow = 'hidden'` locks scroll; restored on close. Image `src` is cleared on close. No next/prev navigation — Escape only.

**Gallery item hover** — `.gallery-item:hover .gallery-img` scales to `1.04` over 400ms ease. No other interactive state on gallery items.
