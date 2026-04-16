# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Static personal portfolio site deployed via GitHub Pages. No build system, no package manager, no framework — everything lives in a single file.

**The entire site is `index.html`.** All CSS (inline `<style>`), HTML structure, and JavaScript (inline `<script>`) are in this one file.

**Workflow:** All edits are made to `index_ccode.html` first. The user reviews and manually merges into `index.html`.

## Development

```bash
python3 -m http.server 8080
# then open http://localhost:8080/index_ccode.html
```

No build, lint, or test commands exist.

## Architecture

### Design System (CSS variables in `:root`)

**Colors**
- `--accent-red: #ff2d2d` — primary interactive accent (hover, active, focus, glow)
- `--accent-warm: #ff6b35` — secondary accent (tags, skill pills, project links) — replaces blue
- `--accent-green: #00e87a` — coder green, used only for "Mission Log" text in hero bio
- `--primary: #f0f0f4` — main body text (off-white, not pure white)
- `--silver: #c4c4d0` — secondary text, category headings, info block headers
- `--secondary: #8a8a9a` — muted text, nav links
- `--bg-primary: #0c0c10`, `--bg-secondary: #13131a`, `--bg-tertiary: #1a1a24` — layered dark backgrounds with slight purple undertone (X/Tesla style)
- `--bg-card: rgba(20,20,28,0.8)` — card background

**Borders & Glow**
- `--border-glass: rgba(255,255,255,0.08)` — subtle glass borders
- `--border-silver: rgba(196,196,208,0.15)` — silver-tinted borders used on most elements
- `--glow-red / --glow-red-soft` — red glow for accent elements
- `--glow-warm` — orange glow for warm accent elements
- `--glow-white: rgba(255,255,255,0.12)` — white glow for silver headings
- `--glow-green: rgba(0,232,122,0.3)` — green glow for coder text

**Fonts:** IBM Plex Mono (body/mono), Bebas Neue (display), Noto Sans JP (project titles)
**Icons:** Font Awesome 6.4.0 via CDN

### Design Aesthetic
- **X / Tesla palette** — 85% layered dark, 15% glowing white/silver accents
- **Apple-meets-Valorant feel** — glassmorphism panels, frosted modal, red scan-line, smooth cubic-bezier transitions
- **No pure black** — all backgrounds use `#0c0c10` or above with gradient depth
- **Glassmorphism:** nav, skill categories, project cards, modal all use `backdrop-filter: blur()`
- **Border radius:** pills=20px, cards=12px, skill-categories=10px, modal=16px, inputs=8px, social icons=10px, buttons=6px
- **Glow animations:** `glowPulse` on hero name and section numbers; `blink` on section marker cursor; `scanLine` sweeps page every 6s; `fadeSlideUp` on scroll reveal

### Page Sections (in order)
1. `nav` — frosted glass top bar with logo (red glowing dot), nav links, social icons, mobile hamburger
2. `#hero` — two-column grid: left = profile photo (color, no grayscale) + name (glow pulse) + social links; right = bio (Mission Log line in green) + resume download + core skills/education grid
3. `#skills` — 6 skill categories as glassmorphic cards, all pills have icons, pill shape (border-radius: 20px)
4. `#projects` — pill filter buttons + dynamically rendered project cards (color images, glass cards, shimmer hover)
5. `#contact` — Formspree form in glassmorphic container + direct email
6. `footer`
7. `#projectModal` — frosted glass overlay, image carousel with rounded arrow buttons (◀ ▶), project details as `<ul><li>` bullet list

### JavaScript (bottom of `<body>`)
All JS is vanilla, no libraries.

**`projectsData` array** — single source of truth for all project content. Each entry:
- `id` — modal key (e.g., `"modal1"`)
- `title`, `tldr`, `contribution`, `details[]`
- `images[]` — paths relative to repo root (e.g., `"images/pair/pair_1.gif"`)
- `category` — space-separated string matched by `filterProjects()` (e.g., `"ros2 python robotics"`)
- `tags[]` — displayed as `#tag` pills
- `github` — empty string `""` if no link

**Key functions:**
- `renderProjects(projects)` — called on `DOMContentLoaded`, builds all `.project-card` elements
- `filterProjects(category)` — shows/hides cards, re-triggers `fadeSlideUp` on visible cards
- `openModal(projectId)` — builds modal HTML with image carousel (prev/next arrows + dots)
- `changeCarouselImage(index)` — toggles `.active` on carousel images and dots
- `stepCarousel(dir)` — +1/-1 relative navigation, wraps around
- Scroll reveal — `IntersectionObserver` adds `.visible` to `.reveal` elements (project cards, skill categories) with staggered delays
- Grid parallax — mouse position subtly shifts grid background via CSS custom properties

### Skill Icons (Font Awesome)
All six Technical Arsenal categories have icons on every pill:
- **Programming:** fa-python, fa-c, fa-copyright, fa-java
- **Robotics:** fa-robot, fa-cube, fa-eye, fa-route, fa-map
- **Embedded:** fa-microchip, fa-wifi, fa-raspberry-pi, fa-clock, fa-network-wired
- **CV & AI:** fa-fire (PyTorch), fa-camera, fa-hand, fa-filter
- **Dev Tools:** fa-docker, fa-linux, fa-git-alt, fa-terminal
- **CAD:** fa-drafting-compass, fa-cube, fa-pen-ruler

### Adding a New Project
1. Add an entry to `projectsData` with a unique `id`
2. Add images to `images/<project-folder>/`
3. Card and modal are auto-generated — no additional HTML needed

### External Services
- **Google Analytics:** GA4 tag `G-8KQ4X9ZTJF` in `<head>`
- **Contact form:** Formspree endpoint `https://formspree.io/f/mjkpkopz`
- **Resume:** `assets/Amar_MASTER_PAIR.pdf` (linked as download in hero section)
