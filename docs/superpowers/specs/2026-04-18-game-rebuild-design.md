# Game Rebuild Design — Keshav's Easter Egg Platformer
Date: 2026-04-18

## One-liner
A lean, pixelated Mario-style platformer in `game.html` that ends with a score-tiered free design audit offer.

---

## Architecture
- Single HTML file, single `<script>` block, no classes, no frameworks
- Three flat arrays: `platforms[]`, `enemies[]`, `entities[]` (powerups, projectiles, spikes, coins, artifacts)
- Camera: one `cameraX` offset, player held at ~35% from left edge
- Integer pixel positions everywhere — `Math.floor` on all draw calls for natural arcade jitter
- `image-rendering: pixelated` on canvas, no anti-aliasing

---

## Visual Style
- Sky: bright blue-to-purple gradient (`#74B9FF → #A29BFE`)
- Platforms: solid bright blocks — greens, oranges, warm purples per zone
- Player: accent stick figure (`#9B2D7A` / `#E569A9` dark), white eyes
- Enemies: chunky pixel blobs, unique color per zone
- Coins: small yellow pixel circles
- Design artifacts: Figma frame, pencil, grid — pixel-drawn, distinct teal/pink/orange colors

---

## Level Layout (~4800px wide, 5 zones)

| Zone | Width | Theme | Obstacles |
|------|-------|-------|-----------|
| 1 | 0–800 | Ground intro | Small gaps, 2 patrolling blobs |
| 2 | 800–2000 | Multi-tier climb | Ceiling spikes in tight passages, 3 enemies |
| 3 | 2000–3200 | Moving platforms | Wide pit, 4 moving platforms, 2 enemies |
| 4 | 3200–4000 | Dense zone | Mixed enemies + powerup placement before hard section |
| 5 | 4000–4800 | Final sprint | Gaps + spikes + enemies mixed, flag at far right |

Ground floor at y=580. World height 700px.

---

## Entities

### Enemies (3 types)
- **Blob** (green): wide, slow patrol, squish animation
- **Spike-bot** (orange): faster, smaller patrol range
- **Grunt** (purple): large, slow, higher damage area

### Powerups (timer-based)
- **Lightning bolt** (yellow): press `X` to throw pixel projectile, kills enemies + breaks spikes. 8s timer.
- **Shield star** (cyan): invincibility, walk through enemies, flicker effect. 5s timer.

### Collectibles
- **Coins**: +1pt each, scattered throughout all zones, ~40 total
- **Design artifacts**: +5pts each, 4 hidden across zones (Figma frame, pencil, grid, cursor)
- **Powerup pickup**: +2pts bonus

---

## Scoring & End Card Tiers

| Score | Headline | Audit Framing |
|-------|----------|---------------|
| 0–20 | "You explored. That's a start." | Discovery session |
| 21–50 | "Good instincts." | Validation pass |
| 51–80 | "Sharp eye." | Focused deep dive |
| 81+ | "You see what most miss." | Strategic partnership |

End card: centered HTML card over dark backdrop. `#EDEAE3` bg, thin `rgba(42,37,53,0.35)` border, 16px radius. DM Serif Display italic headline, Inter muted body. Accent pill CTA: "Claim your free audit" (mailto). Secondary ghost button: back to portfolio.

---

## SFX (Web Audio API, procedural)
- Jump: quick ascending sine blip (80→400Hz, 0.08s)
- Die: descending square wave wail (400→80Hz, 0.4s)
- Collect coin: short ascending arpeggio (C-E-G, 0.05s each)
- Collect artifact: longer arpeggio with shimmer
- Collect powerup: ascending sweep + punch
- Throw: short zap (noise burst, 0.1s)
- Enemy hit: pop (square wave, 0.05s)
- Win: triumphant chiptune riff (C-E-G-C octave up)
- Lose: sad descending tritone

---

## Difficulty Tuning
- Player: `MOVE_SPEED 3.2`, `JUMP_FORCE -11`, `GRAVITY 0.52`, `FRICTION 0.80`
- Enemies: slow (1.0–1.6 px/frame), predictable patrol routes
- Gaps: max 120px (2 player widths), always jumpable
- Spikes: always visible, never hidden
- 3 lives, death resets to last zone checkpoint (not full restart)
- Powerups placed just before hardest section in each zone

---

## What Stays the Same
- File: `game.html` (same URL, same easter egg access)
- Canvas-based rendering
- Keyboard controls: arrows/WASD + Space/W to jump
- Redirect to `index.html` on win/lose (after 3s)
