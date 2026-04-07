# Portfolio Site — Full Build Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add photography (horizontal scroll), writing (article list), mobile nav, section fades, scroll progress line, section numbering, portrait placeholder, and OG meta tags to `index.html`.

**Architecture:** Single-file HTML/CSS/JS. All changes go into `index.html` except photo assets in `photos/`. CSS additions go in the `<style>` block. JS additions go in the `<script>` block at the bottom. New sections are inserted between `#work` and `#about`.

**Tech Stack:** Plain HTML5, CSS3 (custom properties, CSS Grid, Flexbox, sticky positioning), vanilla JS (IntersectionObserver, scroll events). No build process.

---

## File Map

| File | Action | What changes |
|------|--------|--------------|
| `index.html` | Modify | All CSS, HTML, JS changes |
| `photos/` | Modify | Rename 2 still files to clean names |

---

## Task 1: Rename still files and verify photos directory

**Files:**
- Modify: `photos/` (rename 2 files)

- [ ] **Step 1: Rename the two still files used in the photography section**

```bash
cd "/Users/kernhendricks/GitHub/KH Portfolio Website 2026/photos"
mv "Still 2024-07-03 181935_2.25.1.jpg" "still-field-1.jpg"
mv "Still 2024-07-03 182250_2.9.1.jpg" "still-field-2.jpg"
```

- [ ] **Step 2: Verify all 12 photography section images exist**

```bash
ls photos/
```

Expected output includes:
```
afghanistan-forest-council.jpg
afghanistan-forest-loggers.jpg
afghanistan-forest-trees.jpg
afghanistan-gems-mine-interior.jpg
afghanistan-gems-miners.jpg
L1001635.jpg
L1001647.jpg
still-field-1.jpg
still-field-2.jpg
ukraine-reactor-core.jpg
ukraine-reactor-crater.jpg
ukraine-reactor-lede.jpg
```

- [ ] **Step 3: Commit**

```bash
cd "/Users/kernhendricks/GitHub/KH Portfolio Website 2026"
git add photos/
git commit -m "rename video still files to clean filenames"
```

---

## Task 2: Open Graph meta tags + nav links update

**Files:**
- Modify: `index.html` — `<head>` block and `<nav>` block

- [ ] **Step 1: Add OG and Twitter meta tags**

In `index.html`, find this line in `<head>`:
```html
  <meta name="description" content="Documentary filmmaker, photojournalist and humanitarian analyst. Work published by Al Jazeera, Scientific American, DW Documentary, Foreign Policy, Undark.">
```

Add immediately after it:
```html
  <!-- Open Graph -->
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

- [ ] **Step 2: Update nav links to include Photography and Writing**

Find:
```html
      <li><a href="#work">Work</a></li>
      <li><a href="#about">About</a></li>
      <li><a href="#contact">Contact</a></li>
```

Replace with:
```html
      <li><a href="#work">Work</a></li>
      <li><a href="#photography">Photography</a></li>
      <li><a href="#writing">Writing</a></li>
      <li><a href="#about">About</a></li>
      <li><a href="#contact">Contact</a></li>
```

- [ ] **Step 3: Verify in browser**

```bash
python3 -m http.server 8080
```

Open http://localhost:8080. Verify:
- Nav shows 5 links: Work · Photography · Writing · About · Contact
- View source confirms OG meta tags present in `<head>`

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "add OG meta tags and Photography/Writing nav links"
```

---

## Task 3: Scroll progress line

**Files:**
- Modify: `index.html` — CSS block, HTML body, JS block

- [ ] **Step 1: Add CSS for progress line**

In the `<style>` block, find `/* ── CUSTOM CURSOR ── */` and add before it:

```css
    /* ── SCROLL PROGRESS ── */
    #progress-line {
      position: fixed;
      top: 0; left: 0;
      height: 1px;
      width: 0%;
      background: var(--accent);
      z-index: 10000;
    }
```

- [ ] **Step 2: Add the element to the HTML**

Find `<!-- Custom cursor -->` in the body and add before it:

```html
  <!-- Scroll progress line -->
  <div id="progress-line"></div>
```

- [ ] **Step 3: Add JS to drive it**

In the `<script>` block, find `// Custom cursor` and add before it:

```javascript
    // Scroll progress line
    const progressLine = document.getElementById('progress-line');
    function updateProgress() {
      const scrollY = window.scrollY;
      const docH = document.documentElement.scrollHeight - window.innerHeight;
      progressLine.style.width = (docH > 0 ? (scrollY / docH) * 100 : 0) + '%';
    }
    window.addEventListener('scroll', updateProgress, { passive: true });
```

- [ ] **Step 4: Verify in browser**

Open http://localhost:8080. Scroll down — a thin gold line should fill left-to-right from the top of the viewport.

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "add scroll progress line"
```

---

## Task 4: Section numbering + section-level fade transitions

**Files:**
- Modify: `index.html` — CSS block, HTML section labels, JS block

- [ ] **Step 1: Add CSS for section numbers and section fades**

In the `<style>` block, find `/* ── SECTIONS SHARED ── */` and add after the closing brace of `.section-label::after`:

```css
    .section-number {
      font-family: var(--font-mono);
      font-size: 9px;
      letter-spacing: 0.15em;
      color: var(--text-dim);
      margin-bottom: 6px;
    }

    /* ── SECTION FADE ── */
    .section-fade {
      opacity: 0;
      transform: translateY(20px);
      transition: opacity 0.9s var(--ease-out-expo), transform 0.9s var(--ease-out-expo);
    }
    .section-fade.visible {
      opacity: 1;
      transform: translateY(0);
    }
```

- [ ] **Step 2: Add section numbers to the Work section**

Find:
```html
    <p class="section-label reveal">Selected Work</p>
```

Replace with:
```html
    <p class="section-number">—01</p>
    <p class="section-label reveal">Selected Work</p>
```

- [ ] **Step 3: Add section-fade to existing sections**

Add `section-fade` class to `#work`, `#about`, and `#contact`:

Find:
```html
  <section id="work">
```
Replace with:
```html
  <section id="work" class="section-fade">
```

Find:
```html
  <section id="about">
```
Replace with:
```html
  <section id="about" class="section-fade">
```

Find:
```html
  <section id="contact">
```
Replace with:
```html
  <section id="contact" class="section-fade">
```

Note: Do NOT add `section-fade` to `#hero` — it has its own CSS keyframe entrance animations.

- [ ] **Step 4: Add the About section number (—04)**

In `#about`, find the `.about-left` div. It starts with:
```html
      <div class="section-label">About</div>
```

Replace with:
```html
      <p class="section-number">—04</p>
      <div class="section-label">About</div>
```

- [ ] **Step 5: Add the Contact section number (—05)**

In `#contact`, find:
```html
    <h2 class="contact-heading reveal">Let's <em>work</em><br>together.</h2>
```

Add before it:
```html
    <p class="section-number reveal">—05</p>
```

- [ ] **Step 6: Add JS IntersectionObserver for section fades**

In the `<script>` block, after the existing `reveals.forEach(el => observer.observe(el));` line, add:

```javascript
    // Section-level fades
    const sectionFades = document.querySelectorAll('.section-fade');
    const sectionObserver = new IntersectionObserver(entries => {
      entries.forEach(e => {
        if (e.isIntersecting) {
          e.target.classList.add('visible');
          sectionObserver.unobserve(e.target);
        }
      });
    }, { threshold: 0.05 });
    sectionFades.forEach(el => sectionObserver.observe(el));
```

- [ ] **Step 7: Verify in browser**

Open http://localhost:8080. Reload and scroll — each section (Work, About, Contact) should fade and slide up into view. Verify section number `—01` appears above "Selected Work" label. Verify `—04` appears above "About" label.

- [ ] **Step 8: Commit**

```bash
git add index.html
git commit -m "add section numbering and section-level fade transitions"
```

---

## Task 5: Portrait placeholder in About section

**Files:**
- Modify: `index.html` — CSS block, About section HTML

- [ ] **Step 1: Add CSS for portrait placeholder**

In `<style>`, find `/* ── ABOUT ── */` and add after `.about-left { position: sticky; top: 120px; }`:

```css
    .portrait-placeholder {
      width: 100%;
      aspect-ratio: 3/4;
      background: var(--bg-3);
      border: 1px dashed rgba(200,169,110,0.25);
      display: flex;
      align-items: center;
      justify-content: center;
      margin-bottom: 32px;
    }
    .portrait-placeholder span {
      font-family: var(--font-mono);
      font-size: 9px;
      letter-spacing: 0.15em;
      text-transform: uppercase;
      color: var(--accent-dim);
    }
```

- [ ] **Step 2: Add the placeholder element to the About left column**

At this point (after Task 4), `.about-left` starts with:
```html
    <div class="about-left reveal">
      <p class="section-number">—04</p>
```

Find that exact text and replace with:
```html
    <div class="about-left reveal">
      <div class="portrait-placeholder">
        <span>Photo — coming soon</span>
      </div>
      <p class="section-number">—04</p>
```

- [ ] **Step 3: Verify in browser**

Scroll to About section. A dashed-border portrait-ratio box with "PHOTO — COMING SOON" in dim gold should appear at the top of the left column.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "add portrait placeholder to About section"
```

---

## Task 6: Mobile nav (hamburger + full-screen overlay)

**Files:**
- Modify: `index.html` — CSS block, nav HTML, body HTML, JS block

- [ ] **Step 1: Add CSS for hamburger button and mobile overlay**

In `<style>`, find `/* ── NAV ── */`. Add after all existing nav CSS (after `.nav-links a:hover { color: var(--accent); }`):

```css
    /* Mobile hamburger */
    .nav-hamburger {
      display: none;
      flex-direction: column;
      gap: 5px;
      background: none;
      border: none;
      cursor: none;
      padding: 4px;
      position: relative;
      z-index: 1;
    }
    .nav-hamburger span {
      display: block;
      width: 22px;
      height: 1px;
      background: var(--text-dim);
      transition: background 0.3s;
    }
    .nav-hamburger:hover span { background: var(--accent); }

    /* Mobile nav overlay */
    .mobile-nav {
      position: fixed;
      inset: 0;
      background: rgba(10,10,8,0.97);
      z-index: 200;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      opacity: 0;
      pointer-events: none;
      transition: opacity 0.4s var(--ease-out-expo);
    }
    .mobile-nav.open {
      opacity: 1;
      pointer-events: all;
    }
    .mobile-nav-close {
      position: absolute;
      top: 24px; right: 28px;
      background: none;
      border: none;
      color: var(--text-dim);
      font-size: 22px;
      cursor: none;
      font-family: var(--font-mono);
      transition: color 0.3s;
    }
    .mobile-nav-close:hover { color: var(--accent); }
    .mobile-nav-links {
      list-style: none;
      display: flex;
      flex-direction: column;
      align-items: center;
    }
    .mobile-nav-links a {
      font-family: var(--font-mono);
      font-size: 13px;
      letter-spacing: 0.2em;
      text-transform: uppercase;
      color: var(--text-dim);
      padding: 18px 0;
      display: block;
      border-bottom: 1px solid var(--border);
      min-width: 220px;
      text-align: center;
      transition: color 0.3s;
    }
    .mobile-nav-links li:last-child a { border-bottom: none; }
    .mobile-nav-links a:hover { color: var(--accent); }
```

- [ ] **Step 2: Show hamburger on mobile — update the responsive CSS block**

Find in the responsive section:
```css
    @media (max-width: 600px) {
      .nav-links { display: none; }
```

Replace with:
```css
    @media (max-width: 600px) {
      .nav-links { display: none; }
      .nav-hamburger { display: flex; }
```

- [ ] **Step 3: Add hamburger button to the nav HTML**

Find:
```html
    <ul class="nav-links">
```

Add before it:
```html
    <button class="nav-hamburger" id="navHamburger" aria-label="Open menu">
      <span></span><span></span><span></span>
    </button>
```

- [ ] **Step 4: Add the full-screen overlay to the body**

Find `<!-- Navigation -->` and add after the closing `</nav>` tag:

```html
  <!-- Mobile nav overlay -->
  <div class="mobile-nav" id="mobileNav" role="dialog" aria-modal="true" aria-label="Navigation menu">
    <button class="mobile-nav-close" id="mobileNavClose" aria-label="Close menu">✕</button>
    <ul class="mobile-nav-links">
      <li><a href="#work">Work</a></li>
      <li><a href="#photography">Photography</a></li>
      <li><a href="#writing">Writing</a></li>
      <li><a href="#about">About</a></li>
      <li><a href="#contact">Contact</a></li>
    </ul>
  </div>
```

- [ ] **Step 5: Add JS for hamburger toggle**

In `<script>`, add after the cursor expand/collapse event listeners block:

```javascript
    // Mobile nav
    const navHamburger  = document.getElementById('navHamburger');
    const mobileNav     = document.getElementById('mobileNav');
    const mobileNavClose = document.getElementById('mobileNavClose');
    function openMobileNav()  { mobileNav.classList.add('open'); }
    function closeMobileNav() { mobileNav.classList.remove('open'); }
    if (navHamburger) {
      navHamburger.addEventListener('click', openMobileNav);
      mobileNavClose.addEventListener('click', closeMobileNav);
      mobileNav.querySelectorAll('a').forEach(a => a.addEventListener('click', closeMobileNav));
    }
```

- [ ] **Step 6: Verify in browser**

Open http://localhost:8080. Resize browser to ≤600px wide (or use DevTools device emulation). Verify:
- Hamburger button (3 lines) appears top-right
- Tapping it opens a full-screen dark overlay with 5 nav links
- Tapping ✕ or any link closes it
- At desktop width, hamburger is hidden and normal nav links appear

- [ ] **Step 7: Commit**

```bash
git add index.html
git commit -m "add mobile nav hamburger and full-screen overlay"
```

---

## Task 7: Writing section

**Files:**
- Modify: `index.html` — CSS block, HTML body (new section between `#work` and `#about`)

- [ ] **Step 1: Add CSS for writing section**

In `<style>`, find `/* ── ABOUT ── */` and add before it:

```css
    /* ── WRITING ── */
    #writing {
      border-top: 1px solid var(--border);
    }

    .article-list {
      display: flex;
      flex-direction: column;
    }

    .article-row {
      display: grid;
      grid-template-columns: 140px 1fr 56px 24px;
      align-items: center;
      gap: 24px;
      padding: 22px 0;
      border-bottom: 1px solid var(--border);
      text-decoration: none;
      color: inherit;
      transition: border-color 0.3s;
      position: relative;
    }
    .article-row:first-child { border-top: 1px solid var(--border); }
    .article-row:hover { border-bottom-color: var(--border-hi); }

    .article-row::before {
      content: '';
      position: absolute;
      inset: 0;
      background: rgba(200,169,110,0.03);
      opacity: 0;
      transition: opacity 0.3s;
      pointer-events: none;
    }
    .article-row:hover::before { opacity: 1; }

    .article-outlet {
      font-family: var(--font-mono);
      font-size: 9px;
      letter-spacing: 0.18em;
      text-transform: uppercase;
      color: var(--accent);
    }

    .article-title {
      font-family: var(--font-display);
      font-size: clamp(15px, 1.4vw, 19px);
      font-style: italic;
      font-weight: 400;
      color: var(--text-mid);
      line-height: 1.25;
      transition: color 0.3s;
    }
    .article-row:hover .article-title { color: var(--text); }

    .article-year {
      font-family: var(--font-mono);
      font-size: 9px;
      letter-spacing: 0.1em;
      color: var(--text-dim);
      text-align: right;
      transition: color 0.3s;
    }
    .article-row:hover .article-year { color: var(--text-mid); }

    .article-arrow {
      color: var(--accent);
      opacity: 0;
      transform: translateX(-8px);
      transition: opacity 0.3s var(--ease-out-expo), transform 0.3s var(--ease-out-expo);
      display: flex;
      align-items: center;
    }
    .article-row:hover .article-arrow { opacity: 1; transform: translateX(0); }

    @media (max-width: 700px) {
      .article-row { grid-template-columns: 90px 1fr 44px 0; gap: 12px; }
      .article-arrow { display: none; }
    }
```

- [ ] **Step 2: Add the Writing section HTML**

Find `<!-- ── ABOUT ── -->` in the HTML and add the entire Writing section before it:

```html
  <!-- ── WRITING ── -->
  <section id="writing" class="section-fade">
    <p class="section-number reveal">—03</p>
    <p class="section-label reveal">Writing</p>

    <div class="article-list">

      <a href="https://www.aljazeera.com/features/2023/8/18/the-former-afghan-soldiers-turning-to-gem-mining-to-survive" target="_blank" rel="noopener" class="article-row reveal">
        <span class="article-outlet">Al Jazeera</span>
        <span class="article-title">The Former Afghan Soldiers Turning to Gem Mining to Survive</span>
        <span class="article-year">2023</span>
        <span class="article-arrow"><svg width="14" height="10" viewBox="0 0 14 10" fill="none"><path d="M1 5h12M9 1l4 4-4 4" stroke="currentColor" stroke-width="1.2" stroke-linecap="round"/></svg></span>
      </a>

      <a href="https://www.aljazeera.com/features/2022/8/19/women-working-inside-an-afghan-chemical-lab-face-uncertain-future" target="_blank" rel="noopener" class="article-row reveal reveal-delay-1">
        <span class="article-outlet">Al Jazeera</span>
        <span class="article-title">Women Working Inside an Afghan Chemical Lab Face Uncertain Future</span>
        <span class="article-year">2022</span>
        <span class="article-arrow"><svg width="14" height="10" viewBox="0 0 14 10" fill="none"><path d="M1 5h12M9 1l4 4-4 4" stroke="currentColor" stroke-width="1.2" stroke-linecap="round"/></svg></span>
      </a>

      <a href="https://undark.org/2023/05/01/keeping-the-lights-on-at-ukraines-research-nuclear-reactor/" target="_blank" rel="noopener" class="article-row reveal reveal-delay-2">
        <span class="article-outlet">Undark</span>
        <span class="article-title">Keeping the Lights On at Ukraine's Research Nuclear Reactor</span>
        <span class="article-year">2023</span>
        <span class="article-arrow"><svg width="14" height="10" viewBox="0 0 14 10" fill="none"><path d="M1 5h12M9 1l4 4-4 4" stroke="currentColor" stroke-width="1.2" stroke-linecap="round"/></svg></span>
      </a>

      <a href="https://undark.org/2023/04/03/in-ukraine-grain-shortages-reverberate-beyond-borders/" target="_blank" rel="noopener" class="article-row reveal reveal-delay-3">
        <span class="article-outlet">Undark</span>
        <span class="article-title">In Ukraine, Grain Shortages Reverberate Beyond Borders</span>
        <span class="article-year">2023</span>
        <span class="article-arrow"><svg width="14" height="10" viewBox="0 0 14 10" fill="none"><path d="M1 5h12M9 1l4 4-4 4" stroke="currentColor" stroke-width="1.2" stroke-linecap="round"/></svg></span>
      </a>

      <a href="https://www.scientificamerican.com/article/a-rare-glimpse-into-afghanistans-spectacular-vanishing-forests/" target="_blank" rel="noopener" class="article-row reveal">
        <span class="article-outlet">Scientific American</span>
        <span class="article-title">A Rare Glimpse into Afghanistan's Spectacular, Vanishing Forests</span>
        <span class="article-year">2023</span>
        <span class="article-arrow"><svg width="14" height="10" viewBox="0 0 14 10" fill="none"><path d="M1 5h12M9 1l4 4-4 4" stroke="currentColor" stroke-width="1.2" stroke-linecap="round"/></svg></span>
      </a>

      <a href="https://www.vice.com/en/article/afghanistan-boys-coal-mining-taliban-child-labor/" target="_blank" rel="noopener" class="article-row reveal reveal-delay-1">
        <span class="article-outlet">Vice</span>
        <span class="article-title">Young Boys Work in Dangerous Mines As Afghanistan Prioritizes Coal</span>
        <span class="article-year">2022</span>
        <span class="article-arrow"><svg width="14" height="10" viewBox="0 0 14 10" fill="none"><path d="M1 5h12M9 1l4 4-4 4" stroke="currentColor" stroke-width="1.2" stroke-linecap="round"/></svg></span>
      </a>

      <a href="https://www.vice.com/en/article/afghanistan-photos-taliban-poppy-harvest-ban/" target="_blank" rel="noopener" class="article-row reveal reveal-delay-2">
        <span class="article-outlet">Vice</span>
        <span class="article-title">Look at These Photos of the Taliban's Endless Poppy Fields</span>
        <span class="article-year">2022</span>
        <span class="article-arrow"><svg width="14" height="10" viewBox="0 0 14 10" fill="none"><path d="M1 5h12M9 1l4 4-4 4" stroke="currentColor" stroke-width="1.2" stroke-linecap="round"/></svg></span>
      </a>

      <a href="https://undark.org/2022/04/20/in-a-cold-winter-afghans-struggle-to-keep-warm/" target="_blank" rel="noopener" class="article-row reveal reveal-delay-3">
        <span class="article-outlet">Undark</span>
        <span class="article-title">In a Cold Winter, Afghans Struggle to Keep Warm</span>
        <span class="article-year">2022</span>
        <span class="article-arrow"><svg width="14" height="10" viewBox="0 0 14 10" fill="none"><path d="M1 5h12M9 1l4 4-4 4" stroke="currentColor" stroke-width="1.2" stroke-linecap="round"/></svg></span>
      </a>

      <a href="https://undark.org/2020/05/20/afghanistan-meth-ephedra/" target="_blank" rel="noopener" class="article-row reveal">
        <span class="article-outlet">Undark</span>
        <span class="article-title">The Wild Shrub at the Root of the Afghan Meth Epidemic</span>
        <span class="article-year">2020</span>
        <span class="article-arrow"><svg width="14" height="10" viewBox="0 0 14 10" fill="none"><path d="M1 5h12M9 1l4 4-4 4" stroke="currentColor" stroke-width="1.2" stroke-linecap="round"/></svg></span>
      </a>

      <a href="https://undark.org/2019/10/28/afghanistan-health-care-disparities/" target="_blank" rel="noopener" class="article-row reveal reveal-delay-1">
        <span class="article-outlet">Undark</span>
        <span class="article-title">For Afghan Health Workers, a Gauntlet of Making Do</span>
        <span class="article-year">2019</span>
        <span class="article-arrow"><svg width="14" height="10" viewBox="0 0 14 10" fill="none"><path d="M1 5h12M9 1l4 4-4 4" stroke="currentColor" stroke-width="1.2" stroke-linecap="round"/></svg></span>
      </a>

    </div>
  </section>
```

- [ ] **Step 3: Verify in browser**

Scroll to the Writing section. Verify:
- Section number `—03` and "Writing" label appear
- 10 rows, each with outlet in gold, italic title, year, arrow on hover
- Rows stagger-fade in on scroll
- Clicking a row opens the article in a new tab

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "add Writing section with 10 articles"
```

---

## Task 8: Photography section (horizontal scroll)

**Files:**
- Modify: `index.html` — CSS block, HTML body (new section between `#work` and `#writing`), JS block

- [ ] **Step 1: Add CSS for photography section**

In `<style>`, find `/* ── WORK GRID ── */` and add before it:

```css
    /* ── PHOTOGRAPHY (HORIZONTAL SCROLL) ── */
    #photography {
      padding: 0;
      max-width: none;
      margin: 0;
      border-top: 1px solid var(--border);
    }

    .h-scroll-outer {
      height: 400vh;
      position: relative;
    }

    .h-scroll-sticky {
      position: sticky;
      top: 0;
      height: 100vh;
      overflow: hidden;
      display: flex;
      flex-direction: column;
      justify-content: center;
    }

    .h-scroll-header {
      padding: 0 48px;
      margin-bottom: 32px;
      flex-shrink: 0;
    }

    .h-scroll-track {
      display: flex;
      gap: 2px;
      padding: 0 48px;
      will-change: transform;
      flex-shrink: 0;
    }

    .h-photo {
      flex-shrink: 0;
      position: relative;
      overflow: hidden;
      background: var(--bg-3);
      cursor: crosshair;
    }
    .h-photo.landscape {
      height: calc(56vh - 32px);
      width: calc((56vh - 32px) * 3 / 2);
    }
    .h-photo.portrait {
      height: calc(56vh - 32px);
      width: calc((56vh - 32px) * 2 / 3);
    }

    .h-photo img {
      position: absolute;
      inset: 0;
      width: 100%;
      height: 100%;
      object-fit: cover;
      object-position: center;
      filter: saturate(0.75) brightness(0.8);
      transform: scale(1.02);
      transition: filter 0.5s var(--ease-out-expo), transform 0.5s var(--ease-out-expo);
      display: block;
    }
    .h-photo:hover img {
      filter: saturate(0.92) brightness(0.96);
      transform: scale(1.05);
    }

    .h-photo-overlay {
      position: absolute;
      inset: 0;
      background: linear-gradient(to top, rgba(10,10,8,0.88) 0%, transparent 55%);
      display: flex;
      flex-direction: column;
      justify-content: flex-end;
      padding: 20px;
      z-index: 2;
      transition: background 0.4s;
    }
    .h-photo:hover .h-photo-overlay {
      background: linear-gradient(to top, rgba(10,10,8,0.97) 0%, rgba(10,10,8,0.3) 60%, transparent 100%);
    }

    .h-photo-region {
      font-family: var(--font-mono);
      font-size: 8px;
      letter-spacing: 0.2em;
      text-transform: uppercase;
      color: var(--accent);
      margin-bottom: 4px;
      opacity: 0;
      transform: translateY(6px);
      transition: all 0.35s var(--ease-out-expo);
    }
    .h-photo-caption {
      font-family: var(--font-display);
      font-size: clamp(13px, 1.2vw, 16px);
      font-style: italic;
      color: var(--text);
      line-height: 1.2;
      opacity: 0;
      transform: translateY(8px);
      transition: all 0.4s var(--ease-out-expo) 0.04s;
    }
    .h-photo:hover .h-photo-region,
    .h-photo:hover .h-photo-caption {
      opacity: 1;
      transform: translateY(0);
    }

    .h-progress-wrap {
      position: absolute;
      bottom: 32px;
      left: 48px;
      right: 48px;
      display: flex;
      align-items: center;
      gap: 16px;
    }
    .h-progress-track {
      height: 1px;
      background: var(--border);
      position: relative;
      width: 200px;
    }
    .h-progress-fill {
      position: absolute;
      left: 0; top: 0;
      height: 100%;
      background: var(--accent-dim);
      width: 0%;
    }
    .h-progress-label {
      font-family: var(--font-mono);
      font-size: 9px;
      letter-spacing: 0.15em;
      text-transform: uppercase;
      color: var(--text-dim);
    }

    /* Mobile: revert to vertical wrap */
    @media (max-width: 900px) {
      #photography { border-top: 1px solid var(--border); }
      .h-scroll-outer { height: auto; }
      .h-scroll-sticky { position: static; height: auto; overflow: visible; }
      .h-scroll-header { padding: 80px 24px 32px; }
      .h-scroll-track { flex-wrap: wrap; padding: 0 24px; gap: 2px; }
      .h-photo.landscape,
      .h-photo.portrait { height: auto; width: calc(50% - 1px); aspect-ratio: 3/2; }
      .h-photo img { position: static; width: 100%; height: 100%; }
      .h-progress-wrap { display: none; }
    }
```

- [ ] **Step 2: Add the Photography section HTML**

Find `<!-- ── WRITING ── -->` and add the Photography section before it:

```html
  <!-- ── PHOTOGRAPHY ── -->
  <section id="photography">
    <div class="h-scroll-outer" id="hScrollOuter">
      <div class="h-scroll-sticky">

        <div class="h-scroll-header">
          <p class="section-number">—02</p>
          <p class="section-label">Photography</p>
        </div>

        <div class="h-scroll-track" id="hScrollTrack">

          <div class="h-photo landscape">
            <img src="photos/afghanistan-gems-miners.jpg" alt="Gem miners at break, Nuristan, Afghanistan" loading="lazy">
            <div class="h-photo-overlay">
              <span class="h-photo-region">Afghanistan · Nuristan</span>
              <span class="h-photo-caption">Gem miners at break outside their mine</span>
            </div>
          </div>

          <div class="h-photo landscape">
            <img src="photos/L1001647.jpg" alt="Afghanistan" loading="lazy">
            <div class="h-photo-overlay">
              <span class="h-photo-region">Afghanistan</span>
              <span class="h-photo-caption">Caption to be added</span>
            </div>
          </div>

          <div class="h-photo landscape">
            <img src="photos/ukraine-reactor-lede.jpg" alt="Technicians in reactor hall, Kharkiv, Ukraine" loading="lazy">
            <div class="h-photo-overlay">
              <span class="h-photo-region">Ukraine · Kharkiv</span>
              <span class="h-photo-caption">Technicians ascending into the reactor hall</span>
            </div>
          </div>

          <div class="h-photo landscape">
            <img src="photos/afghanistan-forest-trees.jpg" alt="Forest, Korengal Valley, Afghanistan" loading="lazy">
            <div class="h-photo-overlay">
              <span class="h-photo-region">Afghanistan · Korengal Valley</span>
              <span class="h-photo-caption">Trees as livelihood in Wama district</span>
            </div>
          </div>

          <div class="h-photo landscape">
            <img src="photos/afghanistan-gems-mine-interior.jpg" alt="Inside tourmaline mine, Kunar, Afghanistan" loading="lazy">
            <div class="h-photo-overlay">
              <span class="h-photo-region">Afghanistan · Kunar</span>
              <span class="h-photo-caption">Inside Abid's tourmaline mine near Parun</span>
            </div>
          </div>

          <div class="h-photo landscape">
            <img src="photos/still-field-1.jpg" alt="" loading="lazy">
            <div class="h-photo-overlay">
              <span class="h-photo-region">Field</span>
              <span class="h-photo-caption">Caption to be added</span>
            </div>
          </div>

          <div class="h-photo landscape">
            <img src="photos/ukraine-reactor-core.jpg" alt="Reactor core in darkness, Kharkiv, Ukraine" loading="lazy">
            <div class="h-photo-overlay">
              <span class="h-photo-region">Ukraine · Kharkiv</span>
              <span class="h-photo-caption">Reactor core in darkness</span>
            </div>
          </div>

          <div class="h-photo landscape">
            <img src="photos/afghanistan-forest-loggers.jpg" alt="Loggers in Nuristan, Afghanistan" loading="lazy">
            <div class="h-photo-overlay">
              <span class="h-photo-region">Afghanistan · Nuristan</span>
              <span class="h-photo-caption">Dilaram and Sanaullah, loggers in Wama</span>
            </div>
          </div>

          <div class="h-photo landscape">
            <img src="photos/L1001635.jpg" alt="Afghanistan" loading="lazy">
            <div class="h-photo-overlay">
              <span class="h-photo-region">Afghanistan</span>
              <span class="h-photo-caption">Caption to be added</span>
            </div>
          </div>

          <div class="h-photo landscape">
            <img src="photos/afghanistan-forest-council.jpg" alt="Tribal council meeting, Korengal Valley, Afghanistan" loading="lazy">
            <div class="h-photo-overlay">
              <span class="h-photo-region">Afghanistan · Korengal Valley</span>
              <span class="h-photo-caption">Tribal council meeting</span>
            </div>
          </div>

          <div class="h-photo landscape">
            <img src="photos/ukraine-reactor-crater.jpg" alt="Rocket strike crater, Kharkiv, Ukraine" loading="lazy">
            <div class="h-photo-overlay">
              <span class="h-photo-region">Ukraine · Kharkiv</span>
              <span class="h-photo-caption">Crater from March 2022 rocket strike</span>
            </div>
          </div>

          <div class="h-photo landscape">
            <img src="photos/still-field-2.jpg" alt="" loading="lazy">
            <div class="h-photo-overlay">
              <span class="h-photo-region">Field</span>
              <span class="h-photo-caption">Caption to be added</span>
            </div>
          </div>

        </div><!-- /h-scroll-track -->

        <div class="h-progress-wrap">
          <div class="h-progress-track">
            <div class="h-progress-fill" id="hProgressFill"></div>
          </div>
          <span class="h-progress-label" id="hProgressLabel">Scroll to explore</span>
        </div>

      </div><!-- /h-scroll-sticky -->
    </div><!-- /h-scroll-outer -->
  </section>
```

- [ ] **Step 3: Add JS for horizontal scroll mechanic**

In the `<script>` block, add after the `sectionFades.forEach(el => sectionObserver.observe(el));` line:

```javascript
    // Photography horizontal scroll
    const hOuter = document.getElementById('hScrollOuter');
    const hTrack = document.getElementById('hScrollTrack');
    const hFill  = document.getElementById('hProgressFill');
    const hLabel = document.getElementById('hProgressLabel');

    function updateHScroll() {
      if (!hOuter || !hTrack) return;
      // Skip on mobile where section is static
      if (window.innerWidth <= 900) {
        hTrack.style.transform = '';
        return;
      }
      const scrollY  = window.scrollY;
      const winH     = window.innerHeight;
      const start    = hOuter.offsetTop;
      const end      = hOuter.offsetTop + hOuter.offsetHeight - winH;
      const progress = Math.max(0, Math.min(1, (scrollY - start) / (end - start)));
      const maxTx    = hTrack.scrollWidth - window.innerWidth + 96;
      hTrack.style.transform = `translateX(-${progress * maxTx}px)`;
      if (hFill)  hFill.style.width = (progress * 100) + '%';
      if (hLabel) {
        const total = hTrack.children.length;
        const cur   = Math.round(progress * total);
        hLabel.textContent = cur > 0 ? `${cur} / ${total}` : 'Scroll to explore';
      }
    }

    window.addEventListener('scroll', updateHScroll, { passive: true });
    window.addEventListener('resize', updateHScroll, { passive: true });
    updateHScroll();
```

- [ ] **Step 4: Verify in browser**

Open http://localhost:8080. Scroll through the Photography section. Verify:
- Section pins to viewport while photos scroll horizontally left
- All 12 photos appear, images load correctly
- Hover on a photo shows region + caption
- Progress counter at bottom increments (`1 / 12` → `12 / 12`)
- After scrolling through all photos, page continues to Writing section
- At ≤900px wide (DevTools), section reverts to 2-column vertical grid

- [ ] **Step 5: Commit**

```bash
git add index.html photos/
git commit -m "add Photography horizontal scroll section"
```

---

## Task 9: Final verification and deploy

- [ ] **Step 1: Full visual pass in browser**

Open http://localhost:8080. Check end-to-end:

| Check | Expected |
|-------|----------|
| Gold progress line | Fills as you scroll, starts empty on load |
| Hero | Unchanged — showreel background, animations fire on load |
| —01 Work | Section number visible above label, section fades in |
| —02 Photography | Horizontal scroll works, all 12 images load, hover states work |
| —03 Writing | 10 article rows, hover arrow, links open in new tab |
| —04 About | Portrait placeholder visible, section number above label |
| —05 Contact | Section number visible |
| Mobile (≤600px) | Hamburger visible, overlay opens/closes, photography section wraps to grid |
| Nav links | 5 links — Work · Photography · Writing · About · Contact |
| OG tags | Present in page source (`⌘U`) |

- [ ] **Step 2: Check browser console for errors**

Open DevTools → Console. Reload page. Expected: no errors or warnings.

- [ ] **Step 3: Deploy**

```bash
cd "/Users/kernhendricks/GitHub/KH Portfolio Website 2026"
git add index.html photos/
git status
```

Confirm only expected files are staged, then:

```bash
git commit -m "portfolio site: photography, writing, mobile nav, section fades, OG tags"
git push origin main
```

- [ ] **Step 4: Verify on live site**

Wait ~60 seconds, then open https://kernhendricks.com. Confirm the live site matches the local preview.
