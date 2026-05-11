# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Static single-page website for **Piváreň Pikart**, a Slovak pub/bar in Banská Bystrica-Sásová, Slovakia. No build process, no package manager, no dependencies — just HTML, CSS, and vanilla JavaScript.

## Development

Open `index.html` directly in a browser, or use a simple local server:

```powershell
# Python (if available)
python -m http.server 8080

# Node (if available)
npx serve .
```

`pivaren-pikart.html` is an earlier design iteration with a different visual style (Playfair Display typography, gold/violet palette). It is not the primary file.

## Architecture

All code lives in `index.html` (~1300 lines):

- **Lines 1–755 approx**: Embedded `<style>` block — CSS custom properties (design tokens), layout, animations, responsive breakpoints
- **Lines 755–1230 approx**: HTML markup — 11 named sections (see below)
- **Last ~72 lines**: Embedded `<script>` block — vanilla JS for sticky navbar, mobile menu, scroll-reveal, smooth anchoring

### Design Tokens (CSS custom properties on `:root`)

| Variable | Value | Usage |
|---|---|---|
| `--cream` | `rgb(250,235,215)` | Main background, hero text |
| `--near-black` | `rgb(13,13,13)` | Card backgrounds, footer |
| `--off-white` | `rgb(250,249,245)` | Alternate section background |
| `--charcoal` | `rgb(33,33,33)` | Borders, body text |
| `--font-display` | `'Bebas Neue'` | All headlines |
| `--font-body` | `'Plus Jakarta Sans'` | Body copy |

### Page Sections (by anchor ID)

| Anchor | Section |
|---|---|
| `#navbar` | Fixed sticky nav with hamburger for mobile |
| `#hero` | Full-screen video background with CTAs |
| `#o-nas` | About — photo strip, stats (founded 2022, hours, location) |
| *(no id)* | Animated marquee ticker |
| `#menu` | 4 alternating food cards (dark/cream) |
| `#udalosti` | Events — split-panel hero + 2×2 event grid |
| `#napoje` | Drinks — horizontal scroll carousel (5 categories) |
| `#rezervacia` | Reservation CTA — phone button |
| *(no id)* | 5-panel gallery strip with hover zoom |
| `#recenzie` | 3-card Google reviews grid |
| `#kontakt` | 3-column footer (contact, hours, social) |

### JavaScript (vanilla, no libraries)

- **Sticky navbar**: adds `.scrolled` class past 60px scroll → backdrop blur activates
- **Mobile menu**: hamburger toggle + Escape key closes overlay
- **Scroll reveal**: `IntersectionObserver` on `.reveal`, `.reveal-d1/.d2/.d3` (staggered delays)
- **Smooth anchoring**: intercepts `<a href="#...">` clicks, offsets for navbar height

### Assets

```
Assets/
  Photos/   ← 11 PNG images (source originals)
  Videos/
    PIKART.mp4   ← hero background video
```

Duplicate image files exist in the project root (convenience copies for HTML `src` paths).

## Language & Content

The site is in Slovak (`lang="sk"`). Business details embedded in HTML:
- Phone: +421 917 261 425
- Address: Starohorská 4, Banská Bystrica – Sásová
- Hours: Mon–Thu 11:00–23:00, Fri–Sat 11:00–24:00, Sun closed
- Instagram: @pivaren_pikart_2022
