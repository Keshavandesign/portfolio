# Homepage Strategic Facelift Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Apply a subtle strategic facelift to `index.html` — adding credibility signals (availability dot, client marquee, tools orbital, metric whispers, senior-level eyebrow) without disrupting the existing cream/purple/serif aesthetic.

**Architecture:** All changes live inside `index.html`. Inline CSS additions go into the existing `<style>` block. HTML changes edit existing elements or add new sections between hero → about → tools (new) → projects → testimonials → footer. One new JS block for the orbital tools animation.

**Tech Stack:** Plain HTML, CSS custom properties (existing tokens: `--fg`, `--bg`, `--muted`, `--accent`, `--border`, `--sans`, `--serif`, `--ease`), vanilla JS.

**Spec:** `docs/superpowers/specs/2026-04-18-homepage-strategic-facelift-design.md`

---

## File Map

| File | Action | Purpose |
|------|--------|---------|
| `index.html` | Modify (multiple sections) | All facelift changes |

---

## Task 1: Nav Availability Dot

**Files:**
- Modify: `index.html` — add CSS around line 150 (after `.nav-btn` rules) and wrap the nav button in a container

- [ ] **Step 1: Add CSS for availability dot**

Locate the `.nav-btn:hover` block (around line 143-149). Add the following CSS immediately after the existing `.nav-btn` rules (before the `.nav-hamburger` section):

```css
    .nav-right {
      display: flex;
      align-items: center;
      gap: 12px;
      justify-content: flex-end;
    }
    .nav-available-dot {
      width: 8px; height: 8px;
      border-radius: 50%;
      background: #22C55E;
      box-shadow: 0 0 0 0 rgba(34, 197, 94, 0.55);
      animation: availablePulse 2.2s ease-in-out infinite;
      flex-shrink: 0;
    }
    @keyframes availablePulse {
      0%, 100% { box-shadow: 0 0 0 0 rgba(34, 197, 94, 0.55); transform: scale(1); }
      50%      { box-shadow: 0 0 0 6px rgba(34, 197, 94, 0); transform: scale(1.06); }
    }
```

Note: The existing `.nav-right` rule (found near line 125 area) already has `display: flex; justify-content: flex-end;`. If that rule exists, modify it in place to add `align-items: center; gap: 12px;` instead of duplicating.

- [ ] **Step 2: Add dot to nav HTML**

Find the nav-right block (around line 915-918):

```html
    <!-- Right: resume pill -->
    <div class="nav-right">
      <a href="#" class="nav-btn">Resume</a>
    </div>
```

Replace with:

```html
    <!-- Right: availability dot + resume pill -->
    <div class="nav-right">
      <span class="nav-available-dot" aria-label="Available for work" title="Available for work"></span>
      <a href="#" class="nav-btn">Resume</a>
    </div>
```

- [ ] **Step 3: Commit**

```bash
git -C /Users/keshavanand/Desktop/Portfolio add index.html
git -C /Users/keshavanand/Desktop/Portfolio commit -m "feat(nav): add pulsing availability dot next to resume button"
```

---

## Task 2: Hero Strategic Eyebrow

**Files:**
- Modify: `index.html` — add `.hero-eyebrow` CSS and insert eyebrow above `.hero-headline`

- [ ] **Step 1: Add hero eyebrow CSS**

Locate the `.hero-headline` CSS rule (around line 260). Add a new rule immediately above it:

```css
    .hero-eyebrow {
      font-family: var(--sans);
      font-size: 10px;
      font-weight: 600;
      letter-spacing: 0.14em;
      text-transform: uppercase;
      color: var(--muted);
      margin-bottom: 24px;
      opacity: 0;
      transform: translateY(12px);
      animation: fadeUp 0.85s var(--ease) 0.05s forwards;
    }
    .hero-eyebrow .sep {
      display: inline-block;
      margin: 0 10px;
      color: var(--accent);
      opacity: 0.6;
    }
```

- [ ] **Step 2: Add eyebrow HTML inside hero-content**

Find the `.hero-content` block (around line 930-934):

```html
    <div class="hero-content">
      <h1 class="hero-headline">
        Hi, I'm Keshav and I design <span class="accent" id="typed-word"></span><span class="typing-cursor" id="typing-cursor"></span>
      </h1>
    </div>
```

Replace with:

```html
    <div class="hero-content">
      <div class="hero-eyebrow">
        Senior Product Designer<span class="sep">·</span>Design Strategy<span class="sep">·</span>AI Product
      </div>
      <h1 class="hero-headline">
        Hi, I'm Keshav and I design <span class="accent" id="typed-word"></span><span class="typing-cursor" id="typing-cursor"></span>
      </h1>
    </div>
```

- [ ] **Step 3: Commit**

```bash
git -C /Users/keshavanand/Desktop/Portfolio add index.html
git -C /Users/keshavanand/Desktop/Portfolio commit -m "feat(hero): add senior-level eyebrow above headline"
```

---

## Task 3: Client Marquee Strip

**Files:**
- Modify: `index.html` — add marquee CSS and section between hero and about

- [ ] **Step 1: Add marquee CSS**

Locate the end of the hero-related CSS (look for a clean insertion point before the `.about-intro` or `.about-eyebrow` section — around line 330). Add this CSS block:

```css
    /* ─── CLIENT MARQUEE ─── */
    .client-strip {
      padding: 22px 0;
      border-top: 1px solid var(--border);
      border-bottom: 1px solid var(--border);
      overflow: hidden;
      position: relative;
      background: var(--bg);
    }
    .client-strip::before,
    .client-strip::after {
      content: '';
      position: absolute; top: 0; bottom: 0;
      width: 80px;
      z-index: 2;
      pointer-events: none;
    }
    .client-strip::before {
      left: 0;
      background: linear-gradient(to right, var(--bg), transparent);
    }
    .client-strip::after {
      right: 0;
      background: linear-gradient(to left, var(--bg), transparent);
    }
    .client-marquee {
      display: flex;
      gap: 60px;
      width: max-content;
      animation: clientScroll 60s linear infinite;
    }
    .client-marquee span {
      font-size: 12px;
      font-weight: 500;
      letter-spacing: 0.3em;
      text-transform: uppercase;
      color: var(--muted);
      opacity: 0.4;
      white-space: nowrap;
      flex-shrink: 0;
    }
    .client-marquee span.dot {
      opacity: 0.25;
      color: var(--accent);
    }
    @keyframes clientScroll {
      from { transform: translateX(0); }
      to   { transform: translateX(-50%); }
    }
    @media (prefers-reduced-motion: reduce) {
      .client-marquee { animation: none; }
    }
```

- [ ] **Step 2: Add marquee HTML between hero and about sections**

Find the end of the `<section class="hero">` block (around line 953, closing `</section>`). Immediately after it, insert:

```html
  <!-- ── Client Strip ── -->
  <section class="client-strip" aria-label="Selected clients and teams">
    <div class="client-marquee">
      <span>MSN</span><span class="dot">·</span>
      <span>ESPN</span><span class="dot">·</span>
      <span>Getty</span><span class="dot">·</span>
      <span>FinalLayer</span><span class="dot">·</span>
      <span>Rizzle</span><span class="dot">·</span>
      <span>CXFacts</span><span class="dot">·</span>
      <span>Lagorii</span><span class="dot">·</span>
      <span>Excytech</span><span class="dot">·</span>
      <!-- Duplicate for seamless loop -->
      <span>MSN</span><span class="dot">·</span>
      <span>ESPN</span><span class="dot">·</span>
      <span>Getty</span><span class="dot">·</span>
      <span>FinalLayer</span><span class="dot">·</span>
      <span>Rizzle</span><span class="dot">·</span>
      <span>CXFacts</span><span class="dot">·</span>
      <span>Lagorii</span><span class="dot">·</span>
      <span>Excytech</span><span class="dot">·</span>
    </div>
  </section>
```

- [ ] **Step 3: Commit**

```bash
git -C /Users/keshavanand/Desktop/Portfolio add index.html
git -C /Users/keshavanand/Desktop/Portfolio commit -m "feat: add quiet client marquee strip below hero"
```

---

## Task 4: About Section — "Currently at" + Tagline Sharpen

**Files:**
- Modify: `index.html` — adjust `.about-eyebrow` CSS, update eyebrow HTML, update tagline copy

- [ ] **Step 1: Update `.about-eyebrow` CSS to support two-line treatment**

Find the `.about-eyebrow` rule (around line 335):

```css
    .about-eyebrow {
      font-size: 10px;
      font-weight: 400;
      letter-spacing: 0.18em;
      text-transform: uppercase;
      color: var(--muted);
      margin-bottom: 44px;
    }
```

Replace with:

```css
    .about-eyebrow {
      font-size: 10px;
      font-weight: 400;
      letter-spacing: 0.18em;
      text-transform: uppercase;
      color: var(--muted);
      margin-bottom: 44px;
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 10px;
    }
    .about-eyebrow-now {
      font-size: 9px;
      font-weight: 500;
      letter-spacing: 0.22em;
      color: var(--muted);
      opacity: 0.7;
      display: inline-flex;
      align-items: center;
      gap: 8px;
    }
    .about-eyebrow-now::before {
      content: '';
      width: 6px; height: 6px;
      border-radius: 50%;
      background: var(--accent);
      opacity: 0.65;
      display: inline-block;
    }
```

- [ ] **Step 2: Update the about eyebrow HTML**

Find (around line 957):

```html
    <div class="about-eyebrow reveal">'FOR INQUISITIVE MINDS'</div>
```

Replace with:

```html
    <div class="about-eyebrow reveal">
      <span>'FOR INQUISITIVE MINDS'</span>
      <span class="about-eyebrow-now">Currently · Founding Designer at FinalLayer</span>
    </div>
```

- [ ] **Step 3: Sharpen the tagline copy**

Find the tagline paragraph (around line 971-973):

```html
    <p class="about-tagline reveal reveal-d2">
      A digital product designer mad for <strong>Art &amp; Psychology.</strong>
    </p>
```

Replace with:

```html
    <p class="about-tagline reveal reveal-d2">
      A product designer mad for <strong>Art &amp; Psychology</strong> — building AI-native products with <strong>measurable impact.</strong>
    </p>
```

- [ ] **Step 4: Commit**

```bash
git -C /Users/keshavanand/Desktop/Portfolio add index.html
git -C /Users/keshavanand/Desktop/Portfolio commit -m "feat(about): add currently-at line and sharpen tagline toward outcomes"
```

---

## Task 5: Tools Section — Orbital + Floating Logos (NEW)

**Files:**
- Modify: `index.html` — add CSS for tools section, add HTML between about and projects, add JS for orbital motion

- [ ] **Step 1: Add tools section CSS**

Add the following CSS block before the `/* Scroll to top */` comment (around line 661, or anywhere clean in the `<style>` block after the about section CSS):

```css
    /* ─── TOOLS / ORBITAL ─── */
    .tools-section {
      padding: 120px 52px;
      position: relative;
      overflow: hidden;
    }
    .tools-label {
      font-size: 11px;
      font-weight: 500;
      letter-spacing: 0.22em;
      text-transform: lowercase;
      color: var(--muted);
      opacity: 0.7;
      margin-bottom: 16px;
      text-align: left;
      font-style: italic;
      font-family: var(--serif);
    }
    .tools-label::before {
      content: '— ';
      color: var(--accent);
      opacity: 0.6;
      font-style: normal;
    }
    .tools-stage {
      position: relative;
      width: 100%;
      height: 520px;
      display: flex;
      align-items: center;
      justify-content: center;
    }
    .tools-stage::before {
      content: '';
      position: absolute;
      inset: 0;
      margin: auto;
      width: 260px; height: 260px;
      border-radius: 50%;
      border: 1px dashed rgba(155, 45, 122, 0.12);
      pointer-events: none;
    }
    .tool-chip {
      position: absolute;
      width: 64px; height: 64px;
      border-radius: 50%;
      background: var(--bg);
      border: 1px solid var(--border);
      box-shadow: 0 6px 20px rgba(42, 37, 53, 0.06);
      display: flex;
      align-items: center;
      justify-content: center;
      top: 50%; left: 50%;
      margin-top: -32px; margin-left: -32px;
      transition: transform 0.3s var(--ease), box-shadow 0.3s var(--ease);
      cursor: default;
      will-change: transform;
    }
    .tool-chip:hover {
      transform: translate(var(--tx, 0), var(--ty, 0)) scale(1.08);
      box-shadow: 0 10px 28px rgba(42, 37, 53, 0.12);
      z-index: 10;
    }
    .tool-chip-inner {
      font-family: var(--serif);
      font-style: italic;
      font-weight: 400;
      font-size: 20px;
      color: var(--fg);
      letter-spacing: -0.02em;
    }
    .tool-chip-label {
      position: absolute;
      top: calc(100% + 10px);
      left: 50%;
      transform: translateX(-50%);
      font-size: 10px;
      font-weight: 500;
      letter-spacing: 0.08em;
      text-transform: uppercase;
      color: var(--fg);
      background: var(--bg);
      border: 1px solid var(--border);
      padding: 4px 10px;
      border-radius: 100px;
      opacity: 0;
      pointer-events: none;
      transition: opacity 0.25s var(--ease);
      white-space: nowrap;
    }
    .tool-chip:hover .tool-chip-label { opacity: 1; }
    @media (prefers-reduced-motion: reduce) {
      .tool-chip { animation: none !important; }
    }
    @media (max-width: 768px) {
      .tools-section { padding: 80px 24px; }
      .tools-stage { height: 360px; }
      .tools-stage::before { width: 180px; height: 180px; }
      .tool-chip { width: 52px; height: 52px; margin-top: -26px; margin-left: -26px; }
      .tool-chip-inner { font-size: 16px; }
    }
```

- [ ] **Step 2: Add tools section HTML between about and projects**

Find the end of the `<section class="about-intro">` block (closing `</section>` around line 976). Immediately after it, insert:

```html
  <!-- ── Tools ── -->
  <section class="tools-section" id="tools" aria-label="Tools I use">
    <div class="tools-label reveal">tools of the trade</div>
    <div class="tools-stage" id="toolsStage">
      <div class="tool-chip" data-radius="200" data-angle="0"   data-speed="55" data-bob="4"><span class="tool-chip-inner">Fg</span><span class="tool-chip-label">Figma</span></div>
      <div class="tool-chip" data-radius="200" data-angle="36"  data-speed="65" data-bob="5"><span class="tool-chip-inner">Fr</span><span class="tool-chip-label">Framer</span></div>
      <div class="tool-chip" data-radius="200" data-angle="72"  data-speed="60" data-bob="3"><span class="tool-chip-inner">Pp</span><span class="tool-chip-label">Protopie</span></div>
      <div class="tool-chip" data-radius="200" data-angle="108" data-speed="70" data-bob="4"><span class="tool-chip-inner">No</span><span class="tool-chip-label">Notion</span></div>
      <div class="tool-chip" data-radius="200" data-angle="144" data-speed="58" data-bob="5"><span class="tool-chip-inner">Mi</span><span class="tool-chip-label">Miro</span></div>
      <div class="tool-chip" data-radius="120" data-angle="0"   data-speed="-75" data-bob="4"><span class="tool-chip-inner">Cc</span><span class="tool-chip-label">Claude Code</span></div>
      <div class="tool-chip" data-radius="120" data-angle="72"  data-speed="-85" data-bob="3"><span class="tool-chip-inner">Cx</span><span class="tool-chip-label">Codex</span></div>
      <div class="tool-chip" data-radius="120" data-angle="144" data-speed="-80" data-bob="5"><span class="tool-chip-inner">Cu</span><span class="tool-chip-label">Cursor</span></div>
      <div class="tool-chip" data-radius="120" data-angle="216" data-speed="-90" data-bob="4"><span class="tool-chip-inner">v0</span><span class="tool-chip-label">v0</span></div>
      <div class="tool-chip" data-radius="120" data-angle="288" data-speed="-70" data-bob="3"><span class="tool-chip-inner">Lv</span><span class="tool-chip-label">Lovable</span></div>
    </div>
  </section>
```

- [ ] **Step 3: Add orbital animation JS**

Find the `<script>` block at the bottom of the file (around line 1131). Insert this block at the beginning of the script (right after the opening `<script>` line):

```javascript
    /* ═══════════════════════════════════════════
       TOOLS — ORBITAL + FLOATING MOTION
    ═══════════════════════════════════════════ */
    (function initToolsOrbital() {
      const stage = document.getElementById('toolsStage');
      if (!stage) return;
      const reduced = window.matchMedia('(prefers-reduced-motion: reduce)').matches;
      const chips = Array.from(stage.querySelectorAll('.tool-chip')).map(el => ({
        el,
        radius: parseFloat(el.dataset.radius) || 200,
        angle: parseFloat(el.dataset.angle) || 0,
        speed: parseFloat(el.dataset.speed) || 60,
        bob: parseFloat(el.dataset.bob) || 4,
        bobPhase: Math.random() * Math.PI * 2,
      }));

      function place(chip, t) {
        const rad = (chip.angle + (t / chip.speed) * 360) * Math.PI / 180;
        const bobY = Math.sin(t / 1200 + chip.bobPhase) * chip.bob;
        const x = Math.cos(rad) * chip.radius;
        const y = Math.sin(rad) * chip.radius + bobY;
        chip.el.style.setProperty('--tx', x + 'px');
        chip.el.style.setProperty('--ty', y + 'px');
        chip.el.style.transform = `translate(${x}px, ${y}px)`;
      }

      if (reduced) {
        chips.forEach(c => place(c, 0));
        return;
      }

      let start = performance.now();
      function frame(now) {
        const t = (now - start) / 10;
        chips.forEach(c => place(c, t));
        requestAnimationFrame(frame);
      }
      requestAnimationFrame(frame);
    })();

```

- [ ] **Step 4: Commit**

```bash
git -C /Users/keshavanand/Desktop/Portfolio add index.html
git -C /Users/keshavanand/Desktop/Portfolio commit -m "feat: add orbital tools section with floating design + AI logo chips"
```

---

## Task 6: Project Card Metric Whispers

**Files:**
- Modify: `index.html` — add `.project-metric` CSS and insert metric lines into each project card

- [ ] **Step 1: Add metric whisper CSS**

Find the `.project-desc` rule in the existing CSS (search for `.project-desc` — likely near other `.project-*` rules). Immediately after the `.project-desc` block, insert:

```css
    .project-metric {
      font-size: 11px;
      font-weight: 500;
      letter-spacing: 0.08em;
      text-transform: uppercase;
      color: var(--accent);
      opacity: 0.72;
      margin-top: 14px;
      margin-bottom: 22px;
      display: flex;
      align-items: center;
      gap: 8px;
    }
    .project-metric::before {
      content: '';
      width: 18px;
      height: 1px;
      background: var(--accent);
      opacity: 0.4;
      flex-shrink: 0;
    }
```

- [ ] **Step 2: Add metric line to BuyingTeams card**

Find the BuyingTeams card body (around line 1023-1025):

```html
          <h3 class="project-name">BuyingTeams</h3>
          <p class="project-desc">Redesigned a B2B fintech platform connecting businesses and banks — simplifying complex financial workflows into intuitive interfaces.</p>
          <a href="work/buyingteams.html" class="project-btn">View Case Study →</a>
```

Replace with:

```html
          <h3 class="project-name">BuyingTeams</h3>
          <p class="project-desc">Redesigned a B2B fintech platform connecting businesses and banks — simplifying complex financial workflows into intuitive interfaces.</p>
          <div class="project-metric">6× login conversion · 50+ features shipped</div>
          <a href="work/buyingteams.html" class="project-btn">View Case Study →</a>
```

- [ ] **Step 3: Add metric line to CXFacts card**

Find the CXFacts card body (around line 1067-1069):

```html
          <h3 class="project-name">CXFacts</h3>
          <p class="project-desc">Designed the Custom Question feature — giving bank-business users the freedom to build their own survey questions. Attracted 2 Major Nordic Banks.</p>
          <a href="work/cxfacts.html" class="project-btn">View Case Study →</a>
```

Replace with:

```html
          <h3 class="project-name">CXFacts</h3>
          <p class="project-desc">Designed the Custom Question feature — giving bank-business users the freedom to build their own survey questions. Attracted 2 Major Nordic Banks.</p>
          <div class="project-metric">2 Nordic banks · 30-day sprint</div>
          <a href="work/cxfacts.html" class="project-btn">View Case Study →</a>
```

- [ ] **Step 4: Commit**

```bash
git -C /Users/keshavanand/Desktop/Portfolio add index.html
git -C /Users/keshavanand/Desktop/Portfolio commit -m "feat(projects): add subtle metric whisper under each case study description"
```

---

## Task 7: Footer Restructure

**Files:**
- Modify: `index.html` — restructure footer HTML, update footer CSS, update responsive footer styles

- [ ] **Step 1: Update footer CSS**

Find the existing footer CSS (lines 643-659):

```css
    footer {
      padding: 28px 52px;
      border-top: 1px solid var(--border);
      display: flex;
      align-items: center;
      justify-content: space-between;
      gap: 16px;
    }
    .footer-copy { font-size: 12px; font-weight: 400; color: var(--muted); }
    .footer-links { display: flex; gap: 24px; list-style: none; }
    .footer-links a {
      font-size: 12px;
      color: var(--muted);
      text-decoration: none;
      transition: color 0.2s;
    }
    .footer-links a:hover { color: var(--fg); }
```

Replace with:

```css
    footer {
      padding: 40px 52px;
      border-top: 1px solid var(--border);
      display: grid;
      grid-template-columns: 1fr auto 1fr;
      align-items: center;
      gap: 24px;
    }
    .footer-copy {
      font-size: 12px; font-weight: 400; color: var(--muted);
      display: flex; flex-direction: column; gap: 4px;
    }
    .footer-copy span { display: block; }
    .footer-copy .loc { opacity: 0.7; }
    .footer-nav {
      display: flex; gap: 26px; list-style: none;
      justify-content: center;
    }
    .footer-nav a {
      font-size: 11px;
      font-weight: 500;
      letter-spacing: 0.1em;
      text-transform: uppercase;
      color: var(--muted);
      text-decoration: none;
      transition: color 0.2s;
    }
    .footer-nav a:hover { color: var(--fg); }
    .footer-links {
      display: flex; gap: 20px; list-style: none;
      justify-content: flex-end;
      align-items: center;
    }
    .footer-links a {
      font-size: 12px;
      color: var(--muted);
      text-decoration: none;
      transition: color 0.2s;
    }
    .footer-links a:hover { color: var(--fg); }
    .footer-links a.email {
      color: var(--fg);
      font-weight: 500;
    }
    .footer-links a.email:hover { color: var(--accent); }
```

- [ ] **Step 2: Update responsive footer rules at 1024px breakpoint**

Find the rule at line 829 area:

```css
      footer   { padding: 24px 36px; }
```

Leave as-is (the breakpoint padding is fine).

- [ ] **Step 3: Update responsive footer rules at 768px breakpoint**

Find the 768px footer rule (around line 866):

```css
      footer {
```

Inspect the existing block (it likely contains `flex-direction` or similar). Replace that entire footer block inside the `@media (max-width: 768px)` with:

```css
      footer {
        grid-template-columns: 1fr;
        text-align: left;
        padding: 32px 24px;
        gap: 20px;
      }
      .footer-nav { justify-content: flex-start; flex-wrap: wrap; gap: 16px; }
      .footer-links { justify-content: flex-start; }
```

(If the existing block also defines flex-direction column, remove that — the new grid-template-columns replaces it.)

- [ ] **Step 4: Replace footer HTML**

Find the existing footer (around line 1120-1127):

```html
  <!-- ── Footer ── -->
  <footer>
    <div class="footer-copy">© 2026 Keshavanand. All rights reserved.</div>
    <ul class="footer-links">
      <li><a href="#">LinkedIn</a></li>
      <li><a href="#">Dribbble</a></li>
      <li><a href="#">Read.cv</a></li>
    </ul>
  </footer>
```

Replace with:

```html
  <!-- ── Footer ── -->
  <footer>
    <div class="footer-copy">
      <span>© 2026 Keshav Anand</span>
      <span class="loc">Hyderabad, India</span>
    </div>
    <ul class="footer-nav">
      <li><a href="#about">About</a></li>
      <li><a href="#work">Work</a></li>
      <li><a href="#">Resume</a></li>
      <li><a href="#tools">Tools</a></li>
    </ul>
    <ul class="footer-links">
      <li><a href="https://linkedin.com/in/keshav-anand" target="_blank" rel="noopener">LinkedIn</a></li>
      <li><a href="https://behance.net/keshavanand4" target="_blank" rel="noopener">Behance</a></li>
      <li><a href="mailto:keshav@thekasper.ai" class="email">Email →</a></li>
    </ul>
  </footer>
```

- [ ] **Step 5: Commit**

```bash
git -C /Users/keshavanand/Desktop/Portfolio add index.html
git -C /Users/keshavanand/Desktop/Portfolio commit -m "feat(footer): restructure to three-column layout with nav echo and updated socials"
```

---

## Task 8: Visual Verification

**Files:**
- None (verification only)

- [ ] **Step 1: Open homepage in browser**

```bash
open http://localhost:3000/
```

If dev server is not running, start it first:

```bash
cd /Users/keshavanand/Desktop/Portfolio && npx serve -l 3000 . &
```

- [ ] **Step 2: Visual checklist**

Verify each:
- ✅ Pulsing green dot visible next to RESUME nav button
- ✅ Tiny eyebrow "SENIOR PRODUCT DESIGNER · DESIGN STRATEGY · AI PRODUCT" above hero title
- ✅ Client marquee scrolling slowly below hero (MSN · ESPN · Getty · etc., 40% opacity)
- ✅ About eyebrow shows 2 lines: main label + "Currently · Founding Designer at FinalLayer" with accent dot
- ✅ About tagline ends with "...measurable impact."
- ✅ Tools section between about and projects: 10 circular chips (Fg, Fr, Pp, No, Mi, Cc, Cx, Cu, v0, Lv) in orbital motion around center with gentle bob
- ✅ Hovering a tool chip: scales up, shows tool name label below
- ✅ Each project card shows a subtle metric line in accent color between description and CTA
- ✅ Footer: 3-column grid — copyright+location left, nav echo center, socials+email right
- ✅ No horizontal scroll on mobile
- ✅ Mobile (<768px): hamburger works, tools section downsizes, footer stacks vertically

- [ ] **Step 3: Final commit (only if minor cleanup needed)**

If all verified and no adjustments needed, no commit needed. Otherwise:

```bash
git -C /Users/keshavanand/Desktop/Portfolio add index.html
git -C /Users/keshavanand/Desktop/Portfolio commit -m "chore: minor facelift polish after visual review"
```

---

## Summary

8 tasks, each a single responsibility:
1. Nav availability dot
2. Hero strategic eyebrow
3. Client marquee strip
4. About updates (currently-at + tagline)
5. Tools orbital section (NEW)
6. Project metric whispers
7. Footer restructure
8. Visual verification

All changes live in `index.html`. No new files. No build step. Subtle accumulation of credibility signals — every individual change is small enough to barely register, collectively they reframe the site from "designer's personal site" to "senior designer ready for hire."
