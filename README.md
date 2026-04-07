# GOLL GYM — Website

Single-page gym website for **GOLL GYM**. Built with a **Kinetic Brutalism** design system: dark, sharp-edged, high-contrast editorial aesthetic. 
## Tech Stack

| Layer | Tool |
|-------|------|
| Markup/Logic | HTML5 + Vanilla JS (single file, 1644 lines) |
| Styling | Tailwind CSS (CDN) with custom design tokens |
| Animation | GSAP 3.12 + ScrollTrigger plugin |
| Typography | Space Grotesk (headlines) + Inter (body) via Google Fonts |

## Project Structure

```
gymwebsite/
├── index.html                          # Entire site — markup, CSS, JS
├── hf_20260405_195526_...mp4           # Hero scroll-driven video (dumbbell animation)
├── design_refs/
│   └── DESIGN.md                       # Kinetic Brutalism design spec
└── README.md
```

## Page Sections

| # | Section | ID | Description |
|---|---------|-----|-------------|
| — | Loading screen | `#loading` | Animated progress bar with phase labels |
| — | Navigation | `#navbar` | Glassmorphism fixed nav with scroll-spy |
| 1 | Hero | `#hero` | Scroll-driven video + hero copy |
| — | Marquee | — | Infinite red ticker strip |
| — | Stats | — | Animated counters (450+ warriors, 15 coaches, 50 classes, 24/7) |
| 2 | Programs | `#programs` | 3 program cards: Powerlifting, Hybrid Combat, Engine Room |
| — | Manifesto | — | Full-width editorial quote block |
| 3 | Trainers | `#trainers` | 4 coach cards with hover color reveal and Instagram links |
| 4 | Schedule | `#schedule` | 6-day live class schedule with real-time LIVE detection |
| 5 | Pricing | `#pricing` | 3 membership tiers, each opens a plan-specific modal |
| 6 | Testimonials | `#testimonials` | Drag-to-scroll review cards with photos and verified badges |
| — | CTA | `#cta` | Full-viewport closing CTA with trust badges |
| — | Footer | — | 4-column footer with newsletter signup |

## Key Features

### Scroll-Driven Video (Hero)
The hero `<video>` is paused permanently. Its `currentTime` is driven by scroll progress via a GSAP proxy object:
```js
const proxy = { t: 0 };
gsap.to(proxy, {
  t: duration, ease: 'none',
  scrollTrigger: {
    trigger: '#hero', start: 'top top', end: '+=120%', scrub: 0.6,
    onUpdate: (self) => { vid.currentTime = duration * self.progress; }
  }
});
```
Scrolling down advances the dumbbell animation; scrolling back reverses it.

### Loading Screen
Fakes a multi-phase load (INITIALIZING → LOADING ASSETS → PREPARING ARENA → READY) with a gradient progress bar. On complete, fades out and calls `startAnimations()`.

### Cursor Glow
A 500px radial gradient follows the mouse cursor as a fixed overlay, creating a subtle red ambient light effect.

### Nav Scroll-Spy
Tracks `window.scrollY` against section `offsetTop` values at 35% viewport offset. Highlights the active nav link with a sliding `::after` red underline.

### Live Schedule
- 6-day data set (Mon Apr 7 → Sat Apr 12), 4–5 classes per day
- `getLiveClass()` compares current time to class start + duration range
- Active class gets a red left-border accent + pulsing LIVE badge
- Capacity bars animate from 0% width on tab load (70ms stagger per row)
- FULL classes show gradient badge + disabled BOOK button
- Day tabs fade the grid out/in on switch with GSAP

**Class types and colors:**
| Type | Color |
|------|-------|
| Powerlifting | `#FF5540` |
| Hybrid Combat | `#3B82F6` |
| Engine Room | `#F59E0B` |

### Pricing Modal
- Three plans: **Warrior $49** / **Champion $99** / **Legend $199**
- Clicking any plan CTA opens the modal pre-set to that plan
- Left column shows plan-specific perks + locked features from the `PLANS` data object
- In-modal plan switcher with animated perk swap (GSAP fade out/in)
- Right column: form with underline-only inputs; validates name + email on submit
- Success state replaces form with confirmation message

### Testimonial Cards
- 4 drag-to-scroll cards with real Unsplash photos (38×38px), star ratings, verified badges, program type tags, and dates (Jan–Apr 2026)
- Mouse drag-to-scroll with `mousedown/mousemove` pattern
- Scroll-progress dots update on `scroll` event
- Hover lifts card 4px and highlights border in `#FF5540`
- GSAP staggered entrance (scale + fade) on ScrollTrigger

### All CTAs Wired Up
| Button | Action |
|--------|--------|
| JOIN NOW (nav + mobile menu) | Opens modal (Warrior default) |
| START TRAINING (hero) | Opens modal |
| VIEW PROGRAMS (hero) | Scrolls to `#programs` |
| GET STARTED (Warrior) | Opens modal pre-set to Warrior |
| JOIN ELITE (Champion) | Opens modal pre-set to Champion |
| GO LEGEND (Legend) | Opens modal pre-set to Legend |
| GET 1ST FREE TRAINING (CTA) | Opens modal |
| SEE PRICING (CTA) | Scrolls to `#pricing` |

### Other Animations
- **Hero entrance**: headline clips in from bottom, text fades up, video slides in from right
- **Scroll reveals**: `.gsap-reveal-up / left / right` applied throughout with `--delay` CSS var for stagger
- **Stat counters**: count from 0 to target on scroll enter (GSAP proxy)
- **Parallax**: background "NO LIMITS" text drifts on scrub
- **Pricing card 3D tilt**: `mousemove` drives `rotateX/rotateY` via GSAP
- **Floating stat pulse**: "Always Open" card bobs up/down continuously
- **Program cards**: grayscale → color on hover, red bar sweeps from left
- **Trainer cards**: grayscale → color on hover, social icon slides in from right
- **Marquee**: infinite red ticker, CSS `@keyframes marquee`
- **Scroll indicator**: SVG mouse with bouncing dot, CSS `@keyframes scrollBounce`
- **Pulse**: `@keyframes pulse` used on live clock dot and LIVE class badges
- **`prefers-reduced-motion`**: all animations disabled via CSS media query

## Design System — Kinetic Brutalism

Full spec in `design_refs/DESIGN.md`. Key rules enforced:

- **0px border-radius** on everything (Tailwind config overrides all radius defaults)
- **No 1px solid borders** between sections — use tonal background shifts instead
- **Glassmorphism nav**: `rgba(13,13,13,0.65)` + `backdrop-filter:blur(20px)`
- **Primary CTA gradient**: `#FFB4A8` → `#FF5540`, text `#5C0000`
- **Ghost border fallback**: `outline-variant` (`#603E39`) at 15–20% opacity

### Color Tokens (Tailwind custom)

| Token | Hex | Role |
|-------|-----|------|
| `surface` | `#131313` | Page base |
| `surface-container-lowest` | `#0E0E0E` | Darkest sections (hero, CTA, footer) |
| `surface-container-low` | `#1B1B1B` | Stats, schedule grid, pricing cards |
| `surface-container` | `#1F1F1F` | Cards, modal inputs |
| `surface-container-high` | `#2A2A2A` | Input borders |
| `surface-container-highest` | `#353535` | Emphasis dividers, capacity tracks |
| `primary` | `#FFB4A8` | Gradient start, accent |
| `primary-container` | `#FF5540` | CTA fill, type badges, live indicators |
| `on-primary-container` | `#5C0000` | CTA button text |
| `outline-variant` | `#603E39` | Ghost borders |

### Typography
- **Headlines**: Space Grotesk, `font-black` (900), uppercase, `letter-spacing: -0.04em`
- **Display size**: `clamp(4rem, 9vw, 8.5rem)` for hero; `clamp(3rem, 6vw, 5.5rem)` for section heads
- **Body**: Inter, regular (400), `1rem`, `line-height: 1.5`
- **Labels**: Space Grotesk, 700, `0.7rem`, `letter-spacing: 0.4em`, uppercase, color `#FF5540`

## Running Locally

No build step — open directly in a browser:

```bash
open index.html
```

Or serve with any static server:

```bash
npx serve .
# visit http://localhost:3000
```

> The scroll-driven video requires the `.mp4` file to be in the same directory as `index.html`.

## Browser Notes

Requires modern browser support for:
- `backdrop-filter` (nav glassmorphism)
- `video.currentTime` writable (scroll-driven hero)
- CSS `clamp()` (responsive type)
- GSAP ScrollTrigger (all scroll animations)
