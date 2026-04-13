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
├── photos/           ← photography section images (12 files, compressed for web)
├── previews/         ← muted MP4 preview clips for the film grid + hero background
├── og-image.jpg      ← dedicated 1200x630 social share image
├── .gitignore        ← ignores .DS_Store, .superpowers/
├── CNAME             ← kernhendricks.com (GitHub Pages custom domain)
└── README.md         ← deployment and DNS instructions
```

Everything — HTML, CSS, JavaScript — is in `index.html`. Do not split into separate files unless Kern explicitly asks for it.

---

## Design System

### Aesthetic
Dark, editorial, cinematic. Inspired by high-end documentary and photojournalism portfolios. Intentionally restrained — lets the work speak. Not tech-startup, not agency-loud. **Guiding priorities: clean and minimal.**

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
--red:       #b85c4a   (defined but currently unused)
```

### Typography
- **Display / headings:** `Cormorant Garamond` (Google Fonts) — serif, elegant, editorial. Used for hero name, section headings, contact heading.
- **Body / UI:** `DM Sans` (Google Fonts) — clean, lightweight sans-serif.
- **Labels / mono / metadata:** `DM Mono` (Google Fonts) — used for section labels, nav links, tags, eyebrow text.

### Layout Principles
- Max content width: 1400px, centered
- Section padding: 120px top/bottom, 48px sides (80px/24px on mobile)
- 12-column CSS Grid for work section
- Generous negative space
- Scroll-triggered reveal animations (IntersectionObserver)

### Key UI Patterns
- **Custom cursor** — small gold dot, expands on hover over interactive elements (rAF-throttled). Gated behind `@media (hover: hover) and (pointer: fine) and (prefers-reduced-motion: no-preference)` so touch devices and accessibility users get the native cursor.
- **Scroll progress line** — 1px gold line at top of viewport, fills left-to-right on scroll (`#progress-line`)
- **Section numbering** — `—01` through `—05` above each section label, in DM Mono 9px `var(--text-dim)`
- **Section fades** — `.section-fade` class on all sections, IntersectionObserver adds `.visible`, fades in on scroll
- **Nav** — hides on scroll down, reappears on scroll up; hamburger on mobile (≤600px, 44px min tap target)
- **Film items** — each `.work-item` is an `<a>` element (proper link, keyboard-focusable, opens in a new tab via `target="_blank" rel="noopener noreferrer"`) wrapping a muted HTML5 `<video>` with a local MP4 preview (`preload="none"`, YouTube thumbnail as `poster`). An IntersectionObserver plays the video when it scrolls into view (threshold 0.3) and pauses it when it leaves.
- **Section labels** — DM Mono, uppercase, gold, with short gold line suffix (`::after`)
- **Reveal animation** — `.reveal` class, triggered by IntersectionObserver, stagger via `.reveal-delay-1/2/3`
- **Hero** — full-viewport, showreel MP4 (`previews/showreel-preview.mp4`) playing muted as background with grain overlay and vignette. "Kern Hendricks" display name + "Documentary Filmmaker & Journalist" eyebrow sit centered over the video, with a scroll indicator at the bottom. No regions list, no CTA button.
- **Skip-to-content link** — hidden until focused, appears top-left for keyboard navigation
- **Focus styles** — `:focus-visible` gold outline on all interactive elements
- **Favicon** — inline SVG "KH" monogram (gold on dark)

---

## Page Flow

```
Hero → Film (—01) → Photography (—02) → Writing (—03) → About (—04) → Contact (—05)
```

Nav links: Film · Photography · Writing · About · Contact

---

## Current Film Grid

Layout: 3-column grid (items 1–3 at `span 4`, items 4–5 at `span 6`). All thumbnails 16:9. Responsive: 2-column at ≤768px, full-width at ≤600px.

Each `.work-item` contains an HTML5 `<video>` element pointing at a local MP4 in `previews/`, with `muted loop playsinline preload="none"` and a YouTube `maxresdefault.jpg` as the `poster`. An IntersectionObserver plays the video when the item scrolls into view (threshold 0.3) and pauses it when it leaves. Clicking the item opens the full YouTube video in a new tab.

| # | Grid span | Title | Outlet | YouTube ID | Preview file | Role |
|---|-----------|-------|--------|------------|--------------|------|
| 1 | span 4 | 2026 Showreel | 2026 Showreel | `aoZN04k0Y5A` | `previews/showreel-preview.mp4` | DoP · Editor · Producer |
| 2 | span 4 | Arriving in Europe: How Refugees Deal With Their Trauma | DW Documentary | `f2q9bI3O-Sc` | `previews/dw-refugees-preview.mp4` | DoP · Producer · Editor |
| 3 | span 4 | A Mental Health Crisis in the West Bank | Undark | `hwDByBSuK60` | `previews/mental-health-preview.mp4` | DoP · Producer · Editor |
| 4 | span 6 | The Toll of Settler Violence in the West Bank | Field Report | `sIFpTlGrddY` | `previews/settler-violence-preview.mp4` | DoP · Producer · Editor |
| 5 | span 6 | In War-Torn Ukraine, a Doctor Evacuates Children with Cancer | Scientific American | `vGo0Xzw0YpU` | `previews/ukraine-cancer-preview.mp4` | DoP · Producer |

**Hero background video:** `previews/showreel-preview.mp4` (same file as item 1)

### Preview clip generation

Preview clips are generated from the YouTube source via `yt-dlp` + `ffmpeg`. Typical recipe:

```bash
# Download at ≤480p
yt-dlp -f "best[height<=480]" -o raw.mp4 "https://youtube.com/watch?v=VIDEO_ID"

# Trim, strip audio, optional crop (for letterboxed 2.39:1 masters), re-encode
ffmpeg -y -i raw.mp4 -ss START_SECONDS -t 12 -an \
  -vf "crop=W:H:X:Y,scale=854:480" \
  -c:v libx264 -crf 32 -preset slow -movflags +faststart output.mp4
```

- `-ss` before `-i` for fast seek; use `ffmpeg -i raw.mp4 -vf cropdetect -f null -` to auto-detect crop values when black bars are baked in
- Target ~500 KB per clip; current 5 clips total ~700 KB
- All outputs are 854x480, 12 seconds, no audio

### Adding a new film item
1. Generate a preview clip (see recipe above) and drop it in `previews/`
2. Add a new `.work-item` div with `data-vid="YOUTUBE_ID"` inside `.work-grid`
3. Thumbnail: `<video src="previews/YOUR_CLIP.mp4" muted loop playsinline preload="none" poster="https://img.youtube.com/vi/YOUTUBE_ID/maxresdefault.jpg"></video>`
4. Update CSS grid spans so rows balance to 12 columns total
5. The IntersectionObserver + click-to-open-YouTube handlers auto-apply to all `.work-item[data-vid]` — no extra JS needed

---

## Photography Section (`#photography`)

Horizontal scroll strip. `<section id="photography">` has `padding: 0`.

**Mechanic:** `.h-scroll-outer` is `height: 400vh`. Inside is a `.h-scroll-sticky` (sticky, 100vh). JS maps vertical scroll progress through the outer container to `translateX` on `.h-scroll-track`. Progress bar + counter at bottom.

**On mobile (≤900px):** reverts to a vertical grid — `flex-wrap: wrap`, each photo 50% width.

**Photos (12 items, all in `photos/` directory):**

| File | Region | Caption |
|------|--------|---------|
| `afghanistan-gems-miners.jpg` | Afghanistan · Nuristan | Gem miners at break outside their mine |
| `L1001647.jpg` | Afghanistan | *(caption TBD)* |
| `ukraine-reactor-lede.jpg` | Ukraine · Kharkiv | Technicians ascending into the reactor hall |
| `afghanistan-forest-trees.jpg` | Afghanistan · Korengal Valley | Trees as livelihood in Wama district |
| `afghanistan-gems-mine-interior.jpg` | Afghanistan · Kunar | Inside Abid's tourmaline mine near Parun |
| `still-field-1.jpg` | *(confirm)* | *(caption TBD)* |
| `ukraine-reactor-core.jpg` | Ukraine · Kharkiv | Reactor core in darkness |
| `afghanistan-forest-loggers.jpg` | Afghanistan · Nuristan | Dilaram and Sanaullah, loggers in Wama |
| `L1001635.jpg` | Afghanistan | *(caption TBD)* |
| `afghanistan-forest-council.jpg` | Afghanistan · Korengal Valley | Tribal council meeting |
| `ukraine-reactor-crater.jpg` | Ukraine · Kharkiv | Crater from March 2022 rocket strike |
| `still-field-2.jpg` | *(confirm)* | *(caption TBD)* |

To update captions/regions: find the `.h-photo` item in `index.html` and edit `.h-photo-region` and `.h-photo-caption` spans.

---

## Writing Section (`#writing`)

Editorial article list. 10 articles in `.article-list`, each an `.article-row` anchor. Grid layout: `140px 1fr 60px auto` (outlet · title · year · arrow). Sorted newest first.

| Outlet | Title | Year |
|--------|-------|------|
| Al Jazeera | The Former Afghan Soldiers Turning to Gem Mining to Survive | 2023 |
| Al Jazeera | Women Working Inside an Afghan Chemical Lab Face Uncertain Future | 2022 |
| Undark | Keeping the Lights On at Ukraine's Research Nuclear Reactor | 2023 |
| Undark | In Ukraine, Grain Shortages Reverberate Beyond Borders | 2023 |
| Scientific American | A Rare Glimpse into Afghanistan's Spectacular, Vanishing Forests | 2023 |
| Vice | Young Boys Work in Dangerous Mines As Afghanistan Prioritizes Coal | 2022 |
| Vice | Look at These Photos of the Taliban's Endless Poppy Fields | 2022 |
| Undark | In a Cold Winter, Afghans Struggle to Keep Warm | 2022 |
| Undark | The Wild Shrub at the Root of the Afghan Meth Epidemic | 2020 |
| Undark | For Afghan Health Workers, a Gauntlet of Making Do | 2019 |

---

## About Section (`#about`)

Two-column sticky layout. Left column (sticky): portrait placeholder + large display heading + skill tags. Right column: body copy + publications list.

**Portrait placeholder:** `.portrait-placeholder` div in `.about-left`, `width: 100%; aspect-ratio: 3/4`. When Kern supplies a photo, replace the entire div with `<img src="..." alt="Kern Hendricks" style="width:100%;aspect-ratio:3/4;object-fit:cover;">`. No rounded corners.

---

## Contact Section (`#contact`)

Centered. Large display heading. Three contact links: email, YouTube, Twitter/X. Keep minimal.

---

## Footer

One line. Copyright left, location/availability right.

---

## Deployment

- **Repo:** `chaoticabacus/chaoticabacus.github.io` (public)
- **Branch:** `main`
- **Hosting:** GitHub Pages — auto-deploys on every push, ~60 second lag
- **Domain:** kernhendricks.com via Cloudflare DNS (DNS-only / grey cloud — do NOT proxy)
- **SSL:** GitHub Pages enforced HTTPS (free)

### To preview locally
```bash
python3 -m http.server 8080
# then open http://localhost:8080
```

### To deploy changes
```bash
git add index.html photos/ previews/
git commit -m "describe what changed"
git push origin main
```

### Version tags (for rollback)

Annotated git tags mark notable versions of the site. Use `git tag -l -n1` to list them. To roll back to a tagged version:

```bash
git revert TAG..HEAD && git push       # safe: new commit undoes the changes
```

Current tags:
- **`static-thumbs`** (`f8f0a7f`) — static YouTube thumbnails, no intro splash
- **`black-intro-dynamic-thumbnails`** (`888ccab`) — black intro splash + autoplay MP4 previews on scroll

---

## Performance Notes

- **Images:** All photos compressed and resized to max 2000px wide, JPEG quality 70 (total ~7.4 MB, down from ~123 MB)
- **Film previews:** Self-hosted MP4 clips (~700 KB total for all 5) instead of YouTube iframes — avoids ~1.5-2 MB per embed of third-party player JS/CSS. Each video has `preload="none"` so nothing downloads until the IntersectionObserver calls `.play()` on scroll-in, and videos pause when scrolled out of view to free CPU.
- **Scroll handling:** All scroll-driven updates (intro overlay fade, progress line, horizontal scroll, nav hide/show) consolidated into a single `scroll` listener with `requestAnimationFrame` throttling. Cursor mousemove also rAF-throttled.
- **Lazy loading:** All photography images use `loading="lazy"`; film preview videos use `preload="none"` with YouTube thumbnail posters
- **Fonts:** Single Google Fonts stylesheet loads Cormorant Garamond, DM Sans, and DM Mono with `display=swap`
- **Responsive breakpoints:** 900px (photography + about reflow), 768px (work grid 2-col), 700px (article row compact), 600px (mobile nav + work grid full-width)

---

## Known Todos / Future Improvements

- [ ] Add portrait photo to About section (Kern to supply)
- [ ] Add captions/regions for `L1001647.jpg`, `L1001635.jpg`, `still-field-1.jpg`, `still-field-2.jpg` in photography section
- [ ] Consider adding a CV download link (PDF) in the About section
- [ ] Paste Cloudflare Web Analytics token into the `data-cf-beacon` attribute at the bottom of `index.html` (script tag is already wired in; just needs the token from Cloudflare → Analytics & Logs → Web Analytics)
- [ ] Consider adding more work items to the video grid
- [x] Lazy-load the hero YouTube background iframe (currently loads on page load)
- [x] Create a dedicated OG image (1200x630, <300 KB) — current one uses a full photo

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
- Do not revert the film grid to YouTube iframes — the self-hosted MP4 preview pattern (with click-to-open-YouTube in a new tab) is intentional and dramatically lighter
