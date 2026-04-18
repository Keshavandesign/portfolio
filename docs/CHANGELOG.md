# Changelog

All notable changes to Keshav Anand's portfolio.

---

## [Checkpoint 1] — 2026-04-19

### `index.html`

#### Features
- **Footer redesign** — replaced the flat 3-column inline footer with a full editorial layout: large serif logo left, Quick Links center, Socials right with circular SVG icon buttons, bottom bar with copyright and "To top" arrow. Border-top divider, on-theme background.
- **Footer scroll fix** — added `scroll-snap-align: start` to `<footer>` so mandatory scroll snapping no longer hides it behind the testimonials section.
- **Work section padding** — increased desktop horizontal padding from `120px` to `clamp(52px, 18vw, 320px)` for a tighter, more editorial column width.
- **Custom cursor** — `#kCursor` element with accent color aura glow follows pointer.
- **Text selection** — custom `::selection` styling uses accent color.

#### Fixes
- **Removed duplicate scroll-to-top button** — floating `.scroll-top` button (HTML, CSS, JS) removed; replaced by the footer's "To top" link.
- **Merged duplicate `.project-card` CSS rule** — `cursor: pointer` folded into the primary rule block.
- **Null guards on mobile nav JS** — `hamburger` and `mobileNav` event listeners and DOM mutations wrapped in existence checks.
- **Footer color** — reverted accidental `background: var(--fg)` inversion back to `var(--bg)` with `border-top`.

#### Cleanup
- Removed stale `.footer-links` CSS (class no longer in HTML)
- Removed `opacity` transition remnant from `.footer-nav a` hover state
- Removed `--bg-raw` hack — footer now uses `var(--muted)`, `var(--fg)`, `var(--border)` throughout

---

### `work/rizzle.html`

#### Features
- **Rizzle brand colors** — overrode `--accent` to `#9B59FF` (purple, light) / `#B57BFF` (dark) so every component uses Rizzle's palette instead of the portfolio's pink.
- **Toast timer bar** — replaced vertical left stripe with a horizontal progress bar at the bottom of each toast that depletes left-to-right via `scaleX` CSS animation over 5s.
- **Dropdown overflow fix** — forms demo card gets `overflow: visible` class so dropdown menus escape the container; z-index bumped to 200.
- **Disabled cursor** — `.is-disabled` rows on radio, checkbox, and toggle now show `cursor: not-allowed` on hover (previously `pointer-events: none` suppressed the cursor entirely).
- **Color tokens — Secondary tab** — added neon yellow/lime scale (8 stops, 50–700) as a second color tab alongside the Primary purple/pink scales.
- **Color tokens — Primary pink scale** — added hot pink scale (8 stops) as a second group within the Primary tab.
- **Sidebar containment fix** — `.sidebar-demo-shell` given `contain: layout`, `isolation: isolate`, and `position: relative`; `.rz-sidebar` given `flex-shrink: 0` to prevent children from escaping the demo shell onto the page.

#### Cleanup
- Removed unused `--accent-secondary` CSS variable (defined in both light and dark themes but never referenced)
- Removed all `var(--rz-purple)` references — replaced by `var(--accent)` after brand color override

---

### `work/buyingteams.html`

#### Security
- Added `rel="noopener noreferrer"` to LinkedIn and Twitter footer links (were missing on `target="_blank"` anchors)
- Corrected LinkedIn href to `linkedin.com/in/keshav-anand`

---

### `game.html`

#### Features
- **Fullscreen viewport** — `resize()` now scales by screen height (`scale = vh / H`) so the game fills the full viewport; camera scrolls horizontally.
- **Exit button** — fixed-position `✕` button top-right during gameplay; navigates to `index.html`.
- **End card copy** — "Back to portfolio" changed to "Continue to see the work →".

#### Fixes
- **Dual rAF loop bug** — `endGame()` called inside `update()` was cancelling an already-fired frame, leaving a second loop alive on retry, doubling physics speed. Fixed with `if (state !== 'END') rafId = requestAnimationFrame(loop)`.
- **Enemy/platform positions not reset** — stored `initX`/`initDir` on enemies and `initX`/`initDx` on moving platforms; `resetState()` now restores them so retry starts from scratch.
- **Enemy speed** — all enemy patrol speeds reduced ~30% (max 1.0 px/frame, was 1.6).
- **Platform widths** — Zone 1 ground platforms widened; more runway throughout all zones.
- **Factoid UI** — toast redesigned with accent left bar, "KESHAV INSIGHT" label, drop shadow, better sizing.

---

## Known gaps (not yet implemented)

- `assets/sculpture.png` — about section image 404; needs a real asset
- Project card visuals are CSS mockups — replace with screenshots when ready
- Case study illustration zones are placeholder outlines
- `/about` page does not exist
- Logo images have `alt=""` — functional brand logos should have descriptive alt text
- Game palette is hardcoded hex values independent of portfolio CSS variables
