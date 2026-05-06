# Press strip + hero positioning — design

**Date:** 2026-05-06
**Scope:** index.html only. Adds a press logo strip between the hero and film grid; rewrites the hero eyebrow; adds entrance animation, scroll choreography, and a click-to-unmute affordance to the hero.

## Goals

Push the site's first impression from "documentary portfolio template" toward "directed experience that signals high-end doc work." Add the credibility handshake that doc commissioners scan for in 1.5 seconds (recognisable outlet logos), and give the hero the load-time and scroll choreography of a directed page rather than a stack of independent sections.

## Non-goals

- No framework or build process introduction (CLAUDE.md constraint)
- No splitting `index.html` into separate files (CLAUDE.md constraint)
- No pre-load intro overlay (previously rolled back; not revisiting in this change)
- No changes to the film grid, photography section, writing list, or About section copy
- The existing "Published in" text block in About is left untouched — the new press strip supplements rather than replaces it

## Changes

### 1. Hero eyebrow

Single text change inside `.hero-content`:

- `.hero-eyebrow` text changes from `"Documentary Filmmaker & Journalist"` to `"Film and reporting from the margins of crisis"`
- All existing typography (DM Mono, uppercase, tracking) stays the same
- No structural or CSS changes

### 2. Hero entrance animation

On first page load, the hero content elements ease in with a staggered sequence rather than appearing at once.

- Name (`.hero-name`): split into two lines ("Kern" / "Hendricks") that fade up with `translateY(12px)` → `translateY(0)`. Each line eases in on a tight stagger (~80ms between lines).
- Eyebrow (`.hero-eyebrow`): fades in ~280ms after the second name line begins.
- Scroll indicator: fades in ~600ms after the eyebrow begins.
- Total sequence completes within 1.2s.
- Implemented via CSS `@keyframes` + `animation-delay` on each element. No JS needed for the entrance.
- Wrapped in `@media (prefers-reduced-motion: no-preference)`. Under reduced motion, all elements appear at full opacity immediately.
- The showreel video continues to start playing the moment it can; the entrance animation runs over the top of it independently.

### 3. Click-to-unmute affordance on hero showreel

Small persistent UI element bottom-right of the hero (mirroring the bottom-centre scroll indicator), letting a viewer play the showreel with sound.

- Element: `<button class="hero-sound-toggle">` with two states — text reads "SOUND OFF" by default, "SOUND ON" when unmuted.
- Typography: DM Mono, 10px, uppercase, `var(--text-dim)` at rest, `var(--accent)` on hover/focus.
- Click toggles `video.muted` on the hero showreel `<video>` element. If unmuting and the video is paused (some browsers may pause on autoplay block), also calls `.play()` inside the same gesture handler so it plays with sound.
- Keyboard focusable. `aria-label` reflects current state ("Unmute showreel" / "Mute showreel"), updated on toggle.
- Position: absolutely positioned within the hero section. `bottom: 32px; right: 48px` desktop; `bottom: 24px; right: 24px` mobile. The existing scroll indicator stays at bottom-centre; the unmute toggle sits at bottom-right — they don't compete for visual space.
- A small icon (inline SVG speaker, monochrome) sits to the left of the text label. Toggles between speaker-muted and speaker-active glyphs to match the state.
- Hidden on touch-only devices? **No** — autoplay-with-sound is blocked but click-to-unmute works on mobile, and a doc commissioner reviewing on iPad benefits from this.

### 4. Press strip section

A new `<section id="press">` inserted in the page flow between `#hero` and `#work`.

- No section number, no `—NN` label, no "PRESS" heading. The strip is a quiet credibility row, not a destination section.
- Background: `var(--bg-2)` — slightly darker than the default page background, framing the strip as a distinct band without rules or borders.
- Vertical padding: 64px top/bottom desktop, 40px mobile. (Compare to the 120px standard section padding — this is a divider, not a destination.)
- Inner content: a `.press-row` flex container, `justify-content: space-between`, `align-items: center`, centred within the standard `.container` max-width.
- Five SVG logos in this order:
  1. Al Jazeera
  2. Scientific American
  3. DW Documentary
  4. Foreign Policy
  5. ProPublica
- Each logo wrapped in a non-clickable `<div class="press-logo">` with `aria-label` carrying the outlet name and `role="img"`. SVG marked `aria-hidden="true"` (the parent's aria-label provides the accessible name).
- Logos rendered as inline SVG with `fill: currentColor`. The wrapper sets `color: var(--text-dim)` at rest. No hover state (logos are non-interactive).
- **Optical sizing:** logos are NOT all rendered at the same height. Each one is sized so it reads at matched optical weight against its neighbours. Target heights:
  - Al Jazeera: ~26px (dense calligraphic mark, lower height to match optical weight)
  - Scientific American: ~22px (long wordmark, lower height to fit horizontally)
  - DW Documentary: ~28px (DW circular mark + "Documentary" wordmark lockup)
  - Foreign Policy: ~30px (light serif wordmark, taller to compensate for visual weight)
  - ProPublica: ~24px (medium-density wordmark)
- Heights drop to ~80% of desktop values below the 900px breakpoint.
- Below the 600px breakpoint: row wraps via `flex-wrap: wrap` with `gap: 24px 32px`. Logos retain their optical heights; natural wrap will produce roughly 3 + 2 (with Al Jazeera, Sci-Am, DW on the first line and Foreign Policy + ProPublica on the second), but exact wrap point is determined by available width. No fixed grid — flex wrap handles it.

### 5. Hero → press handoff (scroll choreography)

The press strip enters the viewport feeling like the hero is *handing off* to it, rather than appearing as an independent section.

- Mechanism: a single scroll listener (folded into the existing rAF-throttled scroll handler) computes the press strip's offset from the hero bottom. As the user scrolls past the hero:
  - The hero eyebrow continues its existing parallax recession (35% scroll speed). No change.
  - The press strip enters with `opacity: 0` and `translateY(20px)`. Its reveal begins when the top of the strip element is within `viewport_height + 100px` from the top of the viewport (i.e. it's about to enter from below, ~100px before its top edge actually crosses the bottom of the screen). Reaches full opacity / `translateY(0)` over a 600ms ease-out.
  - Critically: the strip's reveal *begins* while the hero eyebrow is still visibly receding above the fold. The two motions overlap by ~200ms, creating the handoff feel.
- Implementation: on top of the existing `.section-fade` IntersectionObserver pattern, add a class `.press-handoff` to the strip. The handoff timing is controlled via the scroll listener (not the IntersectionObserver) so we can tie the entrance to the hero's exit progress, not just to viewport intersection.
- Under `prefers-reduced-motion`, the strip is fully visible immediately when scrolled into view (no translate, no fade delay).

## SVG sourcing approach

All five logos sourced and inlined directly in `index.html` as SVG markup (preserving the "single file, no build" constraint).

- **Al Jazeera:** official wordmark, recreated as SVG path data. The English-language Al Jazeera wordmark is the cleanest version for this audience.
- **Scientific American:** typographic wordmark. Available via Wikimedia Commons or recreatable from their masthead.
- **DW Documentary:** the DW circular mark ("DW" inside a square/circle) is the recognisable element. The full lockup is "DW Documentary"; for clarity at small sizes, render the DW mark + "Documentary" set in DM Sans weight 500 to its right. This keeps the lockup readable without using the heavy-stroked DW marketing lockup that doesn't shrink well.
- **Foreign Policy:** the FP magazine wordmark is essentially "Foreign Policy" set in their custom serif. Render as type using a close serif (Cormorant Garamond is on the page already; in semi-bold it's a near match) with "FOREIGN POLICY" in tracked uppercase.
- **ProPublica:** wordmark, recreatable as type or sourceable from their press kit. Their wordmark is essentially custom letterforms — closest approximation in a clean sans serif works.

For each: monochrome, single-path-or-group SVGs that respect `currentColor`, optimised (no Inkscape metadata, no unnecessary precision).

## Performance budget

- New SVGs inlined: estimated ~6–10 KB total uncompressed, ~2–3 KB after gzip.
- New CSS: estimated ~80–120 lines, ~3 KB.
- New JS: minimal — the click-to-unmute handler (~15 lines), the hero entrance animation is pure CSS, the scroll handoff is added inside the existing rAF-throttled scroll listener (no new listener registered).
- No new fonts, no new external requests.

## Accessibility

- Press strip: each logo has an `aria-label` with the outlet name; the SVG itself is `aria-hidden`.
- Hero entrance animation: `prefers-reduced-motion` respected — content visible immediately, no animation.
- Press strip handoff: `prefers-reduced-motion` respected — visible immediately on scroll-in, no transform.
- Click-to-unmute: keyboard accessible (`<button>`), `aria-label` reflects current state, `:focus-visible` outline matches the rest of the site.

## Out of scope

- Pre-load intro overlay (split from this change — user previously rolled this back)
- Adding/changing existing film items or photography
- Any About section copy or layout changes
- Mobile-specific redesign of the press strip beyond responsive scaling (the 2+2+1 wrap below 600px is the only mobile-specific behaviour)
