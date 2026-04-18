# Homepage Strategic Facelift — Design Spec

**Date:** 2026-04-18
**Files affected:** `index.html` (nav, hero, about, new tools section, projects, new footer)
**Reference:** `docs/Keshav_Anand_Resume.pdf`

---

## Goal

Subtle facelift that signals "Senior Product Designer, AI-native, hireable" without disrupting the current charm. Every change is a whisper — individually barely noticed, collectively shift the perception from "designer's personal site" to "senior designer ready to get hired."

## Design Principle

**Whisper, don't shout.** Credibility cues layered throughout the page; none demand attention. Keep existing cream/purple/serif aesthetic unchanged. Same tokens, same fonts, same rhythm — just sharper positioning.

---

## Section-by-Section Changes

### 1. Nav — Availability signal

**Change:** Add a small green pulse dot next to the RESUME pill button.
- Dot: 8px circle, `#22C55E` (green-500), slow pulse animation (2s ease-in-out).
- Positioned just left of the button, vertically centered.
- Reads as "open to work" at a glance.

**Keep:** Logo, nav links, button style, mobile hamburger.

---

### 2. Hero — Tiny strategic eyebrow

**Add:** An eyebrow line *above* the headline, tiny type.
```
SENIOR PRODUCT DESIGNER · DESIGN STRATEGY · AI PRODUCT
```
- 10px, 600 weight, 0.14em letter-spacing, uppercase.
- Color: `var(--muted)`.
- Positioned above the "Hi, I'm Keshav..." title with ~20px gap.

**Keep:** Headline text, italic "Experience" accent, gradient blobs, scroll badge — fully unchanged.

---

### 3. Client Strip — Quiet marquee (NEW)

**Placement:** Immediately below the hero, before the About section.

**Content:** Horizontal marquee, auto-scrolling.
```
MSN · ESPN · Getty · Rizzle · FinalLayer · CXFacts · Lagorii · Excytech
```
Duplicated 2x for seamless loop.

**Style:**
- 12px, 500 weight, uppercase, 0.3em letter-spacing.
- Color: `var(--muted)` at 40% opacity.
- Slow scroll: ~60s full loop.
- Thin top and bottom hairline borders (1px, `var(--border)`).
- Vertical padding: 24px.
- No eyebrow or label — just the floating names.

**Intent:** Reads as ambient credibility. User scanning won't focus on it, but registers "worked with real brands."

---

### 4. About Section — Currently-at + sharpened tagline

**Current eyebrow:** `'FOR INQUISITIVE MINDS'`

**Change:** Replace with two-line eyebrow:
```
'FOR INQUISITIVE MINDS'
CURRENTLY · FOUNDING DESIGNER AT FINALLAYER
```
- Second line smaller (9px), muted, prefixed with a small accent dot.

**Tagline refinement:** Soften/sharpen the About tagline to nod at outcomes:
- Before (current): *whatever exists now*
- After: Short addition at the end, something like: *"...designing AI-native products with measurable impact."*
- (Exact copy decided in implementation based on current tagline.)

**Keep:** Bust SVG placeholder, ABOUT ME button.

---

### 5. Tools Section — Orbital + Floating Logos (NEW)

**Placement:** Between About section and Featured Projects.

**Layout:**
- Full-width section, ~480px tall.
- Single tiny lowercase label top-left: `tools of the trade` (or similar).
- Centered circular container (~520px diameter).
- 10 logo chips drifting within the container.

**Motion — mix of B (floating) + C (orbital):**
- Each logo is a small white circular chip (60px diameter) with the logo at ~32px centered.
- All chips follow a gentle orbital path around the container center — but at staggered radii and different speeds so they never sync.
- Layered on top: a subtle float/bob (translateY ±4px, 3-4s loop) to make motion feel organic, not mechanical.
- Slow orbit speeds (45-90s full rotation). Direction varied.
- No interactivity required (keep simple); subtle hover: chip scales to 1.05 and shows tooltip with tool name.

**Logos (order TBD for visual balance):**
- Design: Figma, Framer, Protopie, Notion
- AI/Vibe-coding: Claude Code, Codex, Cursor, v0, Lovable, Stitch

**Color treatment:**
- Chip background: `var(--bg)` with 1px `var(--border)`.
- Drop shadow: `0 6px 20px rgba(42,37,53,0.06)`.
- Logos: original brand colors (SVG).

**Fallback:** If a logo SVG isn't available, use a monogram chip with the first 1-2 letters in DM Serif Display, matching the chip style.

**Responsive:**
- Desktop (>1024px): 520px container, 10 chips.
- Tablet (768-1024): 400px container, fewer chips visible at once.
- Mobile (<768px): reduce to static grid of chips (3 columns, no motion) OR smaller orbital container. Motion optional on mobile (respect `prefers-reduced-motion`).

---

### 6. Featured Projects — Metric whispers

**Current:** Two project cards (BuyingTeams, CXFacts) with tags, name, description, CTA.

**Change:** Add a small metric line between the description and the CTA button.
- Style: 11px, 500 weight, 0.08em letter-spacing, uppercase, `var(--accent)` at 70% opacity.
- Prefixed with small bullet or dash.
- Examples:
  - BuyingTeams: `— 6× login conversion · 50+ features shipped`
  - CXFacts: `— 2 Nordic banks · 30-day sprint`

**Keep:** Card structure, mockups, tags, descriptions, CTA buttons — fully unchanged.

---

### 7. Footer — Simple, matches vibe (NEW)

**Structure:** Three-column grid, matches nav width (52px horizontal padding).

**Left:**
```
© 2026 Keshav Anand
Hyderabad, India
```
Two lines, 12px, muted.

**Center:**
Small horizontal nav echo:
```
About · Work · Resume · Contact
```
11px, muted, letter-spacing 0.1em.

**Right:**
Social links + email:
```
LinkedIn · Behance · Email →
```
With email being the only one that slightly stands out (subtle accent on hover).

**Divider:** Thin top border, 1px `var(--border)`.
**Padding:** Vertical 40px, horizontal 52px.

**Responsive:**
- Below 768px: stack to single column, left-aligned, 16px gap.

---

## What's NOT Changing

- Color tokens (cream/dark/accent/muted)
- Fonts (DM Serif Display + Inter)
- Hero headline & italic "Experience" treatment
- Scroll badge, gradient blobs
- Project card mockup structure
- Scroll-reveal animations
- Mobile hamburger

---

## Success Criteria

A visitor who has seen the current site should say: *"Did something change? It feels more polished, not sure why."*

A recruiter visiting for the first time should register within 10 seconds:
- Senior designer ✓
- AI-native / ahead of curve ✓
- Works with real brands (MSN, ESPN, Getty) ✓
- Available for work ✓
- Has measurable outcomes ✓

All without a single shouty element.

---

## Open Questions (to resolve during implementation)

1. **Exact About tagline rewrite** — will be finalized by reading current copy and proposing 2 alternatives.
2. **Tool logos** — need to source official SVG logos for each brand. Fallback to monogram chips if unavailable.
3. **Mobile motion** — confirm static grid OR scaled-down orbital for tools section.
