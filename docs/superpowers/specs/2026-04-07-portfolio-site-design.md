# Portfolio Site — Full Build Design Spec
**Date:** 2026-04-07  
**Owner:** Kern Hendricks (kernhendricks.com)  
**Repo:** chaoticabacus/chaoticabacus.github.io  
**File:** Single-file `index.html` — no frameworks, no build process

---

## Overview

Full pass on the existing portfolio site. The site already has a working hero, video work grid, about section, and contact section. This spec covers everything to be added or changed.

---

## Page Flow (final)

```
Hero → Work (video) → Photography → Writing → About → Contact → Footer
```

Nav links updated to match: Work · Photography · Writing · About · Contact

---

## 1. Section-Level Fade Transitions

Every `<section>` gets a section-level reveal on scroll, in addition to the existing per-element `.reveal` stagger.

**Behaviour:**
- Each section starts at `opacity: 0; transform: translateY(20px)`
- Triggered by `IntersectionObserver` when section enters viewport
- Transitions to `opacity: 1; transform: translateY(0)` over `0.9s var(--ease-out-expo)`
- Per-element `.reveal` stagger inside each section fires after the section itself fades in (delay offset)

**Implementation:** Add `.section-fade` class to all `<section>` elements. IntersectionObserver in JS adds `.visible` class. CSS handles the transition. The existing per-element `.reveal` IntersectionObserver is unchanged — it fires independently when each element enters the viewport. The two systems are additive, not nested: section fade handles the section container, reveal handles children.

---

## 2. Scroll Progress Line

A 1px gold line at the very top of the viewport that fills left-to-right as the user scrolls.

**Spec:**
- `position: fixed; top: 0; left: 0; height: 1px; z-index: 9999`
- Colour: `var(--accent)` (#c8a96e)
- Width driven by JS: `scrollY / (scrollHeight - innerHeight) * 100`
- No transition delay — updates on every scroll event (passive listener)

---

## 3. Section Numbering

All section labels gain a small `—0N` number above them.

**Format:**
- `—01` Work  
- `—02` Photography  
- `—03` Writing  
- `—04` About  
- `—05` Contact

**Style:** `font-family: var(--font-mono); font-size: 9px; letter-spacing: 0.15em; color: var(--text-dim);` — sits on its own line directly above the `.section-label`.

---

## 4. Photography Section (NEW)

Horizontal scroll strip. Replaces the previously planned vertical grid.

**Structure:**
```html
<section id="photography">
  <div class="h-scroll-outer">   <!-- height: 400vh — controls scroll duration -->
    <div class="h-scroll-sticky"> <!-- position: sticky; height: 100vh -->
      <div class="h-scroll-header"> <!-- section number + label -->
      <div class="h-scroll-track">  <!-- flex row of .h-photo items -->
      <div class="h-progress-wrap"> <!-- thin progress bar + counter -->
    </div>
  </div>
</section>
```

**Photo items:**
- All `position: relative; overflow: hidden; flex-shrink: 0`
- Landscape (3:2): `height: calc(56vh - 32px); width: calc(height * 3/2)`
- Portrait (2:3): `height: calc(56vh - 32px); width: calc(height * 2/3)`
- Image: `object-fit: cover; filter: saturate(0.75) brightness(0.8); transform: scale(1.02)`
- Hover: `filter: saturate(0.92) brightness(0.96); transform: scale(1.05)`
- Overlay: gradient bottom-to-top, gold region label + italic caption slide up on hover

**Scroll mechanic:** JS `IntersectionObserver` + scroll listener maps vertical scroll progress (0→1) within the outer container to `translateX` on the track. Progress bar fills accordingly.

**Initial photo set (12 photos, all landscape unless noted):**

| File | Region | Caption |
|------|--------|---------|
| `afghanistan-gems-miners.jpg` | Afghanistan · Nuristan | Gem miners at break outside their mine |
| `L1001647.jpg` | Afghanistan | *(Kern to add caption)* |
| `ukraine-reactor-lede.jpg` | Ukraine · Kharkiv | Technicians ascending into the reactor hall |
| `afghanistan-forest-trees.jpg` | Afghanistan · Korengal Valley | Trees as livelihood in Wama district |
| `afghanistan-gems-mine-interior.jpg` | Afghanistan · Kunar | Inside Abid's tourmaline mine near Parun |
| `still-2.jpg` | *(Kern to confirm)* | *(Kern to add caption)* |
| `ukraine-reactor-core.jpg` | Ukraine · Kharkiv | Reactor core in darkness |
| `afghanistan-forest-loggers.jpg` | Afghanistan · Nuristan | Dilaram and Sanaullah, loggers in Wama |
| `L1001635.jpg` | Afghanistan | *(Kern to add caption)* |
| `afghanistan-forest-council.jpg` | Afghanistan · Korengal Valley | Tribal council meeting |
| `ukraine-reactor-crater.jpg` | Ukraine · Kharkiv | Crater from March 2022 rocket strike |
| `still-3.jpg` | *(Kern to confirm)* | *(Kern to add caption)* |

**Mobile:** On screens ≤900px, horizontal scroll section becomes a standard vertical grid — `h-scroll-outer` height becomes `auto`, `h-scroll-sticky` becomes `position: static; height: auto`, track uses `flex-wrap: wrap` with each photo at `width: calc(50% - 1px); aspect-ratio: 3/2`.

**Note:** The `<section id="photography">` element itself must have `padding: 0` — all internal spacing is handled by `.h-scroll-outer` and `.h-scroll-header`.

---

## 5. Writing Section (NEW)

Editorial article list. All 10 articles, sorted newest first.

**Structure:** `<section id="writing">` with `.article-list` containing `.article-row` anchors.

**Row layout (CSS Grid):** `140px 1fr 60px auto` — outlet · title · year · arrow

**Styles:**
- Outlet: `var(--font-mono); 9px; letter-spacing 0.18em; uppercase; color: var(--accent)`
- Title: `var(--font-display); italic; ~18px; color: var(--text-mid)` → `var(--text)` on hover
- Year: `var(--font-mono); 9px; color: var(--text-dim)`
- Arrow: slides in from left on hover (`translateX(-8px)` → `0`), opacity 0 → 1
- Row border: `1px solid var(--border)` top/bottom; brightens on hover
- Subtle gold background flash on hover (`rgba(200,169,110,0.03)`)

**Articles (in order):**

| Outlet | Title | Year | URL |
|--------|-------|------|-----|
| Al Jazeera | The Former Afghan Soldiers Turning to Gem Mining to Survive | 2023 | https://www.aljazeera.com/features/2023/8/18/the-former-afghan-soldiers-turning-to-gem-mining-to-survive |
| Al Jazeera | Women Working Inside an Afghan Chemical Lab Face Uncertain Future | 2022 | https://www.aljazeera.com/features/2022/8/19/women-working-inside-an-afghan-chemical-lab-face-uncertain-future |
| Undark | Keeping the Lights On at Ukraine's Research Nuclear Reactor | 2023 | https://undark.org/2023/05/01/keeping-the-lights-on-at-ukraines-research-nuclear-reactor/ |
| Undark | In Ukraine, Grain Shortages Reverberate Beyond Borders | 2023 | https://undark.org/2023/04/03/in-ukraine-grain-shortages-reverberate-beyond-borders/ |
| Scientific American | A Rare Glimpse into Afghanistan's Spectacular, Vanishing Forests | 2023 | https://www.scientificamerican.com/article/a-rare-glimpse-into-afghanistans-spectacular-vanishing-forests/ |
| Vice | Young Boys Work in Dangerous Mines As Afghanistan Prioritizes Coal | 2022 | https://www.vice.com/en/article/afghanistan-boys-coal-mining-taliban-child-labor/ |
| Vice | Look at These Photos of the Taliban's Endless Poppy Fields | 2022 | https://www.vice.com/en/article/afghanistan-photos-taliban-poppy-harvest-ban/ |
| Undark | In a Cold Winter, Afghans Struggle to Keep Warm | 2022 | https://undark.org/2022/04/20/in-a-cold-winter-afghans-struggle-to-keep-warm/ |
| Undark | The Wild Shrub at the Root of the Afghan Meth Epidemic | 2020 | https://undark.org/2020/05/20/afghanistan-meth-ephedra/ |
| Undark | For Afghan Health Workers, a Gauntlet of Making Do | 2019 | https://undark.org/2019/10/28/afghanistan-health-care-disparities/ |

---

## 6. Mobile Nav

Triggered at `max-width: 600px`. Currently nav links are `display: none`.

**Additions:**
- Hamburger button: 3 horizontal lines (1px, `var(--text-dim)`), top-right of nav, visible only on mobile
- Full-screen overlay: `position: fixed; inset: 0; background: rgba(10,10,8,0.97); z-index: 200`
- Overlay contains nav links stacked vertically in large DM Mono, plus ✕ close button
- Open/close toggled by JS class on `<body>` or overlay element
- Links: Work · Photography · Writing · About · Contact — each closes the menu on click

---

## 7. Portrait Placeholder (About Section)

Added to the top of `.about-left` (the sticky left column).

**Spec:**
- `width: 100%; aspect-ratio: 3/4`
- `background: var(--bg-3); border: 1px dashed rgba(200,169,110,0.25)`
- Centred label: `"Photo — coming soon"` in `var(--font-mono); 9px; color: var(--accent-dim)`
- `margin-bottom: 32px`
- When photo is supplied: replace entire `<div>` with `<img src="..." alt="Kern Hendricks" style="width:100%;aspect-ratio:3/4;object-fit:cover;">`

---

## 8. Open Graph Meta Tags

Added to `<head>`. All static — no dynamic generation needed.

```html
<meta property="og:title" content="Kern Hendricks — Documentary Filmmaker & Journalist">
<meta property="og:description" content="Documentary filmmaker, photojournalist and humanitarian analyst. Work published by Al Jazeera, Scientific American, DW Documentary, Foreign Policy, Undark.">
<meta property="og:type" content="website">
<meta property="og:url" content="https://kernhendricks.com">
<meta property="og:image" content="https://kernhendricks.com/photos/afghanistan-gems-miners.jpg">
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:site" content="@kernhendricks">
<meta name="twitter:title" content="Kern Hendricks — Documentary Filmmaker & Journalist">
<meta name="twitter:description" content="Documentary filmmaker, photojournalist and humanitarian analyst covering conflict, displacement and human rights.">
<meta name="twitter:image" content="https://kernhendricks.com/photos/afghanistan-gems-miners.jpg">
```

---

## 9. Nav Links Update

Add Photography and Writing to the nav:

```html
<li><a href="#work">Work</a></li>
<li><a href="#photography">Photography</a></li>
<li><a href="#writing">Writing</a></li>
<li><a href="#about">About</a></li>
<li><a href="#contact">Contact</a></li>
```

---

## What Is NOT Changing

- Hero section (no changes)
- Work/video grid (no changes)
- About body copy, tags, publications list (no changes)
- Contact section (no changes)
- Footer (no changes)
- Color tokens, typography, design system (no changes)
- File structure — everything stays in `index.html`

---

## Deployment

After implementation:
```bash
git add index.html photos/
git commit -m "add photography, writing sections; horizontal scroll; mobile nav; OG tags"
git push origin main
```

Site auto-deploys to kernhendricks.com via GitHub Pages (~60s lag).
