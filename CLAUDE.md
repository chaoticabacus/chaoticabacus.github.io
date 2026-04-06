# kernhendricks.com — Portfolio Site

## Project Overview

Personal portfolio site for **Kern Hendricks** — documentary filmmaker, photojournalist, conflict analyst, and humanitarian researcher based in London.

The site is a single-page HTML/CSS/JS application with no frameworks, no build process, and no dependencies. It is hosted on **GitHub Pages** (repo: `chaoticabacus/chaoticabacus.github.io`) with a custom domain at **kernhendricks.com** managed via **Cloudflare DNS**.

---

## Owner / Persona

- **Name:** Kern Hendricks
- **Based:** London, UK
- **Contact:** kernhendricks@gmail.com | +44 7541 068 726
- **Twitter/X:** @kernhendricks
- **YouTube:** @KernHendricks
- **GitHub:** chaoticabacus

**Professional identity (in priority order for this site):**
1. Documentary filmmaker & DoP
2. Photojournalist
3. Conflict analyst / humanitarian researcher

**Published in:** Al Jazeera, Scientific American, DW Documentary, Foreign Policy, Undark, The New Humanitarian, ProPublica

**Notable credential:** Pulitzer Center Crisis Reporting Grant recipient

**Field regions:** Afghanistan, Ukraine, Palestine, Lebanon, MENA, Central Asia, Europe

**Kit:** Sony FX3, DJI Mini 3 Pro (drone), Rode/DJI wireless audio

**Post-production:** DaVinci Resolve, Adobe Premiere Pro, After Effects, Photoshop, Lightroom

---

## File Structure

```
/
├── index.html        ← entire site lives here (single file)
├── CNAME             ← kernhendricks.com (GitHub Pages custom domain)
└── README.md         ← deployment and DNS instructions
```

Everything — HTML, CSS, JavaScript — is in `index.html`. Do not split into separate files unless Kern explicitly asks for it.

---

## Design System

### Aesthetic
Dark, editorial, cinematic. Inspired by high-end documentary and photojournalism portfolios. Intentionally restrained — lets the work speak. Not tech-startup, not agency-loud.

### Color Tokens (CSS variables in `:root`)
```
--bg:        #0a0a08   (near-black warm background)
--bg-2:      #111110
--bg-3:      #1a1a17
--surface:   #1e1e1a
--border:    rgba(255,255,255,0.07)
--border-hi: rgba(255,255,255,0.15)
--text:      #e8e4dc   (warm off-white)
--text-dim:  #7a7870
--text-mid:  #b0aca4
--accent:    #c8a96e   (warm gold — primary accent)
--accent-dim:#7a6540
--red:       #b85c4a   (used sparingly)
```

### Typography
- **Display / headings:** `Cormorant Garamond` (Google Fonts) — serif, elegant, editorial. Used for hero name, section headings, contact heading.
- **Body / UI:** `DM Sans` (Google Fonts) — clean, lightweight sans-serif.
- **Labels / mono / metadata:** `DM Mono` (Google Fonts) — used for section labels, nav links, tags, eyebrow text.

### Layout Principles
- Max content width: 1400px, centered
- Section padding: 120px top/bottom, 48px sides (80px/24px on mobile)
- Asymmetric work grid using CSS Grid (12 columns)
- Generous negative space
- Scroll-triggered reveal animations (IntersectionObserver)

### Key UI Patterns
- **Custom cursor** — small gold dot, expands on hover over interactive elements
- **Nav** — hides on scroll down, reappears on scroll up
- **Work items** — YouTube iframes as thumbnails (muted, no controls), hover reveals play button and role text
- **Section labels** — DM Mono, uppercase, gold, with short gold line suffix (`::after`)
- **Reveal animation** — `.reveal` class, triggered by IntersectionObserver, stagger via `.reveal-delay-1/2/3`
- **Hero** — full-viewport, showreel playing muted as background with grain overlay and vignette

---

## Current Work Grid

Order and grid spans matter — the first item is always the featured/large item.

| # | Grid span | Title | Outlet | YouTube ID | Role |
|---|-----------|-------|--------|------------|------|
| 1 | 7 cols, 2 rows (featured) | Cinematography Showreel | 2025 Showreel | `aoZN04k0Y5A` | DoP · Editor · Producer |
| 2 | 5 cols | Arriving in Europe: How Refugees Deal With Their Trauma | DW Documentary | `f2q9bI3O-Sc` | DoP · Producer · Editor |
| 3 | 5 cols | A Mental Health Crisis in the West Bank | Undark | `hwDByBSuK60` | DoP · Producer · Editor |
| 4 | 4 cols | The Toll of Settler Violence in the West Bank | Field Report | `sIFpTlGrddY` | DoP · Producer · Editor |
| 5 | 8 cols | In War-Torn Ukraine, a Doctor Evacuates Children with Cancer | Scientific American | `vGo0Xzw0YpU` | DoP · Producer |

**Hero background video:** `aoZN04k0Y5A` (same as showreel)

### Adding a new work item
1. Add a new `.work-item` div inside `.work-grid`
2. Set grid span in CSS: `.work-item:nth-child(N) { grid-column: span X; }`
3. Use YouTube embed URL: `https://www.youtube.com/embed/VIDEO_ID?mute=1&controls=0&showinfo=0&rel=0&modestbranding=1&playsinline=1`
4. Re-balance grid spans so rows feel visually intentional (total columns per row = 12)

---

## Sections

### `#hero`
Full-viewport. Showreel plays muted as background (opacity 0.35, desaturated). Grain texture overlay. Vignette. Animated headline with name, descriptor showing field regions, CTA button to `#work`.

### `#work`
Asymmetric 12-column CSS grid of video work items. YouTube embeds as thumbnails (muted, no controls, lazy loaded). Hover state: brightens video, shows play button and role text.

### `#about`
Two-column sticky layout. Left: large display heading + skill tags (sticky). Right: body copy paragraphs + publications list. **No portrait photo currently** — Kern will supply one later. When adding photo: place it in left column above or below tags, use `border-radius: 0` (no rounded corners — matches editorial aesthetic), aspect ratio 4:5 or 3:4.

### `#contact`
Centered. Large display heading. Three contact links: email, YouTube, Twitter/X. Keep minimal.

### `footer`
One line. Copyright left, location/availability right.

---

## Deployment

- **Repo:** `chaoticabacus/chaoticabacus.github.io` (public)
- **Branch:** `main`
- **Hosting:** GitHub Pages — auto-deploys on every push, ~60 second lag
- **Domain:** kernhendricks.com via Cloudflare DNS
- **SSL:** GitHub Pages enforced HTTPS (free)

### To preview locally
```bash
python3 -m http.server 8080
# then open http://localhost:8080
```

### To deploy changes
```bash
git add index.html
git commit -m "describe what changed"
git push origin main
```

---

## Known Todos / Future Improvements

- [ ] Add portrait photo to About section (Kern to supply)
- [ ] Add journalism/writing section with article links (Al Jazeera, Foreign Policy, Scientific American etc.)
- [ ] Add photography section or gallery
- [ ] Consider adding a CV download link (PDF) in the About section
- [ ] Add Open Graph / social share meta tags for better link previews
- [ ] Add Google Analytics or privacy-friendly analytics (Plausible/Fathom)
- [ ] Mobile nav menu (currently hidden on small screens)

---

## What NOT to do

- Do not add frameworks (React, Vue etc.) — keep it plain HTML/CSS/JS
- Do not split into multiple files unless explicitly asked
- Do not use Inter, Roboto, or other generic sans-serifs — stick to the type system above
- Do not use purple gradients, glassmorphism, or generic "AI aesthetic" design patterns
- Do not add rounded corners to images
- Do not change the colour accent from gold without discussing it first
- Do not add autoplay video with sound
- Do not proxy Cloudflare DNS records for GitHub Pages (must be DNS only / grey cloud)
