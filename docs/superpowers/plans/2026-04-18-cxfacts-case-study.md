# CXFacts Case Study Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build `work/cxfacts.html` — a polished, self-contained case study page for the CXFacts Custom Question feature, using the same design system as `work/buyingteams.html` but with a cleaner UI and a redesigned alternating-row process section with illustration placeholders.

**Architecture:** Single self-contained HTML file with inline CSS and JS. Shares design tokens, fonts, and nav pattern with `buyingteams.html`. Process section replaces the broken phase-tabs with a 4-step stepper + alternating full-width rows (text ↔ illustration). All images are styled placeholder blocks swappable with real artwork later.

**Tech Stack:** Plain HTML, CSS custom properties, vanilla JS, Google Fonts (DM Serif Display + Inter)

**Spec:** `docs/superpowers/specs/2026-04-18-cxfacts-case-study-design.md`
**Reference file:** `work/buyingteams.html`

---

## File Map

| File | Action | Purpose |
|------|--------|---------|
| `work/cxfacts.html` | Create | Full case study page |
| `index.html` | Modify | Link CXFacts card to `work/cxfacts.html` |

---

### Task 1: Scaffold + Base CSS + Nav

**Files:**
- Create: `work/cxfacts.html`

- [ ] **Step 1: Create the file with document head, CSS reset, design tokens, fonts, and nav CSS**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>CXFacts — Keshav Anand</title>
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link href="https://fonts.googleapis.com/css2?family=DM+Serif+Display:ital@0;1&family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet" />
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

    :root {
      --bg: #EDEAE3;
      --fg: #2A2535;
      --muted: #7A7490;
      --accent: #9B2D7A;
      --border: rgba(42, 37, 53, 0.1);
      --sans: 'Inter', sans-serif;
      --serif: 'DM Serif Display', serif;
      --ease: cubic-bezier(0.22, 1, 0.36, 1);
    }

    html { scroll-behavior: smooth; scroll-padding-top: 80px; -webkit-font-smoothing: antialiased; }
    body { background: var(--bg); color: var(--fg); font-family: var(--sans); line-height: 1.6; overflow-x: hidden; }

    /* NAV */
    nav {
      position: fixed; top: 0; left: 0; right: 0; z-index: 100;
      display: grid; grid-template-columns: auto 1fr auto; align-items: center;
      padding: 28px 52px;
      transition: transform 0.45s var(--ease), padding 0.4s var(--ease), background 0.35s ease;
    }
    nav.hide { transform: translateY(-110%); }
    nav.scrolled { padding: 14px 52px; background: rgba(237,234,227,0.96); backdrop-filter: blur(8px); }
    .nav-logo { display: flex; align-items: center; gap: 10px; text-decoration: none; color: var(--fg); }
    .logo-mark { flex-shrink: 0; transition: transform 0.35s var(--ease); }
    .nav-logo:hover .logo-mark { transform: rotate(5deg) scale(1.05); }
    .nav-center { display: flex; justify-content: center; }
    .nav-links { display: flex; gap: 44px; list-style: none; }
    .nav-links a {
      font-size: 11px; font-weight: 400; letter-spacing: 0.1em;
      text-transform: uppercase; text-decoration: none; color: var(--muted);
      position: relative; transition: color 0.2s;
    }
    .nav-links a::after {
      content: ''; position: absolute; bottom: -2px; left: 0; right: 0;
      height: 1px; background: var(--accent); transform: scaleX(0);
      transform-origin: left; transition: transform 0.3s var(--ease);
    }
    .nav-links a:hover { color: var(--fg); }
    .nav-links a:hover::after { transform: scaleX(1); }
    .nav-right { display: flex; justify-content: flex-end; }
    .nav-btn {
      font-size: 11px; font-weight: 500; letter-spacing: 0.08em;
      text-transform: uppercase; text-decoration: none; color: var(--fg);
      border: 1.5px solid rgba(42,37,53,0.35); padding: 9px 22px; border-radius: 100px;
      transition: background 0.3s var(--ease), color 0.3s, border-color 0.3s, transform 0.3s var(--ease);
    }
    .nav-btn:hover { background: var(--fg); color: var(--bg); border-color: var(--fg); transform: scale(1.03); }
    .nav-hamburger {
      display: none; flex-direction: column; justify-content: center;
      align-items: flex-end; gap: 5px; width: 32px; height: 32px;
      background: none; border: none; cursor: pointer; padding: 0; margin-left: auto;
    }
    .nav-hamburger span { display: block; height: 1.5px; background: var(--fg); border-radius: 2px; transition: transform 0.35s var(--ease), opacity 0.25s, width 0.3s var(--ease); }
    .nav-hamburger span:nth-child(1) { width: 22px; }
    .nav-hamburger span:nth-child(2) { width: 16px; }
    .nav-hamburger span:nth-child(3) { width: 22px; }
    .nav-hamburger.open span:nth-child(1) { width: 22px; transform: translateY(6.5px) rotate(45deg); }
    .nav-hamburger.open span:nth-child(2) { opacity: 0; transform: scaleX(0); }
    .nav-hamburger.open span:nth-child(3) { width: 22px; transform: translateY(-6.5px) rotate(-45deg); }
    .mobile-nav {
      position: fixed; inset: 0; z-index: 99; background: var(--bg);
      display: flex; flex-direction: column; align-items: center; justify-content: center;
      pointer-events: none; opacity: 0; transition: opacity 0.35s var(--ease);
    }
    .mobile-nav.open { pointer-events: all; opacity: 1; }
    .mobile-nav-links { list-style: none; text-align: center; }
    .mobile-nav-links li { overflow: hidden; }
    .mobile-nav-links a {
      display: block; font-family: var(--serif); font-size: clamp(36px, 8vw, 56px);
      font-weight: 400; letter-spacing: -0.03em; color: var(--fg); text-decoration: none;
      padding: 6px 0; transform: translateY(40px); opacity: 0;
      transition: color 0.2s, transform 0.5s var(--ease), opacity 0.5s var(--ease);
    }
    .mobile-nav.open .mobile-nav-links li:nth-child(1) a { transition-delay: 0.05s; }
    .mobile-nav.open .mobile-nav-links li:nth-child(2) a { transition-delay: 0.10s; }
    .mobile-nav.open .mobile-nav-links li:nth-child(3) a { transition-delay: 0.15s; }
    .mobile-nav.open .mobile-nav-links li:nth-child(4) a { transition-delay: 0.20s; }
    .mobile-nav.open .mobile-nav-links a { transform: translateY(0); opacity: 1; }
    .mobile-nav-links a:hover { color: var(--accent); }
    .mobile-nav-footer { position: absolute; bottom: 40px; font-size: 12px; color: var(--muted); letter-spacing: 0.06em; opacity: 0; transform: translateY(12px); transition: opacity 0.4s var(--ease) 0.3s, transform 0.4s var(--ease) 0.3s; }
    .mobile-nav.open .mobile-nav-footer { opacity: 1; transform: translateY(0); }

    /* BREADCRUMB */
    .breadcrumb {
      position: fixed; top: 88px; left: 52px; z-index: 90;
      display: flex; align-items: center; gap: 8px;
      font-size: 11px; font-weight: 400; letter-spacing: 0.06em; color: var(--muted);
      opacity: 0; transform: translateY(-6px);
      animation: breadcrumbIn 0.5s var(--ease) 0.4s forwards;
    }
    .breadcrumb a { color: var(--muted); text-decoration: none; transition: color 0.2s; }
    .breadcrumb a:hover { color: var(--accent); }
    .breadcrumb-sep { opacity: 0.4; }
    .breadcrumb-current { color: var(--fg); font-weight: 500; }
    .breadcrumb-back { display: none; align-items: center; gap: 4px; font-size: 11px; letter-spacing: 0.06em; color: var(--muted); text-decoration: none; transition: color 0.2s, transform 0.3s var(--ease); }
    .breadcrumb-back:hover { color: var(--fg); transform: translateX(-3px); }
    @keyframes breadcrumbIn { to { opacity: 1; transform: translateY(0); } }

    /* SCROLL REVEAL */
    .reveal { opacity: 0; transform: translateY(18px); transition: opacity 0.6s var(--ease), transform 0.6s var(--ease); }
    .reveal.visible { opacity: 1; transform: translateY(0); }
    .reveal-d1 { transition-delay: 0.08s; }
    .reveal-d2 { transition-delay: 0.16s; }
    .reveal-d3 { transition-delay: 0.24s; }
    .reveal-d4 { transition-delay: 0.32s; }

    /* ANIMATIONS */
    @keyframes fadeSlideUp { from { opacity: 0; transform: translateY(24px); } to { opacity: 1; transform: translateY(0); } }
    @keyframes floatY { 0%, 100% { transform: translateY(0); } 50% { transform: translateY(-10px); } }
  </style>
</head>
<body>
  <!-- Mobile nav overlay -->
  <div class="mobile-nav" id="mobileNav">
    <ul class="mobile-nav-links">
      <li><a href="../index.html#about" class="mobile-link">About</a></li>
      <li><a href="../index.html#work"  class="mobile-link">Work</a></li>
      <li><a href="#"                   class="mobile-link">Resume</a></li>
      <li><a href="#"                   class="mobile-link">Contact</a></li>
    </ul>
    <div class="mobile-nav-footer">keshavanand.com</div>
  </div>

  <!-- Nav -->
  <nav id="nav">
    <a href="../index.html" class="nav-logo">
      <img src="../Assets/Logo.svg" alt="Keshav Anand" class="logo-mark" style="height: 40px; width: auto;" />
    </a>
    <div class="nav-center">
      <ul class="nav-links">
        <li><a href="../index.html#about">About</a></li>
        <li><a href="../index.html#work">Work</a></li>
        <li><a href="#">Resume</a></li>
        <li><a href="#">Contact</a></li>
      </ul>
    </div>
    <div class="nav-right"><a href="#" class="nav-btn">Resume</a></div>
    <button class="nav-hamburger" id="hamburger" aria-label="Toggle menu">
      <span></span><span></span><span></span>
    </button>
  </nav>

  <!-- Breadcrumb -->
  <nav class="breadcrumb" aria-label="Breadcrumb">
    <a href="../index.html">Home</a>
    <span class="breadcrumb-sep">/</span>
    <a href="../index.html#work">Work</a>
    <span class="breadcrumb-sep">/</span>
    <span class="breadcrumb-current">CXFacts</span>
    <a href="../index.html#work" class="breadcrumb-back">← Back</a>
  </nav>
```

- [ ] **Step 2: Verify file exists**

```bash
ls -la /Users/keshavanand/Desktop/Portfolio/work/cxfacts.html
```
Expected: file listed

---

### Task 2: Hero Section CSS + HTML

**Files:**
- Modify: `work/cxfacts.html` — add hero CSS inside `<style>` and hero HTML after breadcrumb

- [ ] **Step 1: Add hero CSS inside the `<style>` block, after the `@keyframes floatY` rule**

```css
    /* ─── HERO ─── */
    .cs-hero {
      min-height: 100svh; background: var(--fg);
      display: flex; flex-direction: column; align-items: flex-start; justify-content: flex-end;
      padding: 120px 52px 80px; position: relative; overflow: hidden;
    }
    .cs-hero::before, .cs-hero::after {
      content: ''; position: absolute; border-radius: 50%;
      pointer-events: none; opacity: 0.12; filter: blur(80px);
    }
    .cs-hero::before { width: 600px; height: 600px; background: var(--accent); top: -100px; right: -100px; animation: floatY 8s ease-in-out infinite; }
    .cs-hero::after  { width: 400px; height: 400px; background: #7A54B8; bottom: -80px; left: 20%; animation: floatY 10s ease-in-out infinite reverse; }
    .cs-hero-meta { display: flex; gap: 10px; flex-wrap: wrap; margin-bottom: 32px; animation: fadeSlideUp 0.6s var(--ease) 0.2s both; }
    .cs-tag {
      font-size: 10px; font-weight: 500; letter-spacing: 0.12em; text-transform: uppercase;
      color: rgba(237,234,227,0.5); border: 1px solid rgba(237,234,227,0.2);
      padding: 5px 14px; border-radius: 100px;
    }
    .cs-tag.accent { color: var(--accent); border-color: var(--accent); background: rgba(155,45,122,0.12); }
    .cs-hero-title {
      font-family: var(--serif); font-size: clamp(48px, 7vw, 110px);
      font-weight: 400; letter-spacing: -0.04em; line-height: 0.95; color: var(--bg);
      animation: fadeSlideUp 0.7s var(--ease) 0.35s both;
    }
    .cs-hero-title em { font-style: italic; color: rgba(237,234,227,0.45); }
    .cs-hero-sub {
      margin-top: 28px; max-width: 480px;
      font-size: 14px; font-weight: 300; line-height: 1.7; color: rgba(237,234,227,0.55);
      animation: fadeSlideUp 0.7s var(--ease) 0.5s both;
    }
    .cs-hero-scroll { position: absolute; bottom: 48px; right: 52px; animation: fadeSlideUp 0.6s var(--ease) 0.8s both; }
    .cs-hero-scroll span { display: flex; flex-direction: column; align-items: center; gap: 8px; font-size: 9px; letter-spacing: 0.14em; text-transform: uppercase; color: rgba(237,234,227,0.3); }
    .cs-hero-scroll-line { width: 1px; height: 48px; background: linear-gradient(to bottom, rgba(237,234,227,0.3), transparent); animation: floatY 2s ease-in-out infinite; }
```

- [ ] **Step 2: Add hero HTML after the breadcrumb `</nav>`**

```html
  <!-- HERO -->
  <section class="cs-hero" id="top">
    <div class="cs-hero-meta">
      <span class="cs-tag">B2B SaaS</span>
      <span class="cs-tag">Survey Tool</span>
      <span class="cs-tag">30 Days</span>
      <span class="cs-tag accent">Product Design</span>
    </div>
    <h1 class="cs-hero-title">
      CXFacts<br><em>Custom Question</em>
    </h1>
    <p class="cs-hero-sub">
      Giving users the freedom to ask what matters most — a new feature built in 30 days that attracted 2 Major Nordic Banks.
    </p>
    <div class="cs-hero-scroll">
      <span>
        <div class="cs-hero-scroll-line"></div>
        Scroll
      </span>
    </div>
  </section>
```

---

### Task 3: Overview Section CSS + HTML

**Files:**
- Modify: `work/cxfacts.html`

- [ ] **Step 1: Add overview CSS after hero CSS**

```css
    /* ─── OVERVIEW ─── */
    .cs-overview { padding: 100px 52px; }
    .cs-overview-grid { display: grid; grid-template-columns: 1.6fr 1fr; gap: 20px; margin-bottom: 20px; }
    .cs-card {
      background: rgba(42,37,53,0.04); border: 1px solid var(--border);
      border-radius: 20px; padding: 40px;
      transition: transform 0.35s var(--ease), box-shadow 0.35s var(--ease), border-color 0.3s;
    }
    .cs-card:hover { transform: translateY(-4px); box-shadow: 0 16px 40px rgba(42,37,53,0.08); border-color: rgba(155,45,122,0.2); }
    .cs-card-label {
      font-size: 10px; font-weight: 600; letter-spacing: 0.14em; text-transform: uppercase;
      color: var(--accent); margin-bottom: 18px; display: flex; align-items: center; gap: 10px;
    }
    .cs-card-label::after { content: ''; flex: 1; height: 1px; background: rgba(155,45,122,0.2); }
    .cs-card-body { font-size: 14px; line-height: 1.8; color: var(--fg); }
    .cs-card.problem .cs-card-body { font-family: var(--serif); font-size: clamp(16px, 1.4vw, 20px); font-style: italic; line-height: 1.65; letter-spacing: -0.01em; }
    .cs-stat-callout {
      display: inline-flex; align-items: center; gap: 8px; margin-top: 20px;
      background: rgba(155,45,122,0.08); border: 1px solid rgba(155,45,122,0.2);
      border-radius: 100px; padding: 8px 18px;
      font-size: 12px; font-weight: 600; color: var(--accent); letter-spacing: 0.04em;
    }
    .cs-stat-callout span { font-size: 18px; font-family: var(--serif); font-style: italic; }
    .cs-meta-row { display: grid; grid-template-columns: repeat(3, 1fr); gap: 20px; }
    .cs-meta-card {
      background: rgba(42,37,53,0.04); border: 1px solid var(--border);
      border-radius: 20px; padding: 32px 36px;
      transition: transform 0.35s var(--ease), box-shadow 0.35s var(--ease), border-color 0.3s;
    }
    .cs-meta-card:hover { transform: translateY(-4px); box-shadow: 0 16px 40px rgba(42,37,53,0.08); border-color: rgba(155,45,122,0.2); }
    .cs-meta-label { font-size: 10px; font-weight: 600; letter-spacing: 0.14em; text-transform: uppercase; color: var(--accent); margin-bottom: 16px; }
    .cs-role-list { list-style: none; display: flex; flex-direction: column; gap: 5px; }
    .cs-role-list li { font-size: 13px; color: var(--fg); }
    .cs-role-list li::before { content: '— '; color: var(--muted); }
    .cs-tools-list { display: flex; flex-wrap: wrap; gap: 8px; }
    .cs-tool-pill {
      font-size: 11px; font-weight: 500; letter-spacing: 0.04em; color: var(--fg);
      background: var(--bg); border: 1px solid var(--border); padding: 5px 14px; border-radius: 100px;
      transition: background 0.2s, color 0.2s;
    }
    .cs-tool-pill:hover { background: var(--fg); color: var(--bg); }
    .cs-time-display { font-family: var(--serif); font-style: italic; font-size: clamp(22px, 2vw, 30px); letter-spacing: -0.02em; color: var(--fg); line-height: 1.3; }
    .cs-time-sub { margin-top: 6px; font-size: 12px; color: var(--muted); }
```

- [ ] **Step 2: Add overview HTML after the hero `</section>`**

```html
  <!-- OVERVIEW -->
  <section class="cs-overview" id="overview">
    <div class="cs-overview-grid">
      <div class="cs-card reveal">
        <div class="cs-card-label">The Problem</div>
        <div class="cs-card-body">
          CXFacts is a collaboration tool in the bank-business relationship — enabling high-quality surveys, requests, and ideas. It maintains quality by using only top-tier questions that drive data and action.
          <br><br>
          However, CXFacts lacked the freedom to let users create their own custom questions, limiting how precisely they could target specific topics.
        </div>
      </div>
      <div class="cs-card problem reveal reveal-d1">
        <div class="cs-card-label">The Solution</div>
        <div class="cs-card-body">
          "A new feature that lets users create the most important question types for data collection — directly inside CXFacts."
        </div>
        <div class="cs-stat-callout"><span>2</span> Major Nordic Banks attracted</div>
      </div>
    </div>
    <div class="cs-meta-row">
      <div class="cs-meta-card reveal">
        <div class="cs-meta-label">Role</div>
        <ul class="cs-role-list">
          <li>Product Designer</li>
          <li>UX Research</li>
          <li>Interaction Design</li>
          <li>Design System</li>
        </ul>
      </div>
      <div class="cs-meta-card reveal reveal-d1">
        <div class="cs-meta-label">Tools</div>
        <div class="cs-tools-list">
          <span class="cs-tool-pill">Figma</span>
          <span class="cs-tool-pill">MS Planner</span>
          <span class="cs-tool-pill">Jira</span>
          <span class="cs-tool-pill">Lucid</span>
        </div>
      </div>
      <div class="cs-meta-card reveal reveal-d2">
        <div class="cs-meta-label">Timeline</div>
        <div class="cs-time-display">30 Days</div>
        <div class="cs-time-sub">Discover → Deliver</div>
      </div>
    </div>
  </section>
```

---

### Task 4: Process Stepper CSS + HTML

**Files:**
- Modify: `work/cxfacts.html`

- [ ] **Step 1: Add process stepper CSS after overview CSS**

```css
    /* ─── PROCESS STEPPER ─── */
    .cs-process-intro {
      padding: 80px 52px 48px; text-align: center;
      background: rgba(42,37,53,0.03);
    }
    .cs-eyebrow { font-size: 10px; font-weight: 600; letter-spacing: 0.16em; text-transform: uppercase; color: var(--accent); margin-bottom: 14px; }
    .cs-section-title { font-family: var(--serif); font-size: clamp(32px, 4vw, 52px); font-weight: 400; letter-spacing: -0.03em; line-height: 1.1; color: var(--fg); margin-bottom: 10px; }
    .cs-section-title em { font-style: italic; color: var(--muted); }
    .cs-section-sub { font-size: 14px; color: var(--muted); max-width: 420px; margin: 0 auto 48px; }

    .cs-stepper {
      display: flex; align-items: center; justify-content: center;
      gap: 0; flex-wrap: wrap; padding: 0 52px 56px;
      background: rgba(42,37,53,0.03);
    }
    .cs-step {
      display: flex; align-items: center; gap: 12px;
      padding: 14px 28px; cursor: pointer;
      border-bottom: 2px solid transparent;
      transition: border-color 0.3s var(--ease);
      text-decoration: none;
    }
    .cs-step:hover .cs-step-label { color: var(--fg); }
    .cs-step.active { border-bottom-color: var(--accent); }
    .cs-step-num {
      width: 26px; height: 26px; border-radius: 50%;
      border: 1.5px solid var(--border); display: flex; align-items: center; justify-content: center;
      font-size: 10px; font-weight: 600; color: var(--muted);
      transition: background 0.3s, border-color 0.3s, color 0.3s;
    }
    .cs-step.active .cs-step-num { background: var(--accent); border-color: var(--accent); color: #fff; }
    .cs-step-label { font-size: 11px; font-weight: 500; letter-spacing: 0.1em; text-transform: uppercase; color: var(--muted); transition: color 0.25s; }
    .cs-step.active .cs-step-label { color: var(--fg); }
    .cs-step-connector { width: 40px; height: 1px; background: var(--border); }
    @media (max-width: 600px) { .cs-step-connector { width: 16px; } .cs-step { padding: 12px 14px; } }
```

- [ ] **Step 2: Add stepper HTML after overview `</section>`**

```html
  <!-- PROCESS INTRO + STEPPER -->
  <section class="cs-process-intro" id="process">
    <div class="cs-eyebrow reveal">Design Process</div>
    <h2 class="cs-section-title reveal reveal-d1">Four stages.<br><em>One sprint.</em></h2>
    <p class="cs-section-sub reveal reveal-d2">Double Diamond methodology. 30 days from kick-off to working prototype.</p>
  </section>
  <div class="cs-stepper reveal">
    <a class="cs-step active" href="#discover">
      <span class="cs-step-num">01</span>
      <span class="cs-step-label">Discover</span>
    </a>
    <div class="cs-step-connector"></div>
    <a class="cs-step" href="#define">
      <span class="cs-step-num">02</span>
      <span class="cs-step-label">Define</span>
    </a>
    <div class="cs-step-connector"></div>
    <a class="cs-step" href="#design">
      <span class="cs-step-num">03</span>
      <span class="cs-step-label">Design</span>
    </a>
    <div class="cs-step-connector"></div>
    <a class="cs-step" href="#deliver">
      <span class="cs-step-num">04</span>
      <span class="cs-step-label">Deliver</span>
    </a>
  </div>
```

---

### Task 5: Process Rows CSS + HTML (all 4 stages)

**Files:**
- Modify: `work/cxfacts.html`

- [ ] **Step 1: Add process rows CSS after stepper CSS**

```css
    /* ─── PROCESS ROWS ─── */
    .cs-process-row {
      display: grid; grid-template-columns: 1fr 1fr;
      min-height: 600px;
    }
    .cs-process-row.flip { direction: rtl; }
    .cs-process-row.flip > * { direction: ltr; }

    .cs-process-text {
      padding: 80px 64px; display: flex; flex-direction: column; justify-content: center;
      background: var(--bg);
    }
    .cs-process-row:nth-child(even) .cs-process-text { background: rgba(42,37,53,0.03); }

    .cs-process-stage {
      font-size: 10px; font-weight: 700; letter-spacing: 0.18em; text-transform: uppercase;
      color: var(--accent); margin-bottom: 20px; display: flex; align-items: center; gap: 12px;
    }
    .cs-process-stage::after { content: ''; flex: 0 0 40px; height: 1px; background: rgba(155,45,122,0.3); }

    .cs-process-heading {
      font-family: var(--serif); font-size: clamp(28px, 3vw, 44px);
      font-weight: 400; letter-spacing: -0.03em; line-height: 1.1;
      color: var(--fg); margin-bottom: 20px;
    }

    .cs-process-body { font-size: 14px; line-height: 1.85; color: var(--fg); margin-bottom: 28px; max-width: 520px; }

    .cs-pill-row { display: flex; flex-wrap: wrap; gap: 8px; }
    .cs-pill {
      font-size: 11px; font-weight: 500; letter-spacing: 0.06em; color: var(--fg);
      background: var(--bg); border: 1px solid var(--border);
      padding: 5px 14px; border-radius: 100px;
    }
    .cs-process-row:nth-child(even) .cs-pill { background: rgba(42,37,53,0.03); }

    .cs-illus-zone {
      display: flex; align-items: center; justify-content: center;
      background: rgba(42,37,53,0.03); position: relative; overflow: hidden; min-height: 480px;
    }
    .cs-process-row:nth-child(even) .cs-illus-zone { background: var(--bg); }

    .cs-illus-placeholder {
      display: flex; flex-direction: column; align-items: center; justify-content: center;
      gap: 16px; width: 80%; height: 70%; min-height: 320px;
      border: 1.5px dashed rgba(42,37,53,0.18); border-radius: 20px;
      background: rgba(42,37,53,0.02);
    }
    .cs-illus-placeholder-icon {
      width: 48px; height: 48px; border-radius: 12px;
      background: rgba(155,45,122,0.08); display: flex; align-items: center; justify-content: center;
    }
    .cs-illus-placeholder-icon svg { width: 22px; height: 22px; stroke: var(--accent); fill: none; stroke-width: 1.5; stroke-linecap: round; stroke-linejoin: round; }
    .cs-illus-label { font-size: 11px; font-weight: 500; letter-spacing: 0.08em; text-transform: uppercase; color: var(--muted); text-align: center; }
    .cs-illus-sub { font-size: 11px; color: rgba(42,37,53,0.35); }

    /* Ghost watermark number */
    .cs-process-row { position: relative; }
    .cs-ghost-num {
      font-family: var(--serif); font-style: italic;
      font-size: clamp(100px, 14vw, 180px); font-weight: 400;
      color: rgba(42,37,53,0.04); line-height: 1;
      position: absolute; bottom: -20px; right: 48px;
      pointer-events: none; user-select: none;
    }
```

- [ ] **Step 2: Add all 4 process row HTML blocks after the stepper `</div>`**

```html
  <!-- 01 DISCOVER -->
  <div class="cs-process-row" id="discover">
    <div class="cs-process-text reveal">
      <div class="cs-process-stage">01 Discover</div>
      <h3 class="cs-process-heading">Project Brief &amp;<br>Understanding Needs</h3>
      <p class="cs-process-body">
        Kicked off with the CXFacts founders to align on scope and objectives for the Custom Question feature. Created a concise project brief and organised all design tasks to keep the 30-day sprint on track.
      </p>
      <div class="cs-pill-row">
        <span class="cs-pill">Kick-off Meeting</span>
        <span class="cs-pill">MS Planner</span>
        <span class="cs-pill">Project Brief</span>
        <span class="cs-pill">Stakeholder Alignment</span>
      </div>
    </div>
    <div class="cs-illus-zone">
      <div class="cs-illus-placeholder">
        <div class="cs-illus-placeholder-icon">
          <svg viewBox="0 0 24 24"><path d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2"/><rect x="9" y="3" width="6" height="4" rx="2"/><path d="M9 12h6M9 16h4"/></svg>
        </div>
        <div class="cs-illus-label">Project Brief</div>
        <div class="cs-illus-sub">Illustration coming soon</div>
      </div>
    </div>
    <div class="cs-ghost-num">01</div>
  </div>

  <!-- 02 DEFINE -->
  <div class="cs-process-row flip" id="define">
    <div class="cs-process-text reveal">
      <div class="cs-process-stage">02 Define</div>
      <h3 class="cs-process-heading">Personas &amp;<br>User Journey</h3>
      <p class="cs-process-body">
        Created two user personas from research findings. <strong>Carl</strong> often faced missing questions in the CXFacts database, limiting his ability to target specific topics. <strong>Vanessa</strong> shared similar goals and frustrations. A user journey map visualised the friction points in the existing Feedback Session flow.
      </p>
      <div class="cs-pill-row">
        <span class="cs-pill">User Personas</span>
        <span class="cs-pill">Journey Mapping</span>
        <span class="cs-pill">Research Synthesis</span>
        <span class="cs-pill">Empathy Mapping</span>
      </div>
    </div>
    <div class="cs-illus-zone">
      <div class="cs-illus-placeholder">
        <div class="cs-illus-placeholder-icon">
          <svg viewBox="0 0 24 24"><circle cx="9" cy="7" r="4"/><path d="M3 21v-2a4 4 0 014-4h4a4 4 0 014 4v2"/><path d="M16 3.13a4 4 0 010 7.75"/><path d="M21 21v-2a4 4 0 00-3-3.87"/></svg>
        </div>
        <div class="cs-illus-label">User Personas &amp; Journey Map</div>
        <div class="cs-illus-sub">Illustration coming soon</div>
      </div>
    </div>
    <div class="cs-ghost-num">02</div>
  </div>

  <!-- 03 DESIGN -->
  <div class="cs-process-row" id="design">
    <div class="cs-process-text reveal">
      <div class="cs-process-stage">03 Design</div>
      <h3 class="cs-process-heading">Wireframes &amp;<br>Design System</h3>
      <p class="cs-process-body">
        Started with low-fidelity wireframes to address core user needs, then moved to high-fidelity screens using the existing CXFacts design system. Designed new components — icons for question types, and bar &amp; donut graphs to visualise collected data.
      </p>
      <div class="cs-pill-row">
        <span class="cs-pill">Lo-Fi Wireframes</span>
        <span class="cs-pill">Hi-Fi Screens</span>
        <span class="cs-pill">Design System</span>
        <span class="cs-pill">New Components</span>
        <span class="cs-pill">Figma</span>
      </div>
    </div>
    <div class="cs-illus-zone">
      <div class="cs-illus-placeholder">
        <div class="cs-illus-placeholder-icon">
          <svg viewBox="0 0 24 24"><rect x="3" y="3" width="18" height="18" rx="2"/><path d="M3 9h18M9 21V9"/></svg>
        </div>
        <div class="cs-illus-label">Wireframes &amp; Design System</div>
        <div class="cs-illus-sub">Illustration coming soon</div>
      </div>
    </div>
    <div class="cs-ghost-num">03</div>
  </div>

  <!-- 04 DELIVER -->
  <div class="cs-process-row flip" id="deliver">
    <div class="cs-process-text reveal">
      <div class="cs-process-stage">04 Deliver</div>
      <h3 class="cs-process-heading">Handoff &amp;<br>Developer Collaboration</h3>
      <p class="cs-process-body">
        Files were prepared with dev notes and spacing guides to enable a 1:1 replication of the UI. Used Jira to communicate tasks across Front-End and Back-End teams — with separate boards for Bank and Business platforms, and child issues to break major tasks into trackable chunks.
      </p>
      <div class="cs-pill-row">
        <span class="cs-pill">Dev Notes</span>
        <span class="cs-pill">Spacing Guides</span>
        <span class="cs-pill">Jira</span>
        <span class="cs-pill">Handoff</span>
      </div>
    </div>
    <div class="cs-illus-zone">
      <div class="cs-illus-placeholder">
        <div class="cs-illus-placeholder-icon">
          <svg viewBox="0 0 24 24"><path d="M12 2L2 7l10 5 10-5-10-5z"/><path d="M2 17l10 5 10-5"/><path d="M2 12l10 5 10-5"/></svg>
        </div>
        <div class="cs-illus-label">Dev Handoff &amp; Jira</div>
        <div class="cs-illus-sub">Illustration coming soon</div>
      </div>
    </div>
    <div class="cs-ghost-num">04</div>
  </div>
```

---

### Task 6: Final Designs + Challenges Sections CSS + HTML

**Files:**
- Modify: `work/cxfacts.html`

- [ ] **Step 1: Add final designs + challenges CSS after process rows CSS**

```css
    /* ─── FINAL DESIGNS ─── */
    .cs-final {
      background: var(--fg); padding: 100px 52px; text-align: center; position: relative; overflow: hidden;
    }
    .cs-final::before { content: ''; position: absolute; width: 500px; height: 500px; background: var(--accent); border-radius: 50%; opacity: 0.08; filter: blur(100px); top: -100px; right: -100px; pointer-events: none; }
    .cs-final-eyebrow { font-size: 10px; font-weight: 600; letter-spacing: 0.16em; text-transform: uppercase; color: rgba(237,234,227,0.4); margin-bottom: 16px; }
    .cs-final-quote {
      font-family: var(--serif); font-style: italic; font-size: clamp(24px, 3.5vw, 48px);
      letter-spacing: -0.03em; line-height: 1.15; color: var(--bg);
      max-width: 700px; margin: 0 auto 32px;
    }
    .cs-final-stats { display: flex; justify-content: center; gap: 12px; flex-wrap: wrap; margin-bottom: 56px; }
    .cs-final-stat {
      font-size: 11px; font-weight: 600; letter-spacing: 0.1em; text-transform: uppercase;
      color: var(--accent); background: rgba(155,45,122,0.12);
      border: 1px solid rgba(155,45,122,0.3); padding: 8px 20px; border-radius: 100px;
    }
    .cs-final-illus {
      width: 100%; max-width: 900px; margin: 0 auto; min-height: 480px;
      border: 1.5px dashed rgba(237,234,227,0.15); border-radius: 24px;
      display: flex; flex-direction: column; align-items: center; justify-content: center; gap: 16px;
      background: rgba(237,234,227,0.03);
    }
    .cs-final-illus-label { font-size: 11px; font-weight: 500; letter-spacing: 0.1em; text-transform: uppercase; color: rgba(237,234,227,0.3); }

    /* ─── CHALLENGES ─── */
    .cs-challenges { padding: 100px 52px; }
    .cs-challenges-header { margin-bottom: 52px; }
    .cs-challenges-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 24px; }
    .cs-challenge-card {
      background: rgba(42,37,53,0.04); border: 1px solid var(--border);
      border-radius: 20px; padding: 40px; overflow: hidden;
      transition: transform 0.35s var(--ease), box-shadow 0.35s var(--ease), border-color 0.3s;
    }
    .cs-challenge-card:hover { transform: translateY(-4px); box-shadow: 0 16px 40px rgba(42,37,53,0.08); border-color: rgba(155,45,122,0.2); }
    .cs-challenge-eyebrow { font-size: 10px; font-weight: 700; letter-spacing: 0.14em; text-transform: uppercase; color: var(--accent); margin-bottom: 14px; display: flex; align-items: center; gap: 8px; }
    .cs-challenge-title { font-family: var(--serif); font-size: clamp(20px, 2vw, 28px); font-weight: 400; letter-spacing: -0.02em; color: var(--fg); margin-bottom: 16px; }
    .cs-challenge-body { font-size: 13px; line-height: 1.8; color: var(--fg); margin-bottom: 32px; }
    .cs-challenge-illus {
      width: 100%; min-height: 240px; border: 1.5px dashed var(--border); border-radius: 16px;
      display: flex; flex-direction: column; align-items: center; justify-content: center; gap: 10px;
      background: rgba(42,37,53,0.02);
    }
    .cs-challenge-illus-label { font-size: 10px; font-weight: 500; letter-spacing: 0.08em; text-transform: uppercase; color: var(--muted); }
```

- [ ] **Step 2: Add final designs + challenges HTML after the deliver process row `</div>`**

```html
  <!-- FINAL DESIGNS -->
  <section class="cs-final" id="final">
    <div class="cs-final-eyebrow reveal">Final Designs</div>
    <p class="cs-final-quote reveal reveal-d1">"Delivered in 30 days.<br>Loved by the team."</p>
    <div class="cs-final-stats reveal reveal-d2">
      <span class="cs-final-stat">30 Days</span>
      <span class="cs-final-stat">2 Nordic Banks</span>
      <span class="cs-final-stat">Now Under Development</span>
    </div>
    <div class="cs-final-illus reveal reveal-d3">
      <svg width="40" height="40" viewBox="0 0 24 24" fill="none" stroke="rgba(237,234,227,0.2)" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"><rect x="3" y="3" width="18" height="18" rx="2"/><circle cx="8.5" cy="8.5" r="1.5"/><polyline points="21 15 16 10 5 21"/></svg>
      <div class="cs-final-illus-label">Final Designs — Illustration Coming Soon</div>
    </div>
  </section>

  <!-- CHALLENGES & ITERATIONS -->
  <section class="cs-challenges" id="iterations">
    <div class="cs-challenges-header">
      <div class="cs-eyebrow reveal">Challenges &amp; Iterations</div>
      <h2 class="cs-section-title reveal reveal-d1">Where the design<br><em>got better.</em></h2>
    </div>
    <div class="cs-challenges-grid">
      <div class="cs-challenge-card reveal">
        <div class="cs-challenge-eyebrow">⚙ Iteration 01</div>
        <h3 class="cs-challenge-title">Previously Used Questions</h3>
        <p class="cs-challenge-body">
          After testing lo-fi wireframes, users indicated they'd want to reuse custom questions across multiple feedback sessions. We iterated by adding a side drawer — triggered by "+ Add Previous Question" — accessible from the same screen where new questions are created.
        </p>
        <div class="cs-challenge-illus">
          <div class="cs-challenge-illus-label">Illustration coming soon</div>
        </div>
      </div>
      <div class="cs-challenge-card reveal reveal-d1">
        <div class="cs-challenge-eyebrow">⚙ Iteration 02</div>
        <h3 class="cs-challenge-title">Character Counter</h3>
        <p class="cs-challenge-body">
          Without character limits, edge-case inputs broke the layout. We researched the maximum characters viable for Question and Option fields, then added a real-time character counter to every input — preventing overflow while keeping the experience seamless.
        </p>
        <div class="cs-challenge-illus">
          <div class="cs-challenge-illus-label">Illustration coming soon</div>
        </div>
      </div>
    </div>
  </section>
```

---

### Task 7: Learnings + Footer + JS

**Files:**
- Modify: `work/cxfacts.html`

- [ ] **Step 1: Add learnings, next-project, footer CSS, scroll-top/email-btn CSS, and responsive CSS after challenges CSS**

```css
    /* ─── LEARNINGS ─── */
    .cs-learnings {
      padding: 100px 52px; background: rgba(42,37,53,0.03); text-align: center;
    }
    .cs-learnings-quote {
      font-family: var(--serif); font-style: italic;
      font-size: clamp(28px, 4vw, 56px); letter-spacing: -0.03em; line-height: 1.1;
      color: var(--fg); max-width: 780px; margin: 0 auto 56px;
    }
    .cs-learnings-quote span { color: var(--accent); }
    .cs-learnings-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 20px; text-align: left; }
    .cs-learning-card {
      background: var(--bg); border: 1px solid var(--border); border-radius: 20px; padding: 36px;
      transition: transform 0.35s var(--ease), box-shadow 0.35s var(--ease), border-color 0.3s;
    }
    .cs-learning-card:hover { transform: translateY(-4px); box-shadow: 0 16px 40px rgba(42,37,53,0.08); border-color: rgba(155,45,122,0.2); }
    .cs-learning-num { font-family: var(--serif); font-style: italic; font-size: 40px; color: rgba(155,45,122,0.2); line-height: 1; margin-bottom: 16px; }
    .cs-learning-text { font-size: 14px; line-height: 1.75; color: var(--fg); }

    /* ─── NEXT PROJECT ─── */
    .cs-next { padding: 100px 52px; display: flex; flex-direction: column; align-items: center; text-align: center; }
    .cs-next-label { font-size: 10px; font-weight: 600; letter-spacing: 0.16em; text-transform: uppercase; color: var(--muted); margin-bottom: 20px; }
    .cs-next-title { font-family: var(--serif); font-size: clamp(32px, 5vw, 64px); font-weight: 400; letter-spacing: -0.03em; line-height: 1.05; color: var(--fg); margin-bottom: 36px; }
    .cs-next-title em { font-style: italic; color: var(--muted); }
    .cs-next-link {
      font-size: 11px; font-weight: 500; letter-spacing: 0.1em; text-transform: uppercase;
      color: var(--fg); border: 1.5px solid rgba(42,37,53,0.35); padding: 14px 32px;
      border-radius: 100px; text-decoration: none; display: inline-flex; align-items: center; gap: 8px;
      transition: background 0.3s var(--ease), color 0.3s, border-color 0.3s, transform 0.3s var(--ease);
    }
    .cs-next-link:hover { background: var(--fg); color: var(--bg); border-color: var(--fg); transform: scale(1.04); }
    .cs-next-link-arrow { transition: transform 0.3s var(--ease); }
    .cs-next-link:hover .cs-next-link-arrow { transform: translateX(4px); }

    /* ─── FOOTER ─── */
    footer { padding: 28px 52px; border-top: 1px solid var(--border); display: flex; align-items: center; justify-content: space-between; gap: 16px; }
    .footer-copy { font-size: 12px; color: var(--muted); }
    .footer-links { display: flex; gap: 24px; list-style: none; }
    .footer-links a { font-size: 12px; color: var(--muted); text-decoration: none; transition: color 0.2s; }
    .footer-links a:hover { color: var(--fg); }

    /* ─── SCROLL TOP / EMAIL BTN ─── */
    .scroll-top {
      position: fixed; right: 28px; bottom: 28px; width: 40px; height: 40px;
      border: 1.5px solid var(--border); border-radius: 50%; background: var(--bg);
      display: flex; align-items: center; justify-content: center; z-index: 200;
      opacity: 0; font-size: 15px; color: var(--muted); text-decoration: none;
      transition: opacity 0.3s, transform 0.3s var(--ease), color 0.2s; pointer-events: none;
    }
    .scroll-top.visible { opacity: 1; pointer-events: auto; }
    .scroll-top:hover { transform: translateY(-3px); color: var(--fg); }
    .email-button {
      position: fixed; left: 28px; bottom: 28px; padding: 12px 28px; border-radius: 100px;
      background: #000; color: #fff; border: none; font-size: 12px; font-weight: 600;
      letter-spacing: 0.08em; text-transform: uppercase; text-decoration: none;
      display: flex; align-items: center; gap: 10px; z-index: 200; opacity: 0;
      transition: opacity 0.3s, transform 0.3s var(--ease), box-shadow 0.3s var(--ease);
      pointer-events: none; box-shadow: 0 8px 24px rgba(0,0,0,0.35); cursor: pointer;
    }
    .email-button.visible { opacity: 1; pointer-events: auto; }
    .email-button:hover { transform: translateY(-3px); box-shadow: 0 16px 40px rgba(0,0,0,0.5); }

    /* ─── RESPONSIVE ─── */
    @media (max-width: 1024px) {
      nav { padding: 22px 36px; } nav.scrolled { padding: 14px 36px; }
      .breadcrumb { left: 36px; }
      .cs-hero, .cs-overview, .cs-challenges, .cs-final, .cs-learnings, .cs-next, .cs-process-intro { padding-left: 36px; padding-right: 36px; }
      .cs-stepper { padding-left: 36px; padding-right: 36px; }
      .cs-overview-grid { grid-template-columns: 1fr; }
      .cs-meta-row { grid-template-columns: 1fr 1fr; }
      .cs-challenges-grid { grid-template-columns: 1fr; }
      .cs-learnings-grid { grid-template-columns: 1fr 1fr; }
      .cs-process-row { grid-template-columns: 1fr; }
      .cs-process-row.flip { direction: ltr; }
      .cs-illus-zone { min-height: 320px; }
      footer { padding: 24px 36px; }
    }
    @media (max-width: 768px) {
      nav { padding: 20px 24px; }
      .nav-center, .nav-right { display: none; }
      .nav-hamburger { display: flex; }
      .breadcrumb { top: 76px; left: 24px; }
      .breadcrumb-sep, .breadcrumb a:not(:last-of-type) { display: none; }
      .breadcrumb-back { display: flex; }
      .cs-hero, .cs-overview, .cs-challenges, .cs-final, .cs-learnings, .cs-next, .cs-process-intro { padding: 72px 24px; }
      .cs-stepper { padding: 0 24px 40px; flex-wrap: wrap; justify-content: flex-start; }
      .cs-process-text { padding: 48px 24px; }
      .cs-meta-row { grid-template-columns: 1fr; }
      .cs-learnings-grid { grid-template-columns: 1fr; }
      .cs-ghost-num { display: none; }
      footer { flex-direction: column; align-items: flex-start; padding: 24px 24px 36px; gap: 16px; }
    }
```

- [ ] **Step 2: Add learnings, next, footer HTML + scroll-top + email-btn after challenges `</section>`**

```html
  <!-- WHAT I LEARNT -->
  <section class="cs-learnings" id="learnings">
    <p class="cs-learnings-quote reveal">
      "Have faith in your decisions — and embrace the fact that you did your <span>best</span> with the insights available."
    </p>
    <div class="cs-learnings-grid">
      <div class="cs-learning-card reveal">
        <div class="cs-learning-num">01</div>
        <p class="cs-learning-text">Involving users and developers early reduces overall project time and creates a more complete experience — different perspectives help you empathise better.</p>
      </div>
      <div class="cs-learning-card reveal reveal-d1">
        <div class="cs-learning-num">02</div>
        <p class="cs-learning-text">Clear designer-developer communication through dev notes and spacing guides is as important as the design itself. Precision at handoff = fewer revision cycles.</p>
      </div>
      <div class="cs-learning-card reveal reveal-d2">
        <div class="cs-learning-num">03</div>
        <p class="cs-learning-text">Studying how users behave inside survey tools — Google Forms, Microsoft Forms, SurveyMonkey — revealed patterns that made the Custom Question UX intuitive from day one.</p>
      </div>
    </div>
  </section>

  <!-- NEXT PROJECT -->
  <section class="cs-next">
    <div class="cs-next-label reveal">Next Project</div>
    <h2 class="cs-next-title reveal reveal-d1">See more<br><em>work</em></h2>
    <a href="../index.html#work" class="cs-next-link reveal reveal-d2">
      Back to Work <span class="cs-next-link-arrow">→</span>
    </a>
  </section>

  <!-- FOOTER -->
  <footer>
    <span class="footer-copy">© 2024 Keshav Anand</span>
    <ul class="footer-links">
      <li><a href="#">LinkedIn</a></li>
      <li><a href="#">Dribbble</a></li>
      <li><a href="#">Twitter</a></li>
    </ul>
  </footer>

  <!-- SCROLL TOP -->
  <a href="#top" class="scroll-top" id="scrollTop" aria-label="Scroll to top">↑</a>

  <!-- EMAIL BUTTON -->
  <a href="mailto:keshav@thekasper.ai" class="email-button" id="emailBtn">
    <svg class="email-icon" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M4 4h16c1.1 0 2 .9 2 2v12c0 1.1-.9 2-2 2H4c-1.1 0-2-.9-2-2V6c0-1.1.9-2 2-2z"/><polyline points="22,6 12,13 2,6"/></svg>
    Let's Talk
  </a>
```

- [ ] **Step 3: Add closing `</body></html>` and the full JS block**

```html
  <script>
    // ── Nav hide/show on scroll ──
    const nav = document.getElementById('nav');
    const breadcrumb = document.querySelector('.breadcrumb');
    let lastY = 0, ticking = false;
    window.addEventListener('scroll', () => {
      if (!ticking) {
        requestAnimationFrame(() => {
          const y = window.scrollY;
          nav.classList.toggle('hide', y > lastY && y > 120);
          nav.classList.toggle('scrolled', y > 40);
          if (breadcrumb) breadcrumb.style.top = nav.classList.contains('scrolled') ? '68px' : '88px';
          lastY = y;
          ticking = false;
        });
        ticking = true;
      }
    });

    // ── Mobile nav ──
    const hamburger = document.getElementById('hamburger');
    const mobileNav = document.getElementById('mobileNav');
    hamburger.addEventListener('click', () => {
      hamburger.classList.toggle('open');
      mobileNav.classList.toggle('open');
      document.body.style.overflow = mobileNav.classList.contains('open') ? 'hidden' : '';
    });
    document.querySelectorAll('.mobile-link').forEach(link => {
      link.addEventListener('click', () => {
        hamburger.classList.remove('open');
        mobileNav.classList.remove('open');
        document.body.style.overflow = '';
      });
    });

    // ── Scroll-reveal ──
    const revealEls = document.querySelectorAll('.reveal');
    const io = new IntersectionObserver((entries) => {
      entries.forEach(e => { if (e.isIntersecting) { e.target.classList.add('visible'); io.unobserve(e.target); } });
    }, { threshold: 0.12 });
    revealEls.forEach(el => io.observe(el));

    // ── Scroll top + email button ──
    const scrollTop = document.getElementById('scrollTop');
    const emailBtn = document.getElementById('emailBtn');
    window.addEventListener('scroll', () => {
      const past = window.scrollY > 400;
      scrollTop.classList.toggle('visible', past);
      emailBtn.classList.toggle('visible', past);
    });

    // ── Active stepper step on scroll ──
    const steps = document.querySelectorAll('.cs-step');
    const sections = ['discover','define','design','deliver'].map(id => document.getElementById(id));
    const stepObserver = new IntersectionObserver((entries) => {
      entries.forEach(e => {
        if (e.isIntersecting) {
          const idx = sections.indexOf(e.target);
          steps.forEach((s, i) => s.classList.toggle('active', i === idx));
        }
      });
    }, { threshold: 0.4 });
    sections.forEach(s => s && stepObserver.observe(s));
  </script>
</body>
</html>
```

---

### Task 8: Wire Up index.html + Commit

**Files:**
- Modify: `index.html` — update the CXFacts project card's "VIEW CASE STUDY" link to `work/cxfacts.html`

- [ ] **Step 1: Find the CXFacts entry in index.html**

```bash
grep -n "cxfacts\|CXFacts\|cxfact" /Users/keshavanand/Desktop/Portfolio/index.html -i
```

- [ ] **Step 2: Update the href on the CXFacts VIEW CASE STUDY button to `work/cxfacts.html`**

Look for the anchor tag near the CXFacts project card and change its `href` attribute to `work/cxfacts.html`.

- [ ] **Step 3: Commit everything**

```bash
cd /Users/keshavanand/Desktop/Portfolio
git add work/cxfacts.html index.html
git commit -m "feat: add CXFacts case study page (v1)"
```

- [ ] **Step 4: Open in browser and verify**

```bash
open http://localhost:3000/work/cxfacts.html
```

Check:
- Hero renders with dark background, tags, title, blobs
- Overview shows problem/solution cards and meta row
- Stepper displays 4 steps, active state updates on scroll
- All 4 process rows alternate correctly with illustration placeholders
- Final Designs dark section renders
- Challenges grid shows 2 cards
- Learnings section has pull quote + 3 cards
- Footer and scroll-to-top button work
- No horizontal overflow on mobile widths
