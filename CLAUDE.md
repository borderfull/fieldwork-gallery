# Fieldwork Gallery — Design & Code Constraints

## Overview
A photographic gallery portal for Prince Tomar's fieldwork in Ladakh. Two exhibitions: **Roads of Ladakh** and **Traces**. Built as a single `Gallery Portal.html` file, deployed to Vercel as a subdomain.

---

## Design System

### Color Palette
```css
--paper:    #f1e9d8   /* warm parchment — main background */
--paper-2:  #ebe0c8   /* slightly darker parchment — sidebar, frame */
--ink:      #1c1814   /* near-black for headings */
--ink-soft: #4a3f35   /* body text */
--ink-mute: #8a7a6e   /* muted labels, hints */
--accent:   #c4603a   /* terracotta — active indicator, highlights */
--rule:     #d4c4ae   /* subtle dividers */
```

### Typography
- **Cormorant** (serif) — titles, captions, display text
- **IBM Plex Mono** — coordinates, counters, metadata labels
- **Spectral** — body / opening text (minimal usage)
- No Inter, Roboto, or Arial

### Tone
Minimal, understated, ethnographic. No emoji. No gradient backgrounds. No rounded-corner accent-border containers.

---

## Exhibition Navigation (Top Bar)

- Exhibition names are plain text buttons, slightly dark (`--ink-soft`)
- Active exhibition: full ink color + **terracotta bottom border only** (2px, `--accent`)
- No background fill, no top/bottom grey lines, no pill shape on buttons
- Share button: plain text/icon, not a pill

---

## Viewer Layout

- Left: sidebar with **title** (Cormorant, large) + **description** (Spectral, italic, two lines together, same size) + image counter
- Right: large image fill — no dark background behind images, sits on `--paper` palette
- Description lines must stay together (no separation), both italic, same font size
- Images use `<picture>` with WebP source + JPEG fallback

---

## Lightbox Design

- **Overlay**: `rgba(20,16,12,0.88)` dark charcoal
- **Frame**: `--paper-2` background, generous padding — **thin equal padding on top/left/right, thicker at bottom** (bottom holds caption)
- Image: `object-fit: contain`, fills frame, never stretched
- **Caption** (bottom of frame, left): district name in Cormorant serif + coordinates in IBM Plex Mono monospace, uppercase, small
- **Close button** (×): top-right of viewport, circular, `--paper` color, rotates 90° on hover
- **Prev/Next arrows** (‹ ›): left/right of viewport at vertical center, circular, `--paper` color — fully visible, not clipped by viewport edge
- No "Esc · click outside to close" hint text
- Opens with scale-in animation (0.92 → 1.0, 320ms ease)
- Clicking image OR overlay closes lightbox

---

## District Metadata (Lightbox)

Each exhibition has a `districts` array parallel to `images`:
```js
districts: [
  { name: 'Nubra', coords: '35.42°N 77.52°E' },
  ...
]
```
- District name: Cormorant serif, `--paper` color
- Coordinates: IBM Plex Mono, muted paper color, small
- If no district data for an image, metadata block hides

**Roads of Ladakh districts:**
1. Nubra · 2. Nubra · 3. Khardung La · 4. Khardung La · 5. Leh · 6. Leh · 7. Leh

**Traces districts:**
1. Nubra · 2. Rangdum · 3. Zanskar · 4. Zanskar · 5. Markha Valley · 6. Somewhere in Ladakh

> ⚠️ Do NOT use real GPS coordinates — this is a sensitive border area. Use district-level locations only.

---

## Code Quality Rules

- **No `innerHTML`** for user/data content — use `createTextNode` + `createElement`
- **No `window.__*`** globals for ephemeral state — use local `let`
- **CSS class toggles** (`.hidden`) over inline `style.display` changes
- Dead variables (`lbCurrent`, `lbTotal` if unused) must be removed
- Touch swipe uses local `let touchStartX`, not a global

---

## Security (vercel.json)

- CSP headers restrict scripts/fonts to known origins
- `X-Frame-Options: SAMEORIGIN`
- `X-Content-Type-Options: nosniff`
- `uploads/` folder is blocked via rewrite rule — never expose dev/upload files

---

## Adding New Exhibitions

Add a new key to the `exhibitions` object following this template:
```js
newExhibition: {
  title: 'Exhibition Title',
  subtitle: 'One line description.',
  opening: 'Optional second line of context.',
  images: ['images/new/01.jpg', ...],
  districts: [
    { name: 'District Name', coords: 'XX.XX°N YY.YY°E' },
    ...
  ],
},
```
- Add a button to the nav toggle: `<button class="nav-exhib" data-exhib="newExhibition">Title</button>`
- Follow the same image naming convention (`images/<key>/01.jpg` etc.)
- All lightbox, viewer, and metadata logic applies automatically

---

## Image Files
- Format: JPEG source + WebP versions (WebP served first via `<picture>`)
- Location: `images/roads/` and `images/traces/`
- Naming: `01.jpg`, `02.jpg`, etc.
- The `uploads/` folder is for dev only — never deploy its contents
