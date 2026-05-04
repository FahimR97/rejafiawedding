# Rejaul & Afia — Wedding Gallery: Design Spec

**Date:** 2026-05-04  
**Author:** Fahim  
**Purpose:** A private wedding gift from photographers Fahim and Sahid. The couple (and their relatives) can view and download all wedding photos and videos.

---

## Overview

A single `index.html` file — all CSS and JS inline, no frameworks. A `MEDIA_DATA` array at the top of the JS block is the sole data source. It holds objects structured by creator, type, and Cloudflare R2 URL. Once real media is uploaded to R2, URLs are swapped in here.

Hosted on Cloudflare Pages at `rejalandafia.pages.dev`. Media served from Cloudflare R2. Both are in the same Cloudflare account. Source controlled on GitHub — Pages deploys on every push.

**Estimated cost:** ~$1.50 total for one month of R2 storage (~100GB). Pages and GitHub are free. No domain purchase needed.

---

## Data Model

```js
const MEDIA_DATA = [
  {
    creator: 'fahim',
    type: 'photo',
    src: 'https://<r2-bucket>.r2.dev/fahim/photos/file.jpg',         // full resolution original
    thumbnail: 'https://<r2-bucket>.r2.dev/fahim/thumbs/file.jpg',   // compressed, ~800px wide
    caption: 'Caption text'
  },
  {
    creator: 'fahim',
    type: 'video',
    src: 'https://<r2-bucket>.r2.dev/fahim/videos/file.mp4',         // full resolution original
    thumbnail: 'https://<r2-bucket>.r2.dev/fahim/thumbs/file.jpg',   // poster frame image
    caption: 'Caption text'
  },
  // sahid entries follow same structure
]
```

**Quality contract:**
- R2 serves original uncompressed files — no transcoding, no quality loss at any point
- `thumbnail` is used only in the gallery grid for fast page load (prevents loading gigabytes on open)
- `src` is used exclusively in the lightbox — full resolution, no size caps in CSS
- Thumbnails for photos: export a compressed copy at ~800px wide alongside the original when uploading to R2 (e.g. `fahim/thumbs/` folder mirrors `fahim/photos/`)
- Thumbnails for videos: a single poster frame JPEG exported from the clip
- Placeholder entries use royalty-free Unsplash/Pexels URLs for photos and a sample MP4 for videos during development

---

## Visual Design

**Palette**
| Token | Hex | Usage |
|---|---|---|
| ivory | `#faf6ee` | Page background, hero |
| parchment | `#f5ede0` | Card backgrounds, nav |
| gold | `#b8913a` | Accents, headings, dividers |
| warm ink | `#1c140a` | Body text, download section bg, footer |

**Typography**
- Display: Cormorant Garamond (Google Fonts) — headings, names, italic accents
- UI: Inter (Google Fonts) — nav, captions, buttons, metadata

**Decorative language**
- Thin gold SVG dividers with diamond centre motif
- Subtle corner flourishes (SVG) on hero
- Soft radial warm glow behind hero text
- The overall feel: cinematic luxury — think high-end wedding film meets South Asian stationery. Slow, intentional motion. Every scroll reveal feels earned.

**Cinematic parallax interludes**
- Full-bleed image panels placed between major sections (after hero, between Sahid/Fahim gallery blocks, before download section)
- Each interlude is a single beautiful photo from `MEDIA_DATA` spanning 60–70vh
- The image moves at ~60% of the scroll rate (CSS `background-attachment: fixed` with `background-size: cover`) creating depth
- A thin warm overlay preserves the ivory palette
- No text — purely visual breathing room
- On mobile: `background-attachment: scroll` (fixed attachment is broken on iOS Safari)

---

## Sections

### 1. Hero
- Full viewport height
- **Background media layer:** cycles through photos and video clips drawn from `MEDIA_DATA`. Photos use a slow Ken Burns zoom/pan effect. Video clips play muted and looped. Items crossfade every ~5s. A warm ivory overlay (`rgba(250,246,238,0.65)`) sits on top — keeps media ambient, not distracting, and preserves text readability.
- Decorative SVG arch motif layered above media, below text, low opacity gold
- Corner flourishes (SVG, four corners)
- Soft radial gold glow centred behind text
- Canvas: floating gold particle animation (motes rising slowly), layered above overlay
- Heading: "Rejaul & Afia" — Cormorant Garamond, large display size. The `&` is italic, gold (`#b8913a`)
- Subline: "3rd May 2026" in Inter spaced-caps, small, muted
- Two CTAs: "View Gallery" (gold outlined, smooth-scrolls to Gallery section) and "Download All" (gold filled, smooth-scrolls to Download section — does not trigger downloads immediately)
- On scroll: parallax shift on heading and arch
- Fallback: if `MEDIA_DATA` is empty (placeholder state), hero background is solid ivory

### 2. Sticky Navigation
- Frosted glass (`backdrop-filter: blur`), ivory/parchment background at low opacity
- Logo: "R & A" in italic Cormorant Garamond
- Links: "Gallery", "Download" — Inter, small caps
- Appears on scroll past hero, animates in from top

### 3. Gallery
- Ivory background
- Two creator blocks in order: **Sahid** first (photo-heavy, richer visual opening), then **Fahim** (video-heavy)
- Each block:
  - Creator name as italic Cormorant heading with thin gold rule beneath
  - **Photos subsection:** 3-column masonry layout using CSS `column-count: 3` (not experimental CSS Grid masonry — cross-browser safe). Cards have rounded corners, soft shadow.
    - Hover: gradient overlay rising from bottom, italic caption text, circular download button
  - **Videos subsection:** 2-column grid. Thumbnail (`<img>`) used as poster frame in card. `<video>` elements are NOT rendered in the grid — click opens lightbox.
    - Hover: play icon scales up, download button appears bottom-right
- Scroll-triggered stagger: each card fades up with `IntersectionObserver`, staggered delay by index
- Gold ornamental divider between Sahid and Fahim blocks
- **Photo lightbox:** loads `src` (full resolution). `<img>` is unconstrained in height, max-width 95vw. No CSS `filter` or `transform` that degrades quality.
- **Video lightbox:** native `<video>` element with `controls`, `preload="metadata"`, no autoplay. Plays original full-resolution file from R2. Supports browser fullscreen. Max dimensions 95vw / 90vh.
- **No quality-degrading CSS on media:** no `filter`, no forced `border-radius` on lightbox images, no `transform: scale` that blurs.

### 4. Download Section
- Background: warm ink `#1c140a` with soft radial gold glow
- Large Cormorant heading: "Take Everything With You"
- Short Inter subline: "Every photo and video, yours to keep."
- Three buttons:
  - **Photos** — iterates `MEDIA_DATA`, triggers `<a download>` for each photo `src`
  - **Videos** — same for videos
  - **Everything** — triggers both
- Button style: gold border, ivory text, hover fills gold

### 5. Footer
- Dark background (`#1c140a`)
- Centred: "Rejaul & Afia" in italic Cormorant Garamond
- Below: "3rd May 2026" in Inter spaced-caps, muted gold
- Minimal — no links, no extra copy

---

## Animations

| Element | Animation |
|---|---|
| Hero heading | Staggered fade + translateY on load (names, ampersand, date, CTAs each delayed) |
| Gold particles | Canvas animation — small gold circles rising, fading out at top, randomised size/speed/opacity |
| Cursor glow | Soft radial gradient div following `mousemove`, pointer-events none. Desktop only — disabled on touch devices via `@media (hover: none)` |
| Hero parallax | Heading + arch translate on `scroll` event |
| Section entrance | `IntersectionObserver` fade + translateY, threshold 0.15 |
| Card stagger | Each card in a grid has delay = index * 60ms |
| Gold dividers | SVG line `stroke-dashoffset` animates to 0 when scrolled into view |
| Photo lightbox | Clicked photo scales in from 0.85 opacity 0, closes on backdrop click or Escape |
| Nav | Slides in from top when hero exits viewport |

---

## Deployment

1. GitHub repo: `rejafiawedding` (public or private)
2. Cloudflare Pages: connect repo, set build output to `/` (no build step), branch `main`
3. Live URL: `rejalandafia.pages.dev` (or similar available name)
4. Cloudflare R2: create public bucket, upload media organised as `fahim/photos/`, `fahim/videos/`, `sahid/photos/`, `sahid/videos/`
5. Swap placeholder URLs in `MEDIA_DATA` with real R2 URLs, push to GitHub — Pages redeploys automatically
6. After ~1 month: delete R2 bucket, disable Pages project

---

## Out of Scope

- Admin upload panel (dropped — Fahim manages R2 directly)
- Backend / database
- Authentication / password protection (URL is effectively private by obscurity)
- Custom domain
