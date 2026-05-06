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
- **Instagram:** @kernhendricks
- **YouTube:** @KernHendricks
- **GitHub:** chaoticabacus

**Professional identity (in priority order for this site):**
1. Documentary filmmaker & DoP
2. Photojournalist
3. Conflict analyst / humanitarian researcher

**Published in:** Al Jazeera, Scientific American, DW Documentary, Foreign Policy, Undark, The New Humanitarian, ProPublica

**Notable credential:** Pulitzer Center Crisis Reporting Grant recipient

**Field regions:** Afghanistan, Ukraine, Palestine, Lebanon, Iraq, Syria, Yemen, MENA, Central Asia, Europe

**Post-production:** DaVinci Resolve, Adobe Premiere Pro, After Effects, Photoshop, Lightroom

---

## File Structure

```
/
├── index.html          ← entire site lives here (single file)
├── photos/             ← photography section images (JPEG originals + WebP versions)
├── previews/           ← muted MP4 preview clips + local poster JPEGs
├── kern-portrait.jpg   ← about section portrait (1024×900, cropped)
├── og-image.jpg        ← dedicated 1200x630 social share image
├── .gitignore          ← ignores .DS_Store, .superpowers/
├── CNAME               ← kernhendricks.com (GitHub Pages custom domain)
└── README.md           ← deployment and DNS instructions
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
- **Custom cursor** — small gold dot, expands on hover over interactive elements (rAF-throttled). Gated behind `@media (hover: hover) and (pointer: fine) and (prefers-reduced-motion: no-preference)` so touch devices and accessibility users get the native cursor. On film items, a `▶ Play` text label appears beside the cursor dot.
- **Scroll progress line** — 1px gold line at top of viewport, fills left-to-right on scroll (`#progress-line`)
- **Section numbering** — `—01` through `—04` above each section label, in DM Mono 9px `var(--text-dim)`
- **Section fades** — `.section-fade` class on all sections, IntersectionObserver adds `.visible`, fades in on scroll
- **Nav** — hides on scroll down, reappears on scroll up; active section's link is highlighted gold; hamburger on mobile (≤600px, 44px min tap target)
- **Active nav highlighting** — scroll position is compared to section offsets on every scroll tick; the current section's nav link gets `.active` (gold colour)
- **Film items** — each `.work-item` is an `<a>` element (proper link, keyboard-focusable, opens in a new tab via `target="_blank" rel="noopener noreferrer"`) wrapping a muted HTML5 `<video>` with a local MP4 preview (`preload="none"`, local poster JPEG). An IntersectionObserver plays the video when it scrolls into view (threshold 0.3) and pauses it when it leaves.
- **Section labels** — DM Mono, uppercase, gold, with short gold line suffix (`::after`)
- **Reveal animation** — `.reveal` class, triggered by IntersectionObserver, stagger via `.reveal-delay-1/2/3`
- **Hero** — full-viewport, showreel MP4 (`previews/hero-showreel.mp4`) playing muted as background with grain overlay and vignette. "Kern Hendricks" display name (split across two lines as `<span class="hero-name-line">` elements with a 140ms staggered fade-up entrance — Kern lands first, then italic Hendricks) + "Film and reporting from the margins of crisis" eyebrow sit centered over the video, with a scroll indicator at the bottom. Hero content has a parallax effect: text recedes at 35% scroll speed; scroll indicator fades out as the user starts scrolling. A small **Sound off / Sound on** toggle (`#heroSoundToggle`) at bottom-right lets viewers unmute the showreel; clicking toggles the video's `muted` property and swaps the speaker glyph + label + aria-label. Keyboard-focusable, mobile-tappable.
- **Press strip** — quiet credibility row between hero and film grid (`<section id="press">`). Five inline-SVG outlet wordmarks — Al Jazeera, Scientific American, DW Documentary, Foreign Policy, ProPublica — set in `var(--text-dim)` on a slightly-darker `--bg-2` band. Optically sized (each logo gets a custom CSS height to read at matched optical weight; not all the same height). Non-clickable, decorative, single colour via `currentColor`. Layout: flex `space-between` row above 1100px; 6-column CSS Grid producing a balanced 3+2 layout below 1100px. **Reveal** is scroll-progress-driven (not the standard `.section-fade` IntersectionObserver) — `updatePressHandoff()` adds `.press-revealed` once the user has scrolled past 60% of the hero, so the strip's 0.6s fade + translateY entrance overlaps with the hero parallax exit and reads as a single handoff motion.
- **Skip-to-content link** — hidden until focused, appears top-left for keyboard navigation
- **Focus styles** — `:focus-visible` gold outline on all interactive elements
- **Favicon** — inline SVG "KH" monogram (gold on dark)
- **Email copy** — clicking the email contact link copies the address to clipboard and shows a brief toast notification ("Email copied") instead of opening a mail client
- **Toast** — `.toast` element appended to body, `visible` class triggers fade/slide in, auto-removed after 2s

---

## Page Flow

```
Hero → Press Strip → Film (—01) → Photography (—02) → Writing (—03) → About (—04)
```

Nav links: Film · Photography · Writing · About

Contact details (email, YouTube, Instagram, Twitter/X) live inside the About section, directly under the "Published in" block — no separate Contact section.

---

## Current Film Grid

Layout: 3-column grid (items 1–3 at `span 4`, items 4–5 at `span 6`). All thumbnails 16:9. Responsive: 2-column at ≤768px, full-width at ≤600px.

Each `.work-item` contains an HTML5 `<video>` element pointing at a local MP4 in `previews/`, with `muted loop playsinline preload="none"` and a **local poster JPEG** in `previews/`. An IntersectionObserver plays the video when the item scrolls into view (threshold 0.3) and pauses it when it leaves. Clicking the item opens the full YouTube video in a new tab.

| # | Grid span | Title | Outlet | YouTube ID | Preview file | Poster file | Role |
|---|-----------|-------|--------|------------|--------------|-------------|------|
| 1 | span 4 | 2026 Showreel | 2026 Showreel | `aoZN04k0Y5A` | `previews/showreel-preview.mp4` | `previews/showreel-poster.jpg` | DoP · Editor · Producer |
| 2 | span 4 | Arriving in Europe: How Refugees Deal With Their Trauma | DW Documentary | `f2q9bI3O-Sc` | `previews/dw-refugees-preview.mp4` | `previews/dw-refugees-poster.jpg` | DoP · Producer · Editor |
| 3 | span 4 | A Mental Health Crisis in the West Bank | Undark | `hwDByBSuK60` | `previews/mental-health-preview.mp4` | `previews/mental-health-poster.jpg` | DoP · Producer · Editor |
| 4 | span 6 | The Toll of Settler Violence in the West Bank | Field Report | `sIFpTlGrddY` | `previews/settler-violence-preview.mp4` | `previews/settler-violence-poster.jpg` | DoP · Producer · Editor |
| 5 | span 6 | In War-Torn Ukraine, a Doctor Evacuates Children with Cancer | Scientific American | `vGo0Xzw0YpU` | `previews/ukraine-cancer-preview.mp4` | `previews/ukraine-cancer-poster.jpg` | DoP · Producer |

**Hero background video:** `previews/showreel-preview.mp4` (same file as item 1)

### Preview clip generation

Preview clips are generated from the YouTube source via `yt-dlp` + `ffmpeg`. Typical recipe:

```bash
# Download best video up to 720p (much sharper downscale to 854x480 than sourcing 480p directly)
yt-dlp -f "bv*[height<=720]+ba/b[height<=720]" --merge-output-format mp4 -o raw.mp4 "https://youtube.com/watch?v=VIDEO_ID"

# Trim, strip audio, scale to 854x480 (preserving any letterbox bars), re-encode
ffmpeg -y -ss START_SECONDS -i raw.mp4 -t 12 -an \
  -vf "scale=854:480" \
  -c:v libx264 -crf 26 -preset slow -movflags +faststart output.mp4
```

- `-ss` before `-i` for fast seek
- For cinematic sources where YouTube has already stripped letterbox bars (e.g. 1280×544 for 2.35:1), use `scale=854:H,pad=854:480:0:(480-H)/2:black` to reintroduce them rather than stretching the frame
- All outputs are 854×480, 12 seconds, no audio
- CRF 26 (not 32) — yields noticeably sharper background-autoplay thumbnails. Clips typically land between 150–870 KB depending on motion/content; total previews dir ~2.8 MB

### Poster generation

Local poster images are extracted from the preview MP4 at 2 seconds and stored in `previews/`:

```bash
ffmpeg -y -ss 2 -i previews/NAME-preview.mp4 -vframes 1 -q:v 3 previews/NAME-poster.jpg
```

Poster files are ~18–47 KB each — much smaller than YouTube CDN thumbnails (~100–200 KB).

### Adding a new film item
1. Generate a preview clip (see recipe above) and drop it in `previews/`
2. Generate a poster image (see recipe above)
3. Add a new `<a class="work-item" data-vid="YOUTUBE_ID" href="..." ...>` inside `.work-grid`
4. Thumbnail: `<video src="previews/YOUR_CLIP.mp4" muted loop playsinline preload="none" poster="previews/YOUR_POSTER.jpg"></video>`
5. Update CSS grid spans so rows balance to 12 columns total
6. The IntersectionObserver + cursor play label auto-apply to all `.work-item[data-vid]` — no extra JS needed

---

## Photography Section (`#photography`)

Horizontal scroll strip. `<section id="photography">` has `padding: 0`.

**Mechanic:** `.h-scroll-outer` is `height: 400vh`. Inside is a `.h-scroll-sticky` (sticky, 100vh). JS maps vertical scroll progress through the outer container to `translateX` on `.h-scroll-track`. Progress bar + counter at bottom.

**Keyboard navigation:** Left/right arrow keys jump photo-by-photo through the strip when the section is in the scroll zone.

**On mobile (≤900px):** reverts to a vertical grid — `flex-wrap: wrap`, each photo 50% width.

**Photos (12 items, all in `photos/` directory):**

Each photo is served as WebP (via `<picture>` element with JPEG fallback) except `ukraine-reactor-crater.jpg` where the JPEG is already smaller than any WebP encoding.

| File | Region | Caption | WebP? |
|------|--------|---------|-------|
| `afghanistan-gems-miners.jpg` | Afghanistan · Nuristan | Gem miners at break outside their mine | ✓ |
| `L1001647.jpg` | Afghanistan | *(caption TBD)* | ✓ |
| `ukraine-reactor-lede.jpg` | Ukraine · Kharkiv | Technicians ascending into the reactor hall | ✓ |
| `afghanistan-forest-trees.jpg` | Afghanistan · Korengal Valley | Trees as livelihood in Wama district | ✓ |
| `afghanistan-gems-mine-interior.jpg` | Afghanistan · Kunar | Inside Abid's tourmaline mine near Parun | ✓ |
| `still-field-1.jpg` | *(confirm)* | *(caption TBD)* | ✓ |
| `ukraine-reactor-core.jpg` | Ukraine · Kharkiv | Reactor core in darkness | ✓ |
| `afghanistan-forest-loggers.jpg` | Afghanistan · Nuristan | Dilaram and Sanaullah, loggers in Wama | ✓ |
| `L1001635.jpg` | Afghanistan | *(caption TBD)* | ✓ |
| `afghanistan-forest-council.jpg` | Afghanistan · Korengal Valley | Tribal council meeting | ✓ |
| `ukraine-reactor-crater.jpg` | Ukraine · Kharkiv | Crater from March 2022 rocket strike | JPEG only |
| `still-field-2.jpg` | *(confirm)* | *(caption TBD)* | ✓ |

To update captions/regions: find the `.h-photo` item in `index.html` and edit `.h-photo-region` and `.h-photo-caption` spans.

### Adding a new photo
1. Add JPEG to `photos/`, compress to max 2000px wide at quality 70
2. Generate WebP: `cwebp -q 82 photos/FILE.jpg -o photos/FILE.webp` — check it's actually smaller; if not, omit WebP and use plain `<img>`
3. Add `.h-photo` div with `<picture>` + `<source>` + `<img>` (see existing items as template)
4. All photos use `loading="lazy"`

---

## Writing Section (`#writing`)

Editorial article list. 10 articles in `.article-list`, each an `.article-row` anchor. Grid layout: `140px 1fr 56px 24px` (outlet · title · year · arrow). Sorted newest first.

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

Two-column sticky layout, with the section header (`—04 / About`) spanning both columns above. Left column (sticky): portrait photo + skill tags. Right column: body copy + publications list + contact links.

**Portrait:** `<img class="portrait" src="kern-portrait.jpg" alt="Kern Hendricks" loading="lazy">` in `.about-left`. `width: 100%; height: auto;` — frame matches the image's native aspect ratio (1024×900) so nothing gets cropped. `margin-top: 6px` compensates for line-leading so the top of the image sits flush with the first text line. No rounded corners.

**Bio slide-in:** Children of `.about-right` use the `.about-slide` class (initial state: `opacity: 0; translateX(48px)`). An IntersectionObserver watches `.portrait` with `rootMargin: -45% 0px -45% 0px` (a thin band around viewport centre); when the portrait enters that band, `slide-in` is added to `.about-right` and the children fade/slide into place with a staggered transition delay. Triggers once. Disabled under `prefers-reduced-motion`.

---

## Contact Links

Live inside the About section's right column, immediately after the `.about-publications` block. Four `<a>` elements (Email, YouTube, Instagram, Twitter/X) inside `.contact-links`, separated from the publications above by a thin top border. There is no standalone Contact section.

The email link copies the address to clipboard (via `navigator.clipboard`) and shows a toast rather than opening a mail client. Falls back to `mailto:` if clipboard API is unavailable.

---

## Footer

One line. Copyright left, location/availability right.

---

## Deployment

- **Repo:** `chaoticabacus/chaoticabacus.github.io` (public)
- **Branch:** `main`
- **Hosting:** GitHub Pages — auto-deploys on every push, ~60 second lag
- **Domain:** kernhendricks.com via Cloudflare DNS, **proxied (orange cloud)** for CDN, analytics, and DDoS protection
- **SSL:** GitHub Pages enforced HTTPS (free) + Cloudflare SSL/TLS mode set to **Full** (NOT Flexible — Flexible causes infinite redirect loops with GitHub Pages)
- **Analytics:** Cloudflare Web Analytics, enabled automatically via the proxied DNS record. No JS snippet in the source.

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

- **Images:** Photos compressed and resized to max 2000px wide, JPEG quality 70. 11 of 12 are served as WebP via `<picture>` elements (~47% smaller on average); `ukraine-reactor-crater.jpg` is kept as JPEG (already smaller than any WebP encoding).
- **Film posters:** Self-hosted local JPEGs (18–47 KB each) extracted from preview clips at 2s via ffmpeg — eliminates YouTube CDN dependency and loads faster than `maxresdefault.jpg` (~100–200 KB).
- **Film previews:** Self-hosted MP4 clips (~700 KB total for all 5) instead of YouTube iframes — avoids ~1.5–2 MB per embed of third-party player JS/CSS. Each video has `preload="none"` so nothing downloads until the IntersectionObserver calls `.play()` on scroll-in, and videos pause when scrolled out of view to free CPU.
- **Hero video preload:** `<link rel="preload" as="video" href="previews/showreel-preview.mp4" type="video/mp4">` in `<head>` so the browser prioritises it.
- **Scroll handling:** All scroll-driven updates (progress line, horizontal scroll, nav hide/show, active nav, hero parallax) consolidated into a single `scroll` listener with `requestAnimationFrame` throttling. Cursor mousemove also rAF-throttled.
- **Lazy loading:** All photography images use `loading="lazy"`; film preview videos use `preload="none"` with local poster images.
- **Fonts:** Single Google Fonts stylesheet loads Cormorant Garamond, DM Sans, and DM Mono with `display=swap`.
- **Responsive breakpoints:** 900px (photography + about reflow), 768px (work grid 2-col), 700px (article row compact), 600px (mobile nav + work grid full-width)
- **Meta:** `theme-color`, `canonical`, full Open Graph + Twitter Card tags in `<head>`

---

## Known Todos / Future Improvements

- [ ] Add captions/regions for `L1001647.jpg`, `L1001635.jpg`, `still-field-1.jpg`, `still-field-2.jpg` in photography section
- [ ] Consider adding a CV download link (PDF) in the About section
- [ ] Consider adding more work items to the video grid

---

## What NOT to do

- Do not add frameworks (React, Vue etc.) — keep it plain HTML/CSS/JS
- Do not split into multiple files unless explicitly asked
- Do not use Inter, Roboto, or other generic sans-serifs — stick to the type system above
- Do not use purple gradients, glassmorphism, or generic "AI aesthetic" design patterns
- Do not add rounded corners to images
- Do not change the colour accent from gold without discussing it first
- Do not add autoplay video with sound
- Do not switch Cloudflare SSL/TLS mode to "Flexible" — it causes infinite redirect loops with GitHub Pages. Stick with "Full" or "Full (strict)".
- Do not revert the film grid to YouTube iframes — the self-hosted MP4 preview pattern (with click-to-open-YouTube in a new tab) is intentional and dramatically lighter
- Do not use YouTube CDN URLs for film poster images — use locally-generated `previews/NAME-poster.jpg` files instead
- Do not serve photos without a WebP alternative (unless WebP is larger for that specific image — check before omitting)
