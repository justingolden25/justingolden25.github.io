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

- **`index.html`** — All content and structure. Six sections: Hero, About, Career Goals, Projects, Resume, Contact. Smooth-scroll SPA behavior via anchor links.
- **`css/styles.css`** — Design system built on CSS custom properties. Georgia Tech color palette (`--navy: #003057`, `--gold: #B3A369`). Responsive breakpoints at 900px, 768px, and 480px.
- **`js/main.js`** — Vanilla JS for: navbar scroll state, mobile hamburger menu, smooth scroll, active nav link tracking via `IntersectionObserver`, scroll-reveal animations, and project category filtering.
- **`assets/`** — SVG graphics (headshot, four project icons).

External dependencies (CDN only, no local install):
- Google Fonts: Playfair Display, Inter, JetBrains Mono
- Font Awesome 6.5.0

## Key Patterns

**Project filtering** — Project cards have a `data-category` attribute (`coursework`, `personal`, `research`). Filter buttons toggle `.hidden` on cards.

**Scroll-reveal animations** — Elements with `.reveal` class are observed by `IntersectionObserver`; when visible, `.revealed` is added, triggering CSS transitions. Staggered via `--delay` CSS custom property.

**Active nav tracking** — Sections are observed; whichever is most visible gets the corresponding nav link marked `.active`.
