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

Single-page site with three source files:

- **`index.html`** — All content and structure. Six sections: Hero (`#home`), About (`#about`), Career Goals (`#goals`), Projects (`#projects`), Resume (`#resume`), Contact (`#contact`). Smooth-scroll SPA behavior via anchor links.
- **`css/styles.css`** — Design system built on CSS custom properties. Georgia Tech color palette (`--gt-navy: #003057`, `--gt-gold: #B3A369`, `--gt-gold-dark`). Responsive breakpoints at 900px, 768px, and 480px.
- **`js/main.js`** — Vanilla JS for: navbar scroll state, mobile hamburger menu, smooth scroll, active nav link tracking via `IntersectionObserver`, scroll-reveal animations, and project category filtering.
- **`assets/`** — Real photos (headshots, project images) and SVG placeholder graphics.

External dependencies (CDN only, no local install):
- Google Fonts: Playfair Display, Inter, JetBrains Mono
- Font Awesome 6.5.0

## Key Patterns

**Project filtering** — Project cards have a `data-category` attribute (`work`, `personal`, `research`, `coursework`). Filter buttons use `data-filter` and toggle `.hidden` on non-matching cards. The `coursework` filter button exists in the HTML with the `hidden` attribute and is not currently displayed. To add it back, remove the `hidden` attribute from the button. Category badge CSS classes: `cat-work` (green), `cat-personal` (blue), `cat-research` (gold/brown), `cat-coursework` (navy).

**Scroll-reveal animations** — Elements with `[data-reveal]` are observed by `IntersectionObserver`; when visible, `.visible` is added (not `.revealed`), triggering CSS transitions. Stagger delay is set inline via `transitionDelay` in JS (every 4th element resets the cycle at `i % 4 * 80ms`). Once revealed, elements are unobserved.

**Active nav tracking** — Sections with `id` are observed; the nav link whose `data-section` matches the intersecting section's `id` gets `.active`. The observer uses `--nav-h` CSS custom property for rootMargin offset (falls back to 68px).

**Hero photo** — `.hero-img-frame` is a fixed square (340px desktop, 220px mobile) with `object-fit: cover` and `object-position: top center` on `.hero-portrait` to ensure circular crop anchored to the top of the image.

**About photo** — `.about-photo` uses `width: 100%` with no fixed height, so it naturally preserves the source image's aspect ratio. Currently displays a landscape photo (`jgpiano.png`).

**About quick-stats** — Four `.stat-card` elements in `.quick-stats` show University, Major, Class Year, and Interests. The Interests card uses a single `.stat-value` `<p>` with all items separated by `·`.

**Resume preview** — The inline HTML resume uses `.rdoc-*` CSS classes. Section titles: `.rdoc-section-title` (gold, uppercase, bordered). Each entry uses `.rdoc-entry-header` (bold name + location) and `.rdoc-entry-sub` (italic role + date range) in a flex row. Skills use `.rdoc-skills-grid` with `.rdoc-skill-group` children. The download button links to `assets/resume.pdf`.

**Section labels** — Visible sections are numbered 01–05 in their `<span class="section-label">` (About through Contact); the Hero section has no label.
