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

All code lives in `index.html` (~1516 lines):

- **Lines 1–911**: Embedded `<style>` block — CSS custom properties (design tokens), layout, animations, responsive breakpoints, popup styles
- **Lines 913–1401**: HTML markup — 11 named sections + event popup overlay (see below)
- **Lines 1405–1513**: Embedded `<script>` block — vanilla JS for sticky navbar, mobile menu, scroll-reveal, smooth anchoring, and event popup logic

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
| `#event-popup-overlay` | Modal popup for current event (shown on page load) |

### JavaScript (vanilla, no libraries)

- **Sticky navbar**: adds `.scrolled` class past 60px scroll → backdrop blur activates
- **Mobile menu**: hamburger toggle + Escape key closes overlay
- **Scroll reveal**: `IntersectionObserver` on `.reveal`, `.reveal-d1/.d2/.d3` (staggered delays)
- **Smooth anchoring**: intercepts `<a href="#...">` clicks, offsets for navbar height
- **Event popup**: driven by `NEW_COURSE` object (line ~1480); shown 1.4 s after load, once per session (`sessionStorage` key `pikart_popup_seen`); set `enabled: false` to suppress

### Event Popup System

The popup is controlled by the `NEW_COURSE` JS object embedded in `index.html` (line ~1480):

```js
var NEW_COURSE = {
  enabled: true,       // set false to hide popup
  type: "Quiz Night",  // shown in meta line (top label)
  title: "Quiz Night", // heading + sessionStorage dedup key
  date: "Utorok 20. mája · 19:00",
  description: "..."
};
```

`popup.json` in the project root mirrors this config in JSON format (reference copy — not loaded at runtime). `popup-draft.json` holds a pending Telegram image `file_id` used during content drafting.

### n8n Automation (`n8n-popup-workflow.json`)

A Telegram-triggered n8n workflow lets the owner update the popup remotely without touching the code:

1. **Telegram Trigger** — listens for a message in the format `Type\nDate\nDescription`
2. **Spracuj správu** — parses the message into `{type, date, description}`
3. **Stiahni index.html** — fetches `index.html` from GitHub via REST API
4. **Priprav nový obsah** — replaces the `NEW_COURSE = { ... }` block via regex
5. **Aktualizuj index.html** — commits the updated file back to GitHub (`praecantat0r/PIKART`, branch `main`)
6. **Odosli potvrdenie** — sends a Telegram confirmation with the new content

This workflow targets the `NEW_COURSE` block by regex (`/var NEW_COURSE = \{[\s\S]*?\};/`), so the block must stay as a single `var` declaration without nesting to remain patchable.

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
