# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Dev Server

```bash
npx serve -l 3000 .
```

Open `http://localhost:3000`. No build step — all files are plain HTML/CSS/JS served directly.

## Repository Structure

```
index.html          — homepage (single file: all CSS inline in <style>, all JS at bottom)
game.html           — easter egg platformer game (canvas-based, accessible via sculpture click)
work/
  rizzle.html       — Rizzle Design System case study (interactive component demos)
  cxfacts.html      — CXFacts B2B fintech case study
  buyingteams.html  — BuyingTeams case study
  figma-redesign.html, blinking-cursor.html, notion-redesign.html, etc. — mini project pages
Assets/             — images and audio referenced by HTML files
favicon.svg         — root favicon, referenced by all pages via relative path
docs/CHANGELOG.md   — checkpoint log of significant changes
```

## Architecture

**No framework, no build system.** Every page is a self-contained HTML file. CSS lives in a `<style>` block in `<head>`; JS lives in `<script>` blocks at the bottom of `<body>`. No external JS libraries.

**Design tokens** are CSS custom properties on `:root` and `[data-theme="dark"]`. Light and dark themes are the only two states. Token names: `--bg`, `--fg`, `--muted`, `--accent`, `--border`, `--border-strong`, `--surface`, `--sans`, `--serif`, `--ease`.

**Theme persistence** uses `localStorage('theme')` with a blocking inline script before `<style>` to prevent FOUC. All pages replicate this pattern.

**Scroll reveal system** (`index.html`): elements with class `reveal` are observed by `IntersectionObserver`; the observer adds `visible` when they enter the viewport. CSS transitions on `.reveal` → `.reveal.visible` drive the animation. Stagger is via `.reveal-d1 / -d2 / -d3` delay classes.

**Word-stagger system** (`index.html`): elements with `data-split="words"` get their text nodes split into `<span class="word-item">` spans at runtime, each with a `--wi` CSS variable for staggered `transition-delay`. The observer adds `split-visible` to trigger the animation.

**Rizzle case study** (`work/rizzle.html`) overrides `--accent` to `#BB00ED` (light) / `#D359F3` (dark) and adds `--accent-form: #D7FF00` for interactive form components. It also uses a `Work Sans` font for UI demos. A fixed floating index (`.ds-index`) spy-scrolls via `IntersectionObserver` against section IDs.

**Nav hide-on-scroll** pattern is shared across case study pages: a `scroll` listener compares `scrollY` to a previous value and toggles `nav.hide` / `nav.scrolled` classes.

## Design System Rules (Non-Negotiable)

These are Keshav's taste constraints. Violating them will be reverted.

**Typography**
- `clamp()` on every display and heading size — nothing display-scale is fixed
- DM Serif Display for headlines and emotional accent; Inter for body, labels, data
- 3–4 type sizes max per page
- Letter-spacing: tight for serif (`-0.02` to `-0.04em`), tracked for uppercase labels

**Copy & Voice**
- **Em dashes (—) are banned.** Use a period, a comma, or rewrite the sentence
- Active voice only in process sections. "I prepared the files" not "Files were prepared"
- No "pain points", "cognitive load", "leverage", "synergy", "impactful", "streamlined"
- Numbers in copy must be earned (cited from real outcomes)

**Layout**
- Horizontal margins: `52px` (≥1024px) → `36px` (768–1024) → `24px` (480–768) → `20px` (<480px)
- Ghost numbers behind work cards: `opacity: 0.035`, `160px` serif, set via `content: attr(data-num)` on `::before`
- No boxy cards with border + background + shadow. Work cards are editorial full-width spreads
- Asset dominates — visual should occupy at least 55% of visual weight

**Interactions**
- Easing curve: `cubic-bezier(0.22, 1, 0.36, 1)` — defined as `var(--ease)`
- Hover lift (`translateY(-10px)`) only on actual card containers
- `display: block` must be explicit on all `<img>` inside flex containers — never rely on inline default
- Never `loading="lazy"` on interactive SVGs or logos

## Per-Page Notes

**`index.html`** — homepage uses `scroll-snap-type: y mandatory`. Adding a new section requires `scroll-snap-align: start` on the element or scroll snapping will break.

**`work/rizzle.html`** — interactive component demos (buttons, forms, modals, toasts, dropdowns) are self-contained CSS+HTML with no external dependencies. Demo cards use `background: #070708` with scoped dark overrides for all child components. The sidebar section uses a static PNG (`Assets/rizzle-nav.png`) — do not attempt to rebuild it as a CSS replica.

**`game.html`** — canvas-based platformer. Accessible as an easter egg by clicking the sculpture on the homepage (also linked directly). Uses `AudioContext` for sound effects.

## Code Quality Standard

This codebase is reviewed by Codex. Every change must pass Codex-level scrutiny: no dead variables, no unused constants, no redundant DOM lookups, no path accumulation bugs (`ctx.beginPath()` before every shape), no innerHTML with dynamic content (use textContent), no unchecked null dereferences, no duplicate cleanup loops, asset filenames must match references exactly (case-sensitive). Canvas perf rules: cache gradients keyed on the dimension that triggers a rebuild (scale, vh, ay); never allocate gradients or set shadowBlur inside the hot render path unless unavoidable. All IntersectionObservers must call unobserve/disconnect after their one-shot trigger fires.
