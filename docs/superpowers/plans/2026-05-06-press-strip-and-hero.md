# Press Strip + Hero Polish Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add a quiet press logo row between hero and film grid, rewrite the hero eyebrow, refine the hero name entrance animation, choreograph the hero → press scroll handoff, and add a click-to-unmute control to the hero showreel.

**Architecture:** All changes in a single file (`index.html`). New SVG markup inlined directly. CSS additions follow the existing token + variable system. JS additions fold into the existing rAF-throttled scroll listener and add one small click handler. No new dependencies, no build step.

**Tech Stack:** Plain HTML5, CSS (custom properties already defined in `:root`), vanilla JS, inline SVG. Hosted on GitHub Pages with custom domain.

**Reference spec:** `docs/superpowers/specs/2026-05-06-press-strip-and-hero-design.md`

**Verification approach:** This codebase has no automated test framework — by design (no build, no dependencies). Verification for every task means: run `python3 -m http.server 8080` from the project root, open `http://localhost:8080` in a browser, and perform the explicit visual/behavioural checks listed in each task. Use Chrome DevTools' device toolbar for responsive checks (set viewport widths exactly).

---

## File Structure

All changes are to `index.html` only. The file is intentionally a single-file site (per `CLAUDE.md`); we do NOT split it.

The changes group into four areas of the file:
- **`<style>` block (lines ~27–1020):** new CSS for press strip, hero name split, scroll handoff state classes, click-to-unmute affordance
- **Hero markup (lines ~1055–1073):** name split, eyebrow text change, click-to-unmute button, sound icon SVGs
- **New `<section id="press">` (insert after hero, before `#work` at line ~1075):** press strip section with 5 inline SVG logos
- **`<script>` block (after line ~1490):** add `updatePressHandoff()` to the scroll listener; add click-to-unmute handler

---

## Task 1: Hero eyebrow text rewrite

**Files:**
- Modify: `index.html:1066`

- [ ] **Step 1: Change the eyebrow text**

Find the current line:

```html
<p class="hero-eyebrow">Documentary Filmmaker &amp; Journalist</p>
```

Replace with:

```html
<p class="hero-eyebrow">Film and reporting from the margins of crisis</p>
```

- [ ] **Step 2: Visual verify in browser**

Run: `python3 -m http.server 8080` from project root (if not already running).
Open: `http://localhost:8080`
Verify:
- Eyebrow text reads "FILM AND REPORTING FROM THE MARGINS OF CRISIS" (uppercase via existing CSS)
- Same gold colour, same DM Mono typography, same letter-spacing as before
- Sits beneath "Kern Hendricks", entrance fade-up still works

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: rewrite hero eyebrow to position film + reporting beat"
```

---

## Task 2: Split hero name into two staggered lines

**Files:**
- Modify: `index.html:359–372` (CSS for `.hero-name`)
- Modify: `index.html:1065` (HTML for `.hero-name`)

The existing `.hero-name` is a single `<h1>` with `<br>` separating "Kern" and "Hendricks", animated as one block. We want each line on its own animated element, with a tight stagger so "Hendricks" lands ~140ms after "Kern".

- [ ] **Step 1: Replace the name HTML with two animated spans**

Find current line 1065:

```html
<h1 class="hero-name">Kern<br><em>Hendricks</em></h1>
```

Replace with:

```html
<h1 class="hero-name">
  <span class="hero-name-line hero-name-first">Kern</span>
  <span class="hero-name-line hero-name-second"><em>Hendricks</em></span>
</h1>
```

- [ ] **Step 2: Update the `.hero-name` CSS — remove block-level animation, add per-line animations**

Find current block at lines 359–372:

```css
.hero-name {
  font-family: var(--font-display);
  font-size: clamp(64px, 9vw, 130px);
  font-weight: 300;
  line-height: 0.92;
  letter-spacing: -0.01em;
  color: var(--text);
  opacity: 0;
  animation: fadeUp 1.2s var(--ease-out-expo) 0.2s forwards;
}
.hero-name em {
  font-style: italic;
  color: var(--text-mid);
}
```

Replace with:

```css
.hero-name {
  font-family: var(--font-display);
  font-size: clamp(64px, 9vw, 130px);
  font-weight: 300;
  line-height: 0.92;
  letter-spacing: -0.01em;
  color: var(--text);
}
.hero-name-line {
  display: block;
  opacity: 0;
  animation: fadeUp 1.2s var(--ease-out-expo) forwards;
}
.hero-name-first  { animation-delay: 0.2s; }
.hero-name-second { animation-delay: 0.34s; }
.hero-name em {
  font-style: italic;
  color: var(--text-mid);
}

@media (prefers-reduced-motion: reduce) {
  .hero-name-line {
    opacity: 1;
    animation: none;
  }
}
```

Reasoning: 140ms stagger (0.20s → 0.34s) is tight enough to feel like one motion but lets the eye register "Kern" before "Hendricks" (italic) lands.

- [ ] **Step 3: Visual verify in browser**

Run: refresh `http://localhost:8080` (hard refresh: Cmd+Shift+R)
Verify on first load:
- "Kern" fades up first, "Hendricks" follows visibly but tightly behind
- Italic "Hendricks" still styled correctly (warm grey via `--text-mid`)
- Eyebrow + scroll indicator still fade in afterwards in their existing sequence
- Total entrance still completes within ~1.5s

Reduced motion check (Chrome DevTools → Rendering tab → "Emulate CSS prefers-reduced-motion: reduce"):
- Both lines visible immediately at full opacity, no animation

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: stagger hero name lines for more deliberate entrance"
```

---

## Task 3: Source and inline 5 outlet SVG logos

**Files:**
- No file changes yet — this task produces a working scratchpad of cleaned SVG markup that Task 4 will paste in.

This is the fiddly task. Each logo needs to be (a) sourced cleanly, (b) optimised, (c) made monochrome (`fill="currentColor"`), (d) viewBox-correct so heights scale predictably.

- [ ] **Step 1: Source Al Jazeera English wordmark**

Approach: Wikimedia Commons has a clean SVG of the Al Jazeera English logo. URL pattern: `https://commons.wikimedia.org/wiki/File:Aljazeera.svg` (or similar — search "Al Jazeera English logo SVG").

Process the SVG:
1. Strip Inkscape metadata, sodipodi attributes, `xmlns:` extras beyond the standard `xmlns="http://www.w3.org/2000/svg"`
2. Replace any `fill="#xxxxxx"` with `fill="currentColor"`
3. Verify viewBox is set (e.g. `viewBox="0 0 200 60"`); if absent, compute from path bounds and add
4. Save the cleaned SVG markup to a scratch file or note it directly into the next task

Expected: a `<svg>` element ~60–200 lines depending on path complexity.

- [ ] **Step 2: Source Scientific American wordmark**

Approach: Wikimedia Commons usually has it. Search "Scientific American logo SVG site:commons.wikimedia.org". The wordmark is the long "SCIENTIFIC AMERICAN" set in their custom serif. If a clean SVG isn't available, the wordmark is recreatable as `<text>` SVG using a serif font, but prefer a vector path version.

Process: same cleaning steps as Task 3 Step 1.

- [ ] **Step 3: Source DW Documentary lockup**

Approach: per the spec, render this as the standard DW (Deutsche Welle) circular mark + the word "Documentary" set in DM Sans alongside.

Build the lockup as a single SVG:

```html
<svg class="press-svg" viewBox="0 0 220 36" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
  <!-- DW square mark (recreate from the official DW logo: blue square with white "DW", but in monochrome here -->
  <rect x="0" y="0" width="36" height="36" fill="currentColor"/>
  <text x="18" y="25" text-anchor="middle" fill="var(--bg-2)" font-family="DM Sans, sans-serif" font-weight="700" font-size="18">DW</text>
  <!-- "Documentary" word in matching site type -->
  <text x="48" y="25" fill="currentColor" font-family="DM Sans, sans-serif" font-weight="500" font-size="16" letter-spacing="0.02em">Documentary</text>
</svg>
```

Note: the DW square is filled with `currentColor` (will be `--text-dim`), and the "DW" letters inside are filled with `var(--bg-2)` to invert against the square. This keeps the mark monochromatic-aware.

Verify: the lockup reads "[DW] Documentary" — the square mark with DW visible inside it, then the word Documentary.

- [ ] **Step 4: Source Foreign Policy wordmark**

Approach: per the spec, render as type — Foreign Policy's wordmark is essentially their custom serif setting "FOREIGN POLICY". Use Cormorant Garamond (already loaded on the page) at semi-bold weight.

Build as SVG:

```html
<svg class="press-svg" viewBox="0 0 240 32" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
  <text x="120" y="22" text-anchor="middle" fill="currentColor" font-family="Cormorant Garamond, serif" font-weight="600" font-size="22" letter-spacing="0.08em">FOREIGN POLICY</text>
</svg>
```

- [ ] **Step 5: Source ProPublica wordmark**

Approach: ProPublica's wordmark is a custom letterform but reads cleanly as "ProPublica" set in a heavy sans serif. Try Wikimedia Commons first; if no clean SVG, recreate as type using DM Sans heavy weight.

Recreated version:

```html
<svg class="press-svg" viewBox="0 0 200 32" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
  <text x="100" y="24" text-anchor="middle" fill="currentColor" font-family="DM Sans, sans-serif" font-weight="700" font-size="24" letter-spacing="-0.01em">ProPublica</text>
</svg>
```

- [ ] **Step 6: Pause and verify SVG markup before integration**

Before pasting into `index.html`, verify each SVG:
- Has `viewBox` attribute
- Uses `currentColor` (or `var(--bg-2)` for the inverted DW letters)
- Has no Inkscape/Adobe metadata
- Total combined size of the 5 SVG strings is under ~10 KB uncompressed

No commit at the end of this task — Task 3's output is markup that Task 4 will paste in. Do not commit a scratch file.

---

## Task 4: Press strip — HTML structure + base CSS

**Files:**
- Modify: `index.html` — insert new `<section>` between the closing `</section>` of `#hero` (line 1073) and the opening `<section id="work">` (line 1076)
- Modify: `index.html` `<style>` block — add new CSS rules near the end of the section-specific blocks, before responsive media queries

- [ ] **Step 1: Insert the press strip section markup**

After line 1073 (the `</section>` that closes `#hero`) and before line 1075 (the `<!-- ── WORK ── -->` comment), insert:

```html
  <!-- ── PRESS ── -->
  <section id="press" class="section-fade press-section">
    <div class="container press-row">
      <div class="press-logo press-logo--aljazeera" role="img" aria-label="Al Jazeera">
        <!-- PASTE Al Jazeera SVG from Task 3 Step 1 here -->
      </div>
      <div class="press-logo press-logo--sciam" role="img" aria-label="Scientific American">
        <!-- PASTE Scientific American SVG from Task 3 Step 2 here -->
      </div>
      <div class="press-logo press-logo--dw" role="img" aria-label="DW Documentary">
        <!-- PASTE DW Documentary SVG from Task 3 Step 3 here -->
      </div>
      <div class="press-logo press-logo--fp" role="img" aria-label="Foreign Policy">
        <!-- PASTE Foreign Policy SVG from Task 3 Step 4 here -->
      </div>
      <div class="press-logo press-logo--propublica" role="img" aria-label="ProPublica">
        <!-- PASTE ProPublica SVG from Task 3 Step 5 here -->
      </div>
    </div>
  </section>
```

Each `<!-- PASTE … -->` comment gets replaced with the actual cleaned SVG markup from Task 3.

- [ ] **Step 2: Add base CSS for the press strip**

Find a location in the `<style>` block after the section shared rules (around line 444, after `.section-fade.visible`) and before `/* ── PHOTOGRAPHY (HORIZONTAL SCROLL) ── */`. Insert:

```css
/* ── PRESS STRIP ── */
.press-section {
  background: var(--bg-2);
  padding: 64px 48px;
  max-width: none;
  margin: 0;
}
.press-row {
  max-width: 1400px;
  margin: 0 auto;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 32px;
  flex-wrap: wrap;
}
.press-logo {
  color: var(--text-dim);
  display: flex;
  align-items: center;
  flex-shrink: 0;
}
.press-logo svg {
  display: block;
  width: auto;
}
/* Optical sizing — heights tuned per logo so they read at matched optical weight */
.press-logo--aljazeera svg  { height: 26px; }
.press-logo--sciam svg      { height: 22px; }
.press-logo--dw svg         { height: 28px; }
.press-logo--fp svg         { height: 30px; }
.press-logo--propublica svg { height: 24px; }

@media (max-width: 900px) {
  .press-section { padding: 48px 32px; }
  .press-row { gap: 24px 32px; justify-content: center; }
  .press-logo--aljazeera svg  { height: 21px; }
  .press-logo--sciam svg      { height: 18px; }
  .press-logo--dw svg         { height: 22px; }
  .press-logo--fp svg         { height: 24px; }
  .press-logo--propublica svg { height: 19px; }
}
@media (max-width: 600px) {
  .press-section { padding: 40px 24px; }
}
```

Note: `#press` overrides the default `section { padding: 120px 48px; max-width: 1400px }` rule because the strip is a divider band, not a destination section.

- [ ] **Step 3: Visual verify at desktop width (1440px)**

Hard-refresh `http://localhost:8080` at desktop viewport.
Verify:
- Press strip appears between hero and film grid
- Background is visibly slightly darker than the rest of the page (subtle but perceptible)
- 5 logos in a row, evenly distributed via `space-between`
- Each logo monochrome, in the warm grey `--text-dim` colour
- DW Documentary lockup reads correctly (DW square + Documentary text)
- Foreign Policy reads in serif type, ProPublica reads in heavier sans
- Strip enters with a fade-in (existing `.section-fade` behaviour)

If any logo reads visually too light or too heavy compared to its neighbours, note it for Task 5.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: add press logo strip between hero and film grid"
```

---

## Task 5: Optical sizing pass + responsive verification

**Files:**
- Modify: `index.html` `<style>` block — adjust `.press-logo--*` heights

- [ ] **Step 1: Compare logos side by side at desktop width**

Open `http://localhost:8080` at 1440px. Squint at the press strip — the test for optical balance is whether your eye lands evenly across the row, or whether one logo is "louder" than the others.

If a logo dominates: reduce its height by 1–2px in the CSS.
If a logo recedes: increase its height by 1–2px.
Keep the desktop range within 22–32px.

Record actual final heights, then update the CSS values from Task 4 Step 2 to match.

- [ ] **Step 2: Test at intermediate viewport (900–1100px)**

Use Chrome DevTools device toolbar at 1024px and 900px.
Verify:
- Logos still in a single row at 1024px
- At 900px exactly, the smaller height set kicks in (should be visible step-down)
- No logo overflows or wraps prematurely

- [ ] **Step 3: Test at mobile viewport (600px and below)**

Set viewport to 414px (iPhone landscape range), then 375px (iPhone standard).
Verify:
- Logos wrap via `flex-wrap` to multiple lines
- Likely 3 + 2 layout (Al Jazeera, Sci-Am, DW on first row; FP, ProPublica on second) — exact split depends on natural widths
- All logos still legible
- Strip padding shrinks to 40px top/bottom at ≤600px

If wrap creates an awkward layout (e.g. 4 + 1 with one orphan), adjust `.press-row` `gap` values or logo heights at the relevant breakpoint to rebalance.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "tune: optical sizing pass on press strip logos"
```

---

## Task 6: Hero → press scroll handoff choreography

**Files:**
- Modify: `index.html` `<style>` block — add `.press-section` initial / handoff state classes
- Modify: `index.html` `<script>` block — add `updatePressHandoff()` to the existing rAF scroll listener

The existing `.section-fade` class triggers via IntersectionObserver. We need a *scroll-progress-driven* reveal that overlaps with the hero's existing parallax exit, not a binary in-view trigger. We'll override `.section-fade` for `#press` specifically.

- [ ] **Step 1: Add CSS for press strip handoff state**

In the `<style>` block, after the `.press-section` rules added in Task 4 Step 2, add:

```css
/* Press strip handoff: scroll-progress-driven entrance overrides the .section-fade default */
#press.section-fade {
  opacity: 0;
  transform: translateY(20px);
  transition: opacity 0.6s var(--ease-out-expo), transform 0.6s var(--ease-out-expo);
}
#press.press-revealed {
  opacity: 1;
  transform: translateY(0);
}

@media (prefers-reduced-motion: reduce) {
  #press.section-fade {
    opacity: 1;
    transform: none;
    transition: none;
  }
}
```

Reasoning: `#press` already has `.section-fade` from Task 4. We override the default `.section-fade.visible` trigger by using a custom class `.press-revealed` driven from scroll position rather than IntersectionObserver. The `.section-fade.visible` state will still apply (set by the existing observer in the `<script>` block), but our scroll-driven `.press-revealed` will reach the same final state earlier in the scroll.

- [ ] **Step 2: Add `updatePressHandoff()` to the scroll listener**

Find the existing scroll listener in the `<script>` block (line 1495–1508):

```js
let scrollTicking = false;
function onScroll() {
  if (!scrollTicking) {
    requestAnimationFrame(() => {
      updateProgress();
      updateHScroll();
      updateNav();
      updateActiveNav();
      updateHeroParallax();
      scrollTicking = false;
    });
    scrollTicking = true;
  }
}
```

Add a call to a new `updatePressHandoff()` inside the rAF callback, after `updateHeroParallax()`:

```js
let scrollTicking = false;
function onScroll() {
  if (!scrollTicking) {
    requestAnimationFrame(() => {
      updateProgress();
      updateHScroll();
      updateNav();
      updateActiveNav();
      updateHeroParallax();
      updatePressHandoff();
      scrollTicking = false;
    });
    scrollTicking = true;
  }
}
```

Then, near the existing `updateHeroParallax()` definition (around line 1518–1531), add the new function:

```js
// ── Press strip scroll handoff ──
const pressEl = document.getElementById('press');
function updatePressHandoff() {
  if (!pressEl) return;
  const rect = pressEl.getBoundingClientRect();
  // Reveal when the strip's top is within (viewport_height + 100px) from top of viewport,
  // i.e. ~100px before its top edge crosses the bottom of the viewport.
  const triggerY = window.innerHeight + 100;
  if (rect.top < triggerY) {
    pressEl.classList.add('press-revealed');
  }
}
// Run once on load in case the strip is visible at initial scroll position (it shouldn't be, but defensive)
updatePressHandoff();
```

- [ ] **Step 3: Visual verify the handoff feel**

Hard-refresh `http://localhost:8080`. Scroll slowly from the top.
Verify:
- The hero text recedes upward as you scroll (existing behaviour, unchanged)
- Before the press strip's top edge actually appears at the bottom of the viewport, it has already begun fading + translating in
- The motion of the hero exiting and the strip entering overlap visually — they are part of the same scroll moment, not two separate events
- By the time the strip is fully on screen, it's at full opacity and `translateY(0)`
- The strip stays revealed if you scroll back up to the hero (no flicker)

Reduced motion check (DevTools → Rendering → "Emulate prefers-reduced-motion: reduce"):
- Strip is visible immediately at full opacity once it enters the viewport, no fade or translate

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: scroll-progress-driven handoff between hero and press strip"
```

---

## Task 7: Click-to-unmute affordance on hero showreel

**Files:**
- Modify: `index.html` hero markup — add `<button class="hero-sound-toggle">` inside `#hero`
- Modify: `index.html` `<style>` block — add `.hero-sound-toggle` CSS
- Modify: `index.html` `<script>` block — add toggle handler

- [ ] **Step 1: Add button markup to the hero section**

Inside `<section id="hero">`, after the closing `</div>` of `<div class="scroll-indicator">` (around line 1072) and before the closing `</section>` of `#hero`, add:

```html
    <button id="heroSoundToggle" class="hero-sound-toggle" aria-label="Unmute showreel" type="button">
      <svg class="hero-sound-icon" width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
        <!-- Speaker muted (default state) -->
        <polygon points="11 5 6 9 2 9 2 15 6 15 11 19 11 5"></polygon>
        <line x1="23" y1="9" x2="17" y2="15"></line>
        <line x1="17" y1="9" x2="23" y2="15"></line>
      </svg>
      <span class="hero-sound-label">Sound off</span>
    </button>
```

- [ ] **Step 2: Add CSS for the toggle**

In the `<style>` block, after the existing hero-related rules and before the `/* ── SECTIONS SHARED ── */` block (around line 400), add:

```css
.hero-sound-toggle {
  position: absolute;
  z-index: 3;
  bottom: 32px;
  right: 48px;
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 8px 12px;
  background: transparent;
  border: 1px solid var(--border);
  color: var(--text-dim);
  font-family: var(--font-mono);
  font-size: 10px;
  letter-spacing: 0.2em;
  text-transform: uppercase;
  cursor: pointer;
  transition: color 0.3s, border-color 0.3s;
}
.hero-sound-toggle:hover,
.hero-sound-toggle:focus-visible {
  color: var(--accent);
  border-color: var(--border-hi);
}
.hero-sound-toggle:focus-visible {
  outline: 2px solid var(--accent);
  outline-offset: 4px;
}
.hero-sound-icon {
  flex-shrink: 0;
}

@media (max-width: 600px) {
  .hero-sound-toggle {
    bottom: 24px;
    right: 24px;
    padding: 6px 10px;
    font-size: 9px;
  }
}
```

- [ ] **Step 3: Add toggle handler to the script block**

In the `<script>` block, after the existing hero parallax setup (after line 1531), add:

```js
// ── Hero showreel mute toggle ──
const heroVideo       = document.querySelector('.reel-bg video');
const heroSoundBtn    = document.getElementById('heroSoundToggle');
const heroSoundLabel  = heroSoundBtn ? heroSoundBtn.querySelector('.hero-sound-label') : null;
const heroSoundIcon   = heroSoundBtn ? heroSoundBtn.querySelector('.hero-sound-icon') : null;

const ICON_MUTED = `
  <polygon points="11 5 6 9 2 9 2 15 6 15 11 19 11 5"></polygon>
  <line x1="23" y1="9" x2="17" y2="15"></line>
  <line x1="17" y1="9" x2="23" y2="15"></line>
`;
const ICON_UNMUTED = `
  <polygon points="11 5 6 9 2 9 2 15 6 15 11 19 11 5"></polygon>
  <path d="M15.54 8.46a5 5 0 0 1 0 7.07"></path>
  <path d="M19.07 4.93a10 10 0 0 1 0 14.14"></path>
`;

if (heroSoundBtn && heroVideo) {
  heroSoundBtn.addEventListener('click', () => {
    const willUnmute = heroVideo.muted;
    heroVideo.muted = !willUnmute ? true : false;
    if (willUnmute) {
      heroVideo.play().catch(() => {});
      heroSoundLabel.textContent = 'Sound on';
      heroSoundBtn.setAttribute('aria-label', 'Mute showreel');
      if (heroSoundIcon) heroSoundIcon.innerHTML = ICON_UNMUTED;
    } else {
      heroSoundLabel.textContent = 'Sound off';
      heroSoundBtn.setAttribute('aria-label', 'Unmute showreel');
      if (heroSoundIcon) heroSoundIcon.innerHTML = ICON_MUTED;
    }
  });
}
```

- [ ] **Step 4: Visual + behaviour verify**

Hard-refresh `http://localhost:8080`.
Verify:
- "SOUND OFF" pill visible at bottom-right of hero, with speaker-muted icon
- Hover: text + border shift to gold
- Click once: label changes to "SOUND ON", icon changes to speaker-with-waves, audio of the showreel becomes audible
- Click again: returns to muted state
- Keyboard: tab to the button, hit Enter/Space — same toggle works
- Focus state: gold outline visible
- Mobile (375px viewport): button is smaller and tucked tighter to the corner, still tappable

Edge case to verify: scroll down past hero, scroll back up — the showreel video keeps playing (autoplay loop), and the button still toggles audio correctly.

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: click-to-unmute affordance on hero showreel"
```

---

## Task 8: prefers-reduced-motion sweep

**Files:**
- Modify: `index.html` `<style>` block (verification pass — may add small additions)

This task verifies all new motion respects `prefers-reduced-motion`. Most overrides were added in earlier tasks; this is the consolidation + audit pass.

- [ ] **Step 1: Audit the new motion**

The new motion introduced by this plan:
1. Hero name two-line stagger (Task 2) — has reduced-motion override ✓
2. Press strip handoff fade + translate (Task 6) — has reduced-motion override ✓
3. Click-to-unmute hover transitions — these are user-interaction transitions, not autoplay motion. `prefers-reduced-motion` typically does NOT require disabling hover transitions, but for consistency we can shorten them.

For consistency with other interaction transitions in the codebase (the existing nav, etc. don't disable hover transitions under reduced-motion), leave the hover transitions as-is.

- [ ] **Step 2: Verify reduced-motion behaviour end-to-end**

Chrome DevTools → Rendering → "Emulate prefers-reduced-motion: reduce". Hard-refresh `http://localhost:8080`.
Verify:
- Hero loads with name + eyebrow + scroll indicator all visible immediately, no fade-up animation
- Scroll down — press strip appears at full opacity / no translate when it enters the viewport
- All other site behaviour (existing parallax, scroll progress line, etc.) unchanged — these were NOT in scope and still work as before

- [ ] **Step 3: Verify normal motion still works**

Disable the reduced-motion emulation (set Rendering → "No emulation" or "no-preference"). Hard-refresh.
Verify:
- All animations from earlier tasks fire as expected

- [ ] **Step 4: Commit (only if any changes were needed in this task)**

If you needed to add or adjust any reduced-motion rules in Step 1:

```bash
git add index.html
git commit -m "fix: tighten reduced-motion handling for new hero/press motion"
```

If no changes were needed (the previous tasks already covered it correctly), skip the commit and move on.

---

## Task 9: Cross-viewport visual verification

**Files:**
- No file changes expected — this is a final verification task

Goal: catch any breakage at common breakpoints before declaring done.

- [ ] **Step 1: Test at desktop sizes**

Viewport widths to test in DevTools device toolbar:
- 1920px (large desktop)
- 1440px (standard desktop)
- 1280px (small desktop)
- 1024px (tablet landscape)

For each, hard-refresh and check:
- Hero entrance animation lands correctly
- Press strip displays in single row at desktop sizes (1280+); may begin to wrap below 1100px depending on logo widths
- Click-to-unmute button stays in bottom-right of hero, doesn't overlap scroll indicator

- [ ] **Step 2: Test at tablet/mobile sizes**

Viewport widths:
- 900px (defined breakpoint)
- 768px (tablet portrait)
- 600px (defined breakpoint)
- 414px (large phone)
- 375px (standard phone)

For each:
- Press strip wraps as expected (3 + 2 below 600px is the target)
- Press strip padding shrinks correctly
- Hero unmute button shrinks at ≤600px and stays accessible
- Hero name still legible (existing `clamp()` handles this)

- [ ] **Step 3: Test on a real device if available**

Pull up `http://[your-mac-ip]:8080` on an iPhone or iPad on the same network. Verify:
- Hero showreel autoplays (muted, as expected)
- Tapping unmute button unmutes audio (gesture-driven, allowed by browsers)
- Press strip readable, not crammed
- No layout regressions in film grid, photography section, writing list, or about

- [ ] **Step 4: Lighthouse / network sanity check (optional but recommended)**

Open Chrome DevTools → Lighthouse → Run audit (Performance, Accessibility, Best Practices).
Compare to a baseline run from main:
- Performance score should not drop more than 2 points
- Accessibility score should be unchanged or improved (we added aria-labels)
- No new console errors or warnings

- [ ] **Step 5: Final commit if any tweaks were needed**

If Step 1–3 revealed issues that needed fixing (e.g. wrap layout adjustment, button overlap):

```bash
git add index.html
git commit -m "fix: <describe specific issue addressed>"
```

If everything passed, no commit needed.

---

## Task 10: Update CLAUDE.md to reflect the new section

**Files:**
- Modify: `CLAUDE.md` — add the press section to the documented page flow + file structure

The site documentation lists current sections and design patterns; we should keep it current.

- [ ] **Step 1: Update the Page Flow section**

Find the "Page Flow" section in CLAUDE.md (search for `Hero → Film`):

```
Hero → Film (—01) → Photography (—02) → Writing (—03) → About (—04)
```

Replace with:

```
Hero → Press Strip → Film (—01) → Photography (—02) → Writing (—03) → About (—04)
```

- [ ] **Step 2: Add a Press Strip subsection under Design System / Key UI Patterns**

Find the "Key UI Patterns" list and add a new bullet describing the press strip:

```markdown
- **Press strip** — quiet credibility row between hero and film grid. Five inline SVG outlet wordmarks (Al Jazeera, Scientific American, DW Documentary, Foreign Policy, ProPublica) on a slightly-darker `--bg-2` band. Optically sized (each logo gets a custom height to read at matched optical weight; not all the same height). Non-clickable, decorative. Reveals via scroll-progress handoff from the hero (overlaps with the hero's parallax exit) rather than the standard `.section-fade` IntersectionObserver trigger.
```

- [ ] **Step 3: Add a Hero unmute affordance bullet to Key UI Patterns**

Add another bullet:

```markdown
- **Hero showreel unmute** — small "SOUND OFF / SOUND ON" toggle button at bottom-right of hero (`#heroSoundToggle`). Click toggles the showreel video's `muted` property and updates the speaker icon + label. Keyboard-accessible. Mobile users can tap to unmute (allowed by browsers as a user gesture).
```

- [ ] **Step 4: Commit**

```bash
git add CLAUDE.md
git commit -m "docs: document new press strip and hero unmute control in CLAUDE.md"
```

---

## Self-review checklist (run after writing the plan, before execution)

- [ ] Spec coverage: every spec section has a task. Hero eyebrow rewrite (Task 1), name stagger (Task 2), SVG sourcing (Task 3), press strip HTML/CSS (Task 4), optical sizing (Task 5), scroll handoff (Task 6), click-to-unmute (Task 7), reduced-motion (Task 8), responsive verification (Task 9), docs (Task 10).
- [ ] No placeholders: no "TBD", no "implement later", no "similar to previous task"
- [ ] Type/name consistency: `#press`, `.press-section`, `.press-row`, `.press-logo`, `.press-revealed`, `#heroSoundToggle`, `.hero-sound-toggle`, `.hero-sound-icon`, `.hero-sound-label`, `.hero-name-line`, `.hero-name-first`, `.hero-name-second` — all consistent across tasks
- [ ] Each task ends in a commit (or explicit no-commit if part of a sourcing scratchpad)
