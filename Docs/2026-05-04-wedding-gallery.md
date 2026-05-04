# Wedding Gallery Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a single self-contained `index.html` cinematic wedding gallery for Rejaul & Afia, deployable to Cloudflare Pages at `rejalandafia.pages.dev`.

**Architecture:** All HTML, CSS, and JS live in one `index.html` — no build step, no frameworks. A `MEDIA_DATA` array at the top of the `<script>` block is the sole data source. Gallery, hero media background, parallax interludes, and download buttons all read from this array. Swapping placeholder URLs for real Cloudflare R2 URLs is the only change needed for production.

**Tech Stack:** Vanilla HTML5 / CSS3 / ES6+, Google Fonts (Cormorant Garamond + Inter), Cloudflare R2 (media storage), Cloudflare Pages (hosting), GitHub (source control)

---

## File Map

| File | Responsibility |
|---|---|
| `index.html` | Entire site — HTML structure, inline `<style>`, inline `<script>` |

All tasks append to or modify `index.html`. The script tag lives at the bottom of `<body>`. All function declarations in it are hoisted, so order of definition across tasks does not matter.

---

### Task 1: Git repo + HTML scaffold

**Files:**
- Create: `index.html`

- [ ] **Step 1: Initialise git repo**

```bash
cd /Users/fahimrayhan/Desktop/rejafiawedding
git init
```

- [ ] **Step 2: Create index.html**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Rejaul &amp; Afia</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,400;0,500;0,600;1,300;1,400;1,500;1,600&family=Inter:wght@300;400;500&display=swap" rel="stylesheet">
  <style>
    /* styles */
  </style>
</head>
<body>
  <!-- content -->
  <script>
    // scripts
  </script>
</body>
</html>
```

- [ ] **Step 3: Verify fonts load**

Open `index.html` in Chrome. DevTools → Network → confirm `fonts.googleapis.com` requests complete. No console errors.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "chore: initial scaffold with Google Fonts"
```

---

### Task 2: CSS foundation

**Files:**
- Modify: `index.html` — `<style>` block

- [ ] **Step 1: Replace `/* styles */` with the full CSS foundation**

```css
:root {
  --ivory: #faf6ee;
  --parchment: #f5ede0;
  --gold: #b8913a;
  --gold-light: rgba(184, 145, 58, 0.15);
  --gold-mid: rgba(184, 145, 58, 0.4);
  --ink: #1c140a;
  --ink-light: rgba(28, 20, 10, 0.6);
  --font-display: 'Cormorant Garamond', Georgia, serif;
  --font-ui: 'Inter', system-ui, sans-serif;
  --ease: cubic-bezier(0.4, 0, 0.2, 1);
}

*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

html { scroll-behavior: smooth; }

body {
  background: var(--ivory);
  color: var(--ink);
  font-family: var(--font-ui);
  overflow-x: hidden;
  -webkit-font-smoothing: antialiased;
}

img, video { display: block; max-width: 100%; }
a { text-decoration: none; color: inherit; }

/* Gold ornamental divider */
.gold-divider {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 1rem;
  margin: 2.5rem auto;
  max-width: 400px;
}
.gold-divider svg { flex: 1; overflow: visible; }
.gold-divider__diamond {
  width: 8px; height: 8px;
  background: var(--gold);
  transform: rotate(45deg);
  flex-shrink: 0;
}

/* Scroll-reveal: added by IntersectionObserver */
.fade-up {
  opacity: 0;
  transform: translateY(28px);
  transition: opacity 0.85s var(--ease), transform 0.85s var(--ease);
}
.fade-up.visible { opacity: 1; transform: translateY(0); }

/* Gold line draw-in */
.gold-line {
  stroke-dasharray: 200;
  stroke-dashoffset: 200;
  transition: stroke-dashoffset 1.1s var(--ease);
}
.gold-line.drawn { stroke-dashoffset: 0; }

/* Shared button base */
.btn {
  font-family: var(--font-ui);
  font-size: 0.72rem;
  letter-spacing: 0.15em;
  text-transform: uppercase;
  padding: 0.9rem 2rem;
  cursor: pointer;
  border-radius: 0;
  transition: all 0.3s var(--ease);
  display: inline-block;
}
.btn--outline {
  border: 1px solid var(--gold);
  color: var(--gold);
  background: transparent;
}
.btn--outline:hover { background: var(--gold); color: var(--ivory); }
.btn--filled {
  background: var(--gold);
  color: var(--ivory);
  border: 1px solid var(--gold);
}
.btn--filled:hover { background: transparent; color: var(--gold); }
```

- [ ] **Step 2: Verify in browser**

Add `<p style="padding:2rem;font-family:'Cormorant Garamond',serif;font-size:2rem;color:#b8913a">Test</p>` inside `<body>`. Open — Cormorant Garamond should render, ivory background. Remove the test paragraph.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: CSS foundation — variables, reset, divider, fade-up, gold line"
```

---

### Task 3: MEDIA_DATA placeholder array

**Files:**
- Modify: `index.html` — `<script>` block

- [ ] **Step 1: Replace `// scripts` with the MEDIA_DATA array**

```js
// ─── DATA ─────────────────────────────────────────────────────────────────────
// Replace placeholder URLs with real Cloudflare R2 URLs before going live.
// src: full-resolution original (used in lightbox + download)
// thumbnail: compressed ~800px version (used in grid cards for fast loading)
const MEDIA_DATA = [
  // Sahid — photos
  { creator: 'sahid', type: 'photo', src: 'https://picsum.photos/seed/sa01/2400/1600', thumbnail: 'https://picsum.photos/seed/sa01/800/533', caption: 'The ceremony' },
  { creator: 'sahid', type: 'photo', src: 'https://picsum.photos/seed/sa02/1600/2400', thumbnail: 'https://picsum.photos/seed/sa02/533/800', caption: 'First look' },
  { creator: 'sahid', type: 'photo', src: 'https://picsum.photos/seed/sa03/2400/1600', thumbnail: 'https://picsum.photos/seed/sa03/800/533', caption: 'With family' },
  { creator: 'sahid', type: 'photo', src: 'https://picsum.photos/seed/sa04/1600/2400', thumbnail: 'https://picsum.photos/seed/sa04/533/800', caption: 'The rings' },
  { creator: 'sahid', type: 'photo', src: 'https://picsum.photos/seed/sa05/2400/1600', thumbnail: 'https://picsum.photos/seed/sa05/800/533', caption: 'Candid moments' },
  { creator: 'sahid', type: 'photo', src: 'https://picsum.photos/seed/sa06/1600/2400', thumbnail: 'https://picsum.photos/seed/sa06/533/800', caption: 'The vows' },
  { creator: 'sahid', type: 'photo', src: 'https://picsum.photos/seed/sa07/2400/1600', thumbnail: 'https://picsum.photos/seed/sa07/800/533', caption: 'Evening light' },
  { creator: 'sahid', type: 'photo', src: 'https://picsum.photos/seed/sa08/1600/2400', thumbnail: 'https://picsum.photos/seed/sa08/533/800', caption: 'Together' },
  // Sahid — videos
  { creator: 'sahid', type: 'video', src: 'https://www.w3schools.com/html/mov_bbb.mp4', thumbnail: 'https://picsum.photos/seed/sv01/800/450', caption: 'Ceremony walk' },
  // Fahim — photos
  { creator: 'fahim', type: 'photo', src: 'https://picsum.photos/seed/fa01/2400/1600', thumbnail: 'https://picsum.photos/seed/fa01/800/533', caption: 'Reception entrance' },
  { creator: 'fahim', type: 'photo', src: 'https://picsum.photos/seed/fa02/1600/2400', thumbnail: 'https://picsum.photos/seed/fa02/533/800', caption: 'First dance' },
  { creator: 'fahim', type: 'photo', src: 'https://picsum.photos/seed/fa03/2400/1600', thumbnail: 'https://picsum.photos/seed/fa03/800/533', caption: 'Table details' },
  // Fahim — videos
  { creator: 'fahim', type: 'video', src: 'https://www.w3schools.com/html/mov_bbb.mp4', thumbnail: 'https://picsum.photos/seed/fv01/800/450', caption: 'Wedding highlights' },
  { creator: 'fahim', type: 'video', src: 'https://www.w3schools.com/html/mov_bbb.mp4', thumbnail: 'https://picsum.photos/seed/fv02/800/450', caption: 'First dance' },
  { creator: 'fahim', type: 'video', src: 'https://www.w3schools.com/html/mov_bbb.mp4', thumbnail: 'https://picsum.photos/seed/fv03/800/450', caption: 'Speeches' },
]

const sahidPhotos = MEDIA_DATA.filter(m => m.creator === 'sahid' && m.type === 'photo')
const sahidVideos = MEDIA_DATA.filter(m => m.creator === 'sahid' && m.type === 'video')
const fahimPhotos = MEDIA_DATA.filter(m => m.creator === 'fahim' && m.type === 'photo')
const fahimVideos = MEDIA_DATA.filter(m => m.creator === 'fahim' && m.type === 'video')
const allPhotos   = MEDIA_DATA.filter(m => m.type === 'photo')
const allVideos   = MEDIA_DATA.filter(m => m.type === 'video')
```

- [ ] **Step 2: Verify in console**

Open `index.html`. In DevTools console:
```js
console.log(MEDIA_DATA.length, sahidPhotos.length, fahimVideos.length)
// Expected: 15 8 3
```

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: MEDIA_DATA placeholder array with derived filter helpers"
```

---

### Task 4: Hero section — HTML and CSS

**Files:**
- Modify: `index.html` — `<body>` and `<style>`

- [ ] **Step 1: Add hero HTML inside `<body>` before `<script>`**

```html
<!-- NAV placeholder — filled in Task 8 -->
<nav id="nav" class="nav"></nav>

<!-- ─── HERO ─────────────────────────────────────────────────────────────── -->
<section id="hero">
  <div class="hero-media-bg" id="heroMediaBg"></div>
  <div class="hero-overlay"></div>

  <svg class="hero-arch" viewBox="0 0 600 800" fill="none" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
    <path d="M100 800 L100 350 Q100 50 300 50 Q500 50 500 350 L500 800" stroke="#b8913a" stroke-width="1.5" fill="none" opacity="0.22"/>
    <path d="M130 800 L130 360 Q130 90 300 90 Q470 90 470 360 L470 800" stroke="#b8913a" stroke-width="0.75" fill="none" opacity="0.12"/>
    <circle cx="300" cy="60" r="10" stroke="#b8913a" stroke-width="1" fill="none" opacity="0.25"/>
    <circle cx="300" cy="60" r="3" fill="#b8913a" opacity="0.25"/>
    <line x1="270" y1="60" x2="240" y2="60" stroke="#b8913a" stroke-width="0.75" opacity="0.2"/>
    <line x1="330" y1="60" x2="360" y2="60" stroke="#b8913a" stroke-width="0.75" opacity="0.2"/>
  </svg>

  <svg class="hero-corner hero-corner--tl" viewBox="0 0 80 80" aria-hidden="true">
    <path d="M5 45 Q5 5 45 5" stroke="#b8913a" stroke-width="1.2" fill="none"/>
    <path d="M5 60 Q5 5 60 5" stroke="#b8913a" stroke-width="0.5" fill="none" opacity="0.45"/>
    <circle cx="5" cy="5" r="2.5" fill="#b8913a" opacity="0.55"/>
  </svg>
  <svg class="hero-corner hero-corner--tr" viewBox="0 0 80 80" aria-hidden="true">
    <path d="M75 45 Q75 5 35 5" stroke="#b8913a" stroke-width="1.2" fill="none"/>
    <path d="M75 60 Q75 5 20 5" stroke="#b8913a" stroke-width="0.5" fill="none" opacity="0.45"/>
    <circle cx="75" cy="5" r="2.5" fill="#b8913a" opacity="0.55"/>
  </svg>
  <svg class="hero-corner hero-corner--bl" viewBox="0 0 80 80" aria-hidden="true">
    <path d="M5 35 Q5 75 45 75" stroke="#b8913a" stroke-width="1.2" fill="none"/>
    <path d="M5 20 Q5 75 60 75" stroke="#b8913a" stroke-width="0.5" fill="none" opacity="0.45"/>
    <circle cx="5" cy="75" r="2.5" fill="#b8913a" opacity="0.55"/>
  </svg>
  <svg class="hero-corner hero-corner--br" viewBox="0 0 80 80" aria-hidden="true">
    <path d="M75 35 Q75 75 35 75" stroke="#b8913a" stroke-width="1.2" fill="none"/>
    <path d="M75 20 Q75 75 20 75" stroke="#b8913a" stroke-width="0.5" fill="none" opacity="0.45"/>
    <circle cx="75" cy="75" r="2.5" fill="#b8913a" opacity="0.55"/>
  </svg>

  <canvas id="particles"></canvas>

  <div class="hero-content" id="heroContent">
    <div class="hero-glow"></div>
    <h1 class="hero-title">
      <span class="hero-name hero-anim" style="--d:0.1s">Rejaul</span>
      <span class="hero-amp hero-anim" style="--d:0.35s">&amp;</span>
      <span class="hero-name hero-anim" style="--d:0.6s">Afia</span>
    </h1>
    <p class="hero-date hero-anim" style="--d:0.85s">3rd May 2026</p>
    <div class="hero-ctas hero-anim" style="--d:1.1s">
      <a href="#gallery" class="btn btn--outline">View Gallery</a>
      <a href="#download" class="btn btn--filled">Download All</a>
    </div>
  </div>
</section>
```

- [ ] **Step 2: Append hero CSS to `<style>`**

```css
/* ─── HERO ──────────────────────────────────────────────────────────────── */
#hero {
  position: relative;
  width: 100%;
  height: 100vh;
  min-height: 600px;
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
  background: var(--ivory);
}

.hero-media-bg {
  position: absolute;
  inset: 0;
  z-index: 0;
}
.hero-media-bg img,
.hero-media-bg video {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: opacity 1.4s ease;
}

.hero-overlay {
  position: absolute;
  inset: 0;
  background: rgba(250, 246, 238, 0.65);
  z-index: 1;
}

.hero-arch {
  position: absolute;
  width: min(72vh, 560px);
  height: auto;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 2;
  pointer-events: none;
}

.hero-corner {
  position: absolute;
  width: 80px;
  height: 80px;
  z-index: 3;
  pointer-events: none;
}
.hero-corner--tl { top: 1.5rem; left: 1.5rem; }
.hero-corner--tr { top: 1.5rem; right: 1.5rem; }
.hero-corner--bl { bottom: 1.5rem; left: 1.5rem; }
.hero-corner--br { bottom: 1.5rem; right: 1.5rem; }

#particles {
  position: absolute;
  inset: 0;
  z-index: 3;
  pointer-events: none;
}

.hero-content {
  position: relative;
  z-index: 4;
  text-align: center;
  padding: 2rem;
  will-change: transform;
}

.hero-glow {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 700px;
  height: 500px;
  transform: translate(-50%, -50%);
  background: radial-gradient(ellipse, rgba(184,145,58,0.1) 0%, transparent 65%);
  pointer-events: none;
}

.hero-title {
  font-family: var(--font-display);
  font-weight: 300;
  font-size: clamp(3.5rem, 10vw, 8rem);
  line-height: 1;
  letter-spacing: 0.04em;
  color: var(--ink);
  display: flex;
  align-items: baseline;
  justify-content: center;
  gap: 0.3em;
  flex-wrap: wrap;
}

.hero-amp {
  font-style: italic;
  color: var(--gold);
  font-size: 1.1em;
  line-height: 1;
}

.hero-date {
  font-family: var(--font-ui);
  font-size: 0.72rem;
  letter-spacing: 0.28em;
  text-transform: uppercase;
  color: var(--ink-light);
  margin-top: 1.75rem;
}

.hero-ctas {
  display: flex;
  gap: 1rem;
  justify-content: center;
  margin-top: 2.5rem;
  flex-wrap: wrap;
}

/* Staggered load animation */
.hero-anim {
  opacity: 0;
  transform: translateY(22px);
  animation: heroIn 1s var(--ease) var(--d, 0s) forwards;
}
@keyframes heroIn {
  to { opacity: 1; transform: translateY(0); }
}
```

- [ ] **Step 3: Verify in browser**

Open `index.html`. Confirm:
- Full-viewport hero on ivory background
- "Rejaul & Afia" fades in staggered — names first, then gold italic ampersand, then date, then CTAs
- Corner SVG flourishes in all four corners
- Arch SVG centred behind text at low opacity
- Both buttons render with correct colours; hover states invert them
- No console errors

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: hero section — layout, typography, SVG arch, corner flourishes, load animations"
```

---

### Task 5: Hero particle canvas

**Files:**
- Modify: `index.html` — `<script>` block

- [ ] **Step 1: Append particle system after the MEDIA_DATA block**

```js
// ─── PARTICLES ────────────────────────────────────────────────────────────────
;(function initParticles() {
  const canvas = document.getElementById('particles')
  const ctx = canvas.getContext('2d')
  let W, H, particles

  function resize() {
    W = canvas.width = canvas.offsetWidth
    H = canvas.height = canvas.offsetHeight
  }

  function make() {
    return {
      x: Math.random() * W,
      y: H + Math.random() * 60,
      r: Math.random() * 1.8 + 0.4,
      speed: Math.random() * 0.55 + 0.18,
      opacity: Math.random() * 0.45 + 0.08,
      drift: (Math.random() - 0.5) * 0.25,
    }
  }

  function init() {
    resize()
    particles = Array.from({ length: 55 }, make)
    particles.forEach(p => { p.y = Math.random() * H })
  }

  function draw() {
    ctx.clearRect(0, 0, W, H)
    particles.forEach(p => {
      ctx.beginPath()
      ctx.arc(p.x, p.y, p.r, 0, Math.PI * 2)
      ctx.fillStyle = `rgba(184,145,58,${p.opacity})`
      ctx.fill()
      p.y -= p.speed
      p.x += p.drift
      p.opacity -= 0.0007
      if (p.y < -10 || p.opacity <= 0) Object.assign(p, make())
    })
    requestAnimationFrame(draw)
  }

  window.addEventListener('resize', resize)
  init()
  draw()
})()
```

- [ ] **Step 2: Verify in browser**

Reload. Subtle gold motes should rise slowly through the hero. They should feel ambient — if they're distracting, the opacity values are too high.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: hero gold particle canvas"
```

---

### Task 6: Hero background media slideshow

**Files:**
- Modify: `index.html` — `<script>` block

- [ ] **Step 1: Append hero slideshow after particle init**

```js
// ─── HERO MEDIA SLIDESHOW ─────────────────────────────────────────────────────
;(function initHeroSlideshow() {
  const container = document.getElementById('heroMediaBg')
  if (!MEDIA_DATA.length) return

  const heroItems = MEDIA_DATA.slice(0, 8)
  let current = 0
  let currentEl = null

  const kenBurns = [
    { start: 'scale(1.08) translate(2%, 1%)',   end: 'scale(1) translate(0%, 0%)' },
    { start: 'scale(1.06) translate(-2%, -1%)', end: 'scale(1) translate(0%, 0%)' },
    { start: 'scale(1.07) translate(1%, -2%)',  end: 'scale(1) translate(0%, 0%)' },
  ]

  function createPhotoEl(item) {
    const img = document.createElement('img')
    img.src = item.src
    img.alt = ''
    const kb = kenBurns[current % kenBurns.length]
    img.style.cssText = `opacity:0; transform:${kb.start}; transition:opacity 1.4s ease, transform 6s ease;`
    setTimeout(() => {
      img.style.opacity = '1'
      img.style.transform = kb.end
    }, 30)
    return img
  }

  function createVideoEl(item) {
    const vid = document.createElement('video')
    vid.src = item.src
    vid.muted = vid.loop = vid.playsInline = true
    vid.style.cssText = 'opacity:0; transition:opacity 1.4s ease;'
    setTimeout(() => { vid.style.opacity = '1'; vid.play().catch(() => {}) }, 30)
    return vid
  }

  function next() {
    const item = heroItems[current % heroItems.length]
    const el = item.type === 'video' ? createVideoEl(item) : createPhotoEl(item)
    container.appendChild(el)

    if (currentEl) {
      const prev = currentEl
      prev.style.opacity = '0'
      setTimeout(() => prev.remove(), 1500)
    }
    currentEl = el
    current++
  }

  next()
  setInterval(next, 5000)
})()
```

- [ ] **Step 2: Verify in browser**

Reload. Background images should appear behind the ivory overlay and text, crossfading every 5 seconds. Ken Burns zoom/pan should be visible but subtle. Text must stay fully readable.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: hero background media slideshow — Ken Burns, crossfade"
```

---

### Task 7: Cursor glow + hero scroll parallax

**Files:**
- Modify: `index.html` — `<body>`, `<style>`, `<script>`

- [ ] **Step 1: Add cursor glow element before `</body>`**

```html
<div id="cursorGlow" aria-hidden="true"></div>
```

- [ ] **Step 2: Append CSS**

```css
/* ─── CURSOR GLOW ───────────────────────────────────────────────────────── */
#cursorGlow {
  position: fixed;
  width: 320px;
  height: 320px;
  border-radius: 50%;
  background: radial-gradient(circle, rgba(184,145,58,0.07) 0%, transparent 70%);
  pointer-events: none;
  transform: translate(-50%, -50%);
  z-index: 9999;
  transition: opacity 0.3s;
}
@media (hover: none) { #cursorGlow { display: none; } }
```

- [ ] **Step 3: Append cursor glow + parallax JS**

```js
// ─── CURSOR GLOW ──────────────────────────────────────────────────────────────
;(function() {
  const glow = document.getElementById('cursorGlow')
  document.addEventListener('mousemove', e => {
    glow.style.left = e.clientX + 'px'
    glow.style.top  = e.clientY + 'px'
  })
})()

// ─── HERO PARALLAX ────────────────────────────────────────────────────────────
;(function() {
  const content = document.getElementById('heroContent')
  const arch    = document.querySelector('.hero-arch')
  window.addEventListener('scroll', () => {
    const y = window.scrollY
    if (y > window.innerHeight) return
    content.style.transform = `translateY(${y * 0.28}px)`
    arch.style.transform    = `translate(-50%, calc(-50% + ${y * 0.14}px))`
  }, { passive: true })
})()
```

- [ ] **Step 4: Verify in browser**

Move mouse across the page — faint gold glow follows. Scroll down from hero — content drifts up at a slower rate than the page, arch shifts slightly. On mobile (or DevTools mobile emulation) the glow div should not render.

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: cursor glow and hero parallax on scroll"
```

---

### Task 8: Sticky navigation

**Files:**
- Modify: `index.html` — `<body>` (fill nav placeholder), `<style>`, `<script>`

- [ ] **Step 1: Replace `<nav id="nav" class="nav"></nav>` with full nav**

```html
<nav id="nav" class="nav" aria-label="Site navigation">
  <a href="#hero" class="nav-logo">R &amp; A</a>
  <ul class="nav-links">
    <li><a href="#gallery">Gallery</a></li>
    <li><a href="#download">Download</a></li>
  </ul>
</nav>
```

- [ ] **Step 2: Append nav CSS**

```css
/* ─── NAV ───────────────────────────────────────────────────────────────── */
.nav {
  position: fixed;
  top: 0; left: 0; right: 0;
  z-index: 100;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 1rem 2.5rem;
  background: rgba(250, 246, 238, 0.82);
  backdrop-filter: blur(14px);
  -webkit-backdrop-filter: blur(14px);
  border-bottom: 1px solid rgba(184, 145, 58, 0.14);
  transform: translateY(-100%);
  transition: transform 0.5s var(--ease);
}
.nav.visible { transform: translateY(0); }

.nav-logo {
  font-family: var(--font-display);
  font-style: italic;
  font-size: 1.4rem;
  letter-spacing: 0.06em;
  color: var(--ink);
}

.nav-links {
  list-style: none;
  display: flex;
  gap: 2.5rem;
}
.nav-links a {
  font-family: var(--font-ui);
  font-size: 0.68rem;
  letter-spacing: 0.22em;
  text-transform: uppercase;
  color: var(--ink-light);
  transition: color 0.3s;
}
.nav-links a:hover { color: var(--gold); }
```

- [ ] **Step 3: Append nav JS**

```js
// ─── NAV ──────────────────────────────────────────────────────────────────────
;(function() {
  const nav  = document.getElementById('nav')
  const hero = document.getElementById('hero')
  new IntersectionObserver(([e]) => nav.classList.toggle('visible', !e.isIntersecting), { threshold: 0.1 }).observe(hero)
})()
```

- [ ] **Step 4: Verify in browser**

Scroll past hero — nav slides in from top, frosted glass visible. Scroll back — nav slides away. "Gallery" and "Download" links scroll to their anchors (sections don't exist yet; no error should occur).

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: sticky frosted-glass nav with IntersectionObserver trigger"
```

---

### Task 9: Gallery section structure + parallax interludes

**Files:**
- Modify: `index.html` — `<body>`, `<style>`, `<script>`

- [ ] **Step 1: Add gallery + interludes HTML after `</section>` (hero close tag)**

```html
<!-- ─── PARALLAX INTERLUDE 1 ───────────────────────────────────────────── -->
<div class="parallax-interlude" id="interlude1"></div>

<!-- ─── GALLERY ─────────────────────────────────────────────────────────── -->
<section id="gallery">
  <div id="galleryContent"></div>
</section>

<!-- ─── PARALLAX INTERLUDE 2 ───────────────────────────────────────────── -->
<div class="parallax-interlude" id="interlude2"></div>
```

- [ ] **Step 2: Append CSS**

```css
/* ─── GALLERY ───────────────────────────────────────────────────────────── */
#gallery { background: var(--ivory); padding: 5rem 0; }

.creator-block {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 2rem 5rem;
}

.creator-heading {
  font-family: var(--font-display);
  font-style: italic;
  font-weight: 400;
  font-size: clamp(1.8rem, 4vw, 3rem);
  color: var(--ink);
  text-align: center;
  letter-spacing: 0.04em;
  margin-bottom: 0.25rem;
}

.media-label {
  font-family: var(--font-ui);
  font-size: 0.62rem;
  letter-spacing: 0.28em;
  text-transform: uppercase;
  color: var(--gold);
  text-align: center;
  margin: 2.75rem 0 1.25rem;
}

/* ─── PARALLAX INTERLUDES ───────────────────────────────────────────────── */
.parallax-interlude {
  height: 65vh;
  background-attachment: fixed;
  background-size: cover;
  background-position: center;
  position: relative;
}
.parallax-interlude::after {
  content: '';
  position: absolute;
  inset: 0;
  background: rgba(250, 246, 238, 0.32);
}
@media (max-width: 768px) {
  /* background-attachment:fixed is broken on iOS Safari */
  .parallax-interlude { background-attachment: scroll; }
}
```

- [ ] **Step 3: Append gallery render + interlude init JS**

```js
// ─── GALLERY RENDER ───────────────────────────────────────────────────────────
function goldDividerHTML() {
  return `<div class="gold-divider">
    <svg height="1" viewBox="0 0 200 1"><line x1="0" y1="0.5" x2="200" y2="0.5" stroke="#b8913a" stroke-width="1" class="gold-line"/></svg>
    <div class="gold-divider__diamond"></div>
    <svg height="1" viewBox="0 0 200 1"><line x1="0" y1="0.5" x2="200" y2="0.5" stroke="#b8913a" stroke-width="1" class="gold-line"/></svg>
  </div>`
}

function renderGallery() {
  const container = document.getElementById('galleryContent')
  const creators = [
    { label: 'Sahid',  photos: sahidPhotos, videos: sahidVideos },
    { label: 'Fahim',  photos: fahimPhotos, videos: fahimVideos },
  ]
  container.innerHTML = creators.map((c, i) => `
    <div class="creator-block fade-up">
      <h2 class="creator-heading">${c.label}</h2>
      ${goldDividerHTML()}
      ${c.photos.length ? `<p class="media-label">Photos</p><div class="photo-grid">${c.photos.map(photoCardHTML).join('')}</div>` : ''}
      ${c.videos.length ? `<p class="media-label">Videos</p><div class="video-grid">${c.videos.map(videoCardHTML).join('')}</div>` : ''}
    </div>
    ${i === 0 ? `<div style="max-width:1200px;margin:0 auto;padding:0 2rem">${goldDividerHTML()}</div>` : ''}
  `).join('')
}

function initInterludes() {
  const i1 = document.getElementById('interlude1')
  const i2 = document.getElementById('interlude2')
  if (i1 && allPhotos[2]) i1.style.backgroundImage = `url(${allPhotos[2].src})`
  if (i2 && allPhotos[5]) i2.style.backgroundImage = `url(${allPhotos[5].src})`
}
```

- [ ] **Step 4: Verify in browser**

The gallery section and parallax interludes exist in the DOM. The interludes should show as tall panels (if `allPhotos[2]` has loaded its picsum URL they'll show images). Gallery container is empty — `renderGallery()` isn't called yet (next tasks). No console errors.

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: gallery section structure, parallax interlude panels"
```

---

### Task 10: Photo masonry grid

**Files:**
- Modify: `index.html` — `<style>` and `<script>`

- [ ] **Step 1: Append photo grid CSS**

```css
/* ─── PHOTO GRID ────────────────────────────────────────────────────────── */
.photo-grid { column-count: 3; column-gap: 0.875rem; }
@media (max-width: 900px) { .photo-grid { column-count: 2; } }
@media (max-width: 500px) { .photo-grid { column-count: 1; } }

.photo-card {
  break-inside: avoid;
  position: relative;
  margin-bottom: 0.875rem;
  cursor: pointer;
  overflow: hidden;
  border-radius: 2px;
}

.photo-card img {
  width: 100%;
  height: auto;
  display: block;
  transition: transform 0.65s var(--ease);
  opacity: 0;
  transition: opacity 0.5s ease, transform 0.65s var(--ease);
}
.photo-card img.loaded { opacity: 1; }
.photo-card:hover img { transform: scale(1.04); }

.photo-card__overlay {
  position: absolute;
  inset: 0;
  background: linear-gradient(to top, rgba(28,20,10,0.72) 0%, transparent 55%);
  opacity: 0;
  transition: opacity 0.4s var(--ease);
  display: flex;
  flex-direction: column;
  justify-content: flex-end;
  padding: 0.875rem 0.875rem 0.75rem;
}
.photo-card:hover .photo-card__overlay { opacity: 1; }

.photo-card__caption {
  font-family: var(--font-display);
  font-style: italic;
  font-size: 0.95rem;
  color: rgba(250,246,238,0.9);
  margin-bottom: 0.5rem;
  line-height: 1.3;
}

.card-download {
  align-self: flex-end;
  width: 34px; height: 34px;
  border-radius: 50%;
  background: rgba(184,145,58,0.88);
  border: none;
  display: flex; align-items: center; justify-content: center;
  color: white;
  cursor: pointer;
  transition: background 0.3s;
  text-decoration: none;
  flex-shrink: 0;
}
.card-download:hover { background: var(--gold); }
.card-download svg { width: 14px; height: 14px; }
```

- [ ] **Step 2: Append `photoCardHTML` function before `renderGallery`**

```js
function photoCardHTML(item, idx) {
  return `<div class="photo-card fade-up" style="--d:${idx * 0.055}s" data-src="${item.src}" data-type="photo">
    <img src="${item.thumbnail}" alt="${item.caption}" loading="lazy" onload="this.classList.add('loaded')">
    <div class="photo-card__overlay">
      <p class="photo-card__caption">${item.caption}</p>
      <a href="${item.src}" download="${item.caption.replace(/\s+/g,'-').toLowerCase()}" class="card-download" onclick="event.stopPropagation()" title="Download photo">
        <svg viewBox="0 0 16 16" fill="none" stroke="currentColor" stroke-width="2"><path d="M8 2v8M5 7l3 3 3-3M2 12v2h12v-2"/></svg>
      </a>
    </div>
  </div>`
}
```

- [ ] **Step 3: Verify in browser**

Temporarily call `renderGallery()` from the console. Photo masonry grid should appear. Confirm 3-column layout, hover overlays work (gradient + italic caption + download button). Download icon triggers file download. Confirm no console errors. Reload to reset.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: photo masonry grid with hover overlay and download"
```

---

### Task 11: Video grid, gallery init, image loading

**Files:**
- Modify: `index.html` — `<style>` and `<script>`

- [ ] **Step 1: Append video grid CSS**

```css
/* ─── VIDEO GRID ────────────────────────────────────────────────────────── */
.video-grid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 0.875rem; }
@media (max-width: 580px) { .video-grid { grid-template-columns: 1fr; } }

.video-card {
  position: relative;
  cursor: pointer;
  overflow: hidden;
  border-radius: 2px;
  aspect-ratio: 16 / 9;
  background: var(--parchment);
}

.video-card img {
  width: 100%; height: 100%;
  object-fit: cover;
  transition: transform 0.65s var(--ease), opacity 0.5s ease;
  opacity: 0;
}
.video-card img.loaded { opacity: 1; }
.video-card:hover img { transform: scale(1.04); }

.video-card__overlay {
  position: absolute;
  inset: 0;
  background: linear-gradient(to top, rgba(28,20,10,0.65) 0%, rgba(28,20,10,0.1) 100%);
  display: flex;
  align-items: center;
  justify-content: center;
  transition: background 0.4s;
}
.video-card:hover .video-card__overlay {
  background: linear-gradient(to top, rgba(28,20,10,0.78) 0%, rgba(28,20,10,0.18) 100%);
}

.video-card__play {
  width: 60px; height: 60px;
  border-radius: 50%;
  background: rgba(250,246,238,0.9);
  display: flex; align-items: center; justify-content: center;
  transition: transform 0.4s var(--ease), background 0.3s;
}
.video-card:hover .video-card__play { transform: scale(1.14); background: white; }
.video-card__play svg { width: 22px; height: 22px; margin-left: 3px; fill: var(--ink); }

.video-card__caption {
  position: absolute;
  bottom: 0.75rem; left: 0.75rem; right: 3rem;
  font-family: var(--font-display);
  font-style: italic;
  font-size: 0.92rem;
  color: rgba(250,246,238,0.88);
  opacity: 0;
  transform: translateY(5px);
  transition: opacity 0.4s, transform 0.4s;
}
.video-card:hover .video-card__caption { opacity: 1; transform: translateY(0); }

.video-card__dl {
  position: absolute;
  bottom: 0.6rem; right: 0.65rem;
  opacity: 0;
  transition: opacity 0.4s;
}
.video-card:hover .video-card__dl { opacity: 1; }
```

- [ ] **Step 2: Append `videoCardHTML` function before `renderGallery`**

```js
function videoCardHTML(item, idx) {
  return `<div class="video-card fade-up" style="--d:${idx * 0.055}s" data-src="${item.src}" data-type="video">
    <img src="${item.thumbnail}" alt="${item.caption}" loading="lazy" onload="this.classList.add('loaded')">
    <div class="video-card__overlay">
      <div class="video-card__play">
        <svg viewBox="0 0 24 24"><path d="M8 5v14l11-7z"/></svg>
      </div>
    </div>
    <p class="video-card__caption">${item.caption}</p>
    <a href="${item.src}" download="${item.caption.replace(/\s+/g,'-').toLowerCase()}" class="card-download video-card__dl" onclick="event.stopPropagation()" title="Download video">
      <svg viewBox="0 0 16 16" fill="none" stroke="currentColor" stroke-width="2"><path d="M8 2v8M5 7l3 3 3-3M2 12v2h12v-2"/></svg>
    </a>
  </div>`
}
```

- [ ] **Step 3: Append DOMContentLoaded init at end of script**

```js
// ─── INIT ─────────────────────────────────────────────────────────────────────
document.addEventListener('DOMContentLoaded', () => {
  renderGallery()
  initInterludes()
})
```

- [ ] **Step 4: Verify in browser**

Reload. Full gallery renders — Sahid first (8 photos in 3-col masonry + 1 video), then gold divider, then Fahim (3 photos + 3 videos in 2-col grid). Images fade in as they load. Hover states on both card types work. No console errors.

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: video grid, gallery init, image fade-in on load"
```

---

### Task 12: Photo and video lightbox

**Files:**
- Modify: `index.html` — `<body>`, `<style>`, `<script>`

- [ ] **Step 1: Add lightbox HTML before `<div id="cursorGlow">`**

```html
<!-- ─── LIGHTBOX ────────────────────────────────────────────────────────── -->
<div id="lightbox" class="lightbox" role="dialog" aria-modal="true" aria-label="Media viewer">
  <button class="lightbox__close" id="lbClose" aria-label="Close">&times;</button>
  <div class="lightbox__inner" id="lbInner"></div>
</div>
```

- [ ] **Step 2: Append lightbox CSS**

```css
/* ─── LIGHTBOX ──────────────────────────────────────────────────────────── */
.lightbox {
  position: fixed;
  inset: 0;
  background: rgba(28, 20, 10, 0.93);
  z-index: 1000;
  display: flex;
  align-items: center;
  justify-content: center;
  opacity: 0;
  pointer-events: none;
  transition: opacity 0.35s var(--ease);
}
.lightbox.open { opacity: 1; pointer-events: all; }

.lightbox__inner {
  transform: scale(0.87);
  opacity: 0;
  transition: transform 0.4s var(--ease), opacity 0.4s var(--ease);
}
.lightbox.open .lightbox__inner { transform: scale(1); opacity: 1; }

.lightbox__inner img {
  max-width: 95vw;
  max-height: 90vh;
  width: auto; height: auto;
  object-fit: contain;
  display: block;
}

.lightbox__inner video {
  max-width: 95vw;
  max-height: 90vh;
  width: auto; height: auto;
  display: block;
  background: #000;
}

.lightbox__close {
  position: absolute;
  top: 1.25rem; right: 1.5rem;
  background: none; border: none;
  color: rgba(250,246,238,0.6);
  font-size: 2.2rem;
  cursor: pointer;
  line-height: 1;
  transition: color 0.3s;
  z-index: 1001;
}
.lightbox__close:hover { color: var(--ivory); }
```

- [ ] **Step 3: Append lightbox JS before the INIT block**

```js
// ─── LIGHTBOX ─────────────────────────────────────────────────────────────────
;(function() {
  const lb     = document.getElementById('lightbox')
  const inner  = document.getElementById('lbInner')
  const closeB = document.getElementById('lbClose')

  function open(card) {
    inner.innerHTML = ''
    if (card.dataset.type === 'photo') {
      const img = document.createElement('img')
      img.src = card.dataset.src
      img.alt = ''
      inner.appendChild(img)
    } else {
      const vid = document.createElement('video')
      vid.src = card.dataset.src
      vid.controls = true
      vid.preload = 'metadata'
      vid.playsInline = true
      inner.appendChild(vid)
    }
    lb.classList.add('open')
    document.body.style.overflow = 'hidden'
  }

  function close() {
    lb.classList.remove('open')
    document.body.style.overflow = ''
    setTimeout(() => {
      const v = inner.querySelector('video')
      if (v) v.pause()
      inner.innerHTML = ''
    }, 400)
  }

  document.addEventListener('click', e => {
    const card = e.target.closest('[data-type]')
    if (!card) return
    if (e.target.closest('.card-download')) return
    open(card)
  })

  lb.addEventListener('click', e => { if (e.target === lb) close() })
  closeB.addEventListener('click', close)
  document.addEventListener('keydown', e => { if (e.key === 'Escape') close() })
})()
```

- [ ] **Step 4: Verify in browser**

Click a photo card → lightbox scales in showing full-resolution image. Click backdrop or × → closes. Press Escape → closes. Click a video card → lightbox opens with native `<video>` player (controls visible, fullscreen button present). Download buttons on cards do NOT open lightbox.

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: photo and video lightbox with scale-in, keyboard close"
```

---

### Task 13: Scroll animations + gold line draw-in

**Files:**
- Modify: `index.html` — `<script>`

- [ ] **Step 1: Append scroll animation JS before the INIT block**

```js
// ─── SCROLL ANIMATIONS ────────────────────────────────────────────────────────
function observeScrollTargets() {
  const fadeObs = new IntersectionObserver(entries => {
    entries.forEach(e => {
      if (!e.isIntersecting) return
      const delay = parseFloat(getComputedStyle(e.target).getPropertyValue('--d') || 0) * 1000
      setTimeout(() => e.target.classList.add('visible'), delay)
      fadeObs.unobserve(e.target)
    })
  }, { threshold: 0.08, rootMargin: '0px 0px -30px 0px' })

  const lineObs = new IntersectionObserver(entries => {
    entries.forEach(e => {
      if (!e.isIntersecting) return
      e.target.classList.add('drawn')
      lineObs.unobserve(e.target)
    })
  }, { threshold: 0.6 })

  document.querySelectorAll('.fade-up').forEach(el => fadeObs.observe(el))
  document.querySelectorAll('.gold-line').forEach(el => lineObs.observe(el))
}
```

- [ ] **Step 2: Call `observeScrollTargets` in the DOMContentLoaded init block**

Find the existing `DOMContentLoaded` block from Task 11 and update it:

```js
document.addEventListener('DOMContentLoaded', () => {
  renderGallery()
  initInterludes()
  observeScrollTargets()
})
```

- [ ] **Step 3: Verify in browser**

Reload and scroll slowly from top. Creator blocks and cards should fade up with staggered delays as they enter the viewport. Gold divider lines should draw left-to-right. Elements above the fold are immediately visible (IntersectionObserver fires on load for elements already in view).

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: IntersectionObserver scroll animations, staggered cards, gold line draw-in"
```

---

### Task 14: Download section + footer

**Files:**
- Modify: `index.html` — `<body>`, `<style>`, `<script>`

- [ ] **Step 1: Add download section + footer HTML after `#interlude2`**

```html
<!-- ─── DOWNLOAD ────────────────────────────────────────────────────────── -->
<section id="download">
  <div class="dl-glow"></div>
  <div class="dl-content fade-up">
    <h2 class="dl-heading">Take Everything<br><em>With You</em></h2>
    <p class="dl-sub">Every photo and video, yours to keep.</p>
    <div class="dl-btns">
      <button class="btn btn--dl" onclick="downloadAll('photo')">Photos</button>
      <button class="btn btn--dl" onclick="downloadAll('video')">Videos</button>
      <button class="btn btn--dl btn--dl-hero" onclick="downloadAll('all')">Everything</button>
    </div>
  </div>
</section>

<!-- ─── FOOTER ───────────────────────────────────────────────────────────── -->
<footer>
  <p class="footer-names">Rejaul <em>&amp;</em> Afia</p>
  <p class="footer-date">3rd May 2026</p>
</footer>
```

- [ ] **Step 2: Append CSS**

```css
/* ─── DOWNLOAD ──────────────────────────────────────────────────────────── */
#download {
  position: relative;
  background: var(--ink);
  padding: 9rem 2rem;
  text-align: center;
  overflow: hidden;
}

.dl-glow {
  position: absolute;
  top: 50%; left: 50%;
  transform: translate(-50%, -50%);
  width: 700px; height: 500px;
  background: radial-gradient(ellipse, rgba(184,145,58,0.13) 0%, transparent 68%);
  pointer-events: none;
}

.dl-heading {
  font-family: var(--font-display);
  font-weight: 300;
  font-size: clamp(2.5rem, 6vw, 5.5rem);
  line-height: 1.1;
  color: var(--ivory);
  margin-bottom: 1.25rem;
}
.dl-heading em { font-style: italic; color: var(--gold); }

.dl-sub {
  font-family: var(--font-ui);
  font-size: 0.82rem;
  letter-spacing: 0.12em;
  color: rgba(250,246,238,0.45);
  margin-bottom: 3.5rem;
}

.dl-btns { display: flex; gap: 1rem; justify-content: center; flex-wrap: wrap; }

.btn--dl {
  font-family: var(--font-ui);
  font-size: 0.68rem;
  letter-spacing: 0.22em;
  text-transform: uppercase;
  padding: 0.9rem 2.5rem;
  border: 1px solid rgba(184,145,58,0.4);
  color: rgba(250,246,238,0.6);
  background: transparent;
  cursor: pointer;
  transition: all 0.3s var(--ease);
  border-radius: 0;
}
.btn--dl:hover { border-color: var(--gold); color: var(--gold); }
.btn--dl-hero { border-color: var(--gold); color: var(--gold); }
.btn--dl-hero:hover { background: var(--gold); color: var(--ink); }

/* ─── FOOTER ────────────────────────────────────────────────────────────── */
footer {
  background: var(--ink);
  border-top: 1px solid rgba(184,145,58,0.12);
  padding: 3rem 2rem;
  text-align: center;
}

.footer-names {
  font-family: var(--font-display);
  font-style: italic;
  font-size: 1.75rem;
  color: rgba(250,246,238,0.75);
  letter-spacing: 0.06em;
}
.footer-names em { color: var(--gold); }

.footer-date {
  font-family: var(--font-ui);
  font-size: 0.62rem;
  letter-spacing: 0.28em;
  text-transform: uppercase;
  color: rgba(184,145,58,0.45);
  margin-top: 0.5rem;
}
```

- [ ] **Step 3: Append `downloadAll` JS before the INIT block**

```js
// ─── DOWNLOAD ─────────────────────────────────────────────────────────────────
function downloadAll(type) {
  const items = type === 'all' ? MEDIA_DATA
    : MEDIA_DATA.filter(m => m.type === type)
  items.forEach((item, i) => {
    setTimeout(() => {
      const a = document.createElement('a')
      a.href = item.src
      a.download = item.caption.replace(/\s+/g, '-').toLowerCase() + '-' + (i + 1)
      document.body.appendChild(a)
      a.click()
      document.body.removeChild(a)
    }, i * 180)
  })
}
```

- [ ] **Step 4: Verify in browser**

Scroll to bottom. Download section shows on dark ink background with gold glow and large heading. Click "Photos" — browser triggers a download per photo. Footer shows italic names and spaced-caps date. `fade-up` on `.dl-content` should animate in when scrolled into view.

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: download section with batch download, footer"
```

---

### Task 15: Mobile responsiveness + final polish

**Files:**
- Modify: `index.html` — `<style>`

- [ ] **Step 1: Append responsive and polish CSS**

```css
/* ─── RESPONSIVE ────────────────────────────────────────────────────────── */
@media (max-width: 768px) {
  .hero-title { gap: 0.2em; }
  .hero-corner { width: 52px; height: 52px; }
  .hero-arch { width: 88vw; }
  .creator-block { padding: 0 1rem 3rem; }
  .nav { padding: 0.875rem 1.25rem; }
  .dl-btns { flex-direction: column; align-items: center; }
  .btn--dl { width: 220px; }
}

@media (max-width: 480px) {
  .hero-ctas { flex-direction: column; align-items: center; }
  .btn { width: 200px; text-align: center; }
  .video-grid { grid-template-columns: 1fr; }
}

/* Prevent layout shift while fonts load */
h1, h2, h3, .nav-logo, .footer-names {
  font-display: swap;
}

/* Ensure no accidental quality degradation on media */
.lightbox__inner img,
.lightbox__inner video {
  image-rendering: auto;
}
```

- [ ] **Step 2: Full visual QA — work through this checklist in browser**

Open `index.html` at full desktop width (1440px). Check each item:

- [ ] Hero media slideshow cycling with Ken Burns — images visible behind ivory overlay
- [ ] Gold particles floating upward
- [ ] Heading stagger animation on fresh load
- [ ] CTAs hover correctly
- [ ] Corner flourishes and arch visible
- [ ] Nav slides in on scroll, disappears when back at hero
- [ ] Parallax interlude 1 appears (full-bleed image between hero and gallery)
- [ ] Gallery: Sahid first, Fahim second — correct order
- [ ] Photo masonry 3 columns, images fade in as they load
- [ ] Video grid 2 columns, poster frames load
- [ ] Hover on photo card: gradient overlay, italic caption, download button
- [ ] Hover on video card: play icon scales, caption appears, download button appears
- [ ] Click photo → lightbox opens full resolution, Escape closes
- [ ] Click video → lightbox opens with native player, controls visible
- [ ] Gold divider lines draw in when scrolled to
- [ ] Creator blocks and cards fade up on scroll
- [ ] Parallax interlude 2 appears between gallery and download
- [ ] Download section: dark bg, gold glow, all 3 buttons work
- [ ] Footer: italic names, spaced-caps date

Then resize to 375px (mobile) and confirm layout doesn't break.

- [ ] **Step 3: Fix any layout issues found in QA**

Address any issues discovered in Step 2 before committing.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: mobile responsive styles, final QA polish"
```

---

### Task 16: GitHub + Cloudflare Pages deployment

**Files:**
- No code changes

- [ ] **Step 1: Create GitHub repo**

Go to [github.com](https://github.com) → New repository → name: `rejafiawedding` → Private → No README → Create.

- [ ] **Step 2: Push to GitHub**

```bash
git remote add origin https://github.com/fahimrayhan/rejafiawedding.git
git branch -M main
git push -u origin main
```

- [ ] **Step 3: Create Cloudflare Pages project**

1. [dash.cloudflare.com](https://dash.cloudflare.com) → Pages → Create a project
2. Connect to Git → Authorise GitHub → Select `rejafiawedding`
3. Build settings:
   - Framework preset: `None`
   - Build command: *(leave empty)*
   - Build output directory: `/`
4. Click "Save and Deploy"

- [ ] **Step 4: Confirm live URL**

Cloudflare assigns `rejafiawedding.pages.dev` (or you can set a custom Pages subdomain in settings → Custom domains). Open in incognito. Confirm full site loads, fonts render, images appear.

- [ ] **Step 5: When real media is ready — set up Cloudflare R2**

```
1. Cloudflare → R2 → Create bucket: "rejafia-wedding"
2. Settings → Public access → Allow
3. Upload structure:
     sahid/photos/   ← Sahid's full-res JPEGs
     sahid/thumbs/   ← Sahid's thumbnails (~800px wide, exported separately)
     sahid/videos/   ← Sahid's video clips
     fahim/photos/
     fahim/thumbs/
     fahim/videos/
4. Copy the public bucket URL: https://pub-<hash>.r2.dev
5. In index.html: replace all picsum.photos URLs in MEDIA_DATA with real R2 URLs
6. git add index.html && git commit -m "chore: swap in real R2 media URLs" && git push
   → Cloudflare Pages redeploys automatically
```

---

## Self-Review

**Spec coverage check:**

| Requirement | Task |
|---|---|
| Hero media slideshow (photos + video, Ken Burns, crossfade) | 6 |
| Gold particle canvas | 5 |
| Hero text staggered fade-up | 4 |
| Hero parallax on scroll | 7 |
| SVG arch + corner flourishes | 4 |
| Sticky frosted-glass nav | 8 |
| Parallax interlude panels | 9 |
| Gallery: Sahid first, Fahim second | 9 |
| Photo 3-col masonry (CSS column-count) | 10 |
| Photo hover: overlay, caption, download | 10 |
| Video 2-col grid | 11 |
| Video hover: play scale, caption, download | 11 |
| Photo lightbox: full-res, scale-in, Escape/backdrop close | 12 |
| Video lightbox: native player, preload=metadata | 12 |
| Scroll fade-up with stagger | 13 |
| Gold line draw-in on scroll | 13 |
| Cursor glow (desktop only) | 7 |
| Download: Photos / Videos / Everything buttons | 14 |
| Footer: italic names, spaced-caps date | 14 |
| Mobile responsive | 15 |
| R2 + Cloudflare Pages deployment | 16 |
| thumbnail vs src separation (HQ lightbox) | 3, 10, 11 |
| iOS Safari parallax fix | 9 |
| No quality-degrading CSS on lightbox media | 15 |
