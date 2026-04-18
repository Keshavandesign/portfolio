# CXFacts Case Study — Design Spec
**Date:** 2026-04-18
**File:** `work/cxfacts.html`
**Reference template:** `work/buyingteams.html`

---

## Overview

A case study page for the CXFacts Custom Question feature. Follows the same design system as BuyingTeams (same tokens, fonts, nav) but with a cleaner, more refined UI — improved whitespace, tighter typography, better card layouts.

---

## Design System

Same tokens as portfolio-wide:
- `--bg: #EDEAE3`, `--fg: #2A2535`, `--accent: #9B2D7A`, `--muted: #7A7490`
- Fonts: DM Serif Display (headings) + Inter (body)
- Same nav, breadcrumb, scroll-reveal, and mobile nav as BuyingTeams

---

## Page Sections

### 1. Hero
- Dark background (`--fg`), ambient blobs (accent + purple)
- Tags: `Product Design` · `B2B SaaS` · `Survey Tool` · `30 Days`
- Large serif title: *CXFacts* with italic *Custom Question* on second line
- Subtitle: "Giving users the freedom to ask what matters most."
- Scroll indicator bottom-right

### 2. Overview
- Light cream background (`--bg`)
- Two-column card grid:
  - **Problem card** (larger): CXFacts lacked custom question creation freedom
  - **Solution card**: Custom Question feature, built with founders/PMs/engineers
  - Accent callout: "2 Major Nordic Banks" attracted
- Stat row below grid: `30 Days` · `4-Stage Process` · `2 Personas`

### 3. Process Timeline (Stepper)
- Sticky horizontal 4-step stepper: `Discover → Define → Design → Deliver`
- Active step highlighted in `--accent`
- Brief note: "Double Diamond methodology, 30-day sprint"
- Each step is a clickable anchor to its sub-section below

### 4. Process Sub-sections (Redesigned)
Full-width alternating rows (text left/right, illustration right/left):

- **01 DISCOVER** — Project Brief: kick-off meeting, MS Planner, tools used. Illustration placeholder: `[ Illustration — Project Brief ]`
- **02 DEFINE** — User Personas (Carl + Vanessa) as inline mini-cards + User Journey Map. Illustration placeholder: `[ Illustration — User Personas & Journey ]`
- **03 DESIGN** — Wireframes → Design System (existing + new components: icons, bar/donut graphs). Two illustration placeholders: `[ Illustration — Wireframes ]` and `[ Illustration — Design System ]`
- **04 DELIVER** — Dev notes, spacing guides, Jira workflow (Bank/Business boards, child issues). Illustration placeholder: `[ Illustration — Handoff & Jira ]`

Each row:
- Eyebrow: `01 DISCOVER` in small caps, accent color
- Section title in DM Serif
- 2–3 sentences of content
- Tool/method pills (e.g. `MS Planner`, `Figma`, `Jira`)
- Large illustration zone: fixed min-height ~480px, dashed border placeholder, swappable with real art

### 5. Final Designs
- Full-width dark panel (matching hero)
- Quote callout: *"Delivered in 30 days. Loved by the team."*
- Stat pills: `30 Days` · `2 Nordic Banks` in accent
- Large centered illustration placeholder: `[ Final Designs ]`

### 6. Challenges & Iterations
- Two cards side by side
- Each card:
  - Eyebrow: `⚙ Iteration 01` / `⚙ Iteration 02` in accent
  - Title: *Previously Used Questions* / *Character Counter*
  - 2–3 sentence description
  - Large illustration placeholder at bottom of card
- Card style: subtle border, slight bg tint, generous padding

### 7. What I Learnt
- Full-width light section
- Large DM Serif italic pull quote: *"Have faith in your decisions."*
- Three takeaways in a clean horizontal row:
  1. Involve users and devs early
  2. Research other survey tools (Google Forms, MS Forms, SurveyMonkey)
  3. Clear designer-developer communication reduces overall time

### 8. Footer CTA
- Same pattern as BuyingTeams
- "← Back to Work" link + next project nudge
- Portfolio footer with © and social links

---

## Illustration Placeholders

All images are placeholders until the user uploads final artwork. Each uses a consistent styled block:
- Dashed border, `--border` color
- Centered label text in `--muted`
- Background: `rgba(42,37,53,0.03)`
- Min-height: 480px (adjustable per section)

---

## Technical Notes

- File location: `work/cxfacts.html`
- Self-contained single HTML file (inline CSS + JS), same pattern as `buyingteams.html`
- Nav links back to `../index.html`
- Breadcrumb: Work → CXFacts
- Scroll-snap on major sections
- Scroll-reveal (`.reveal` class) on all content blocks
- Mobile-responsive: hamburger nav below 768px, single-column stacking for process rows
