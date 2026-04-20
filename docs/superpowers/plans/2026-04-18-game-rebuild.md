# Game Rebuild Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Rebuild `game.html` as a lean, pixelated Mario-style platformer with coins, collectibles, powerups, procedural SFX, and a score-tiered free audit end card.

**Architecture:** Single `game.html` file, flat JS arrays for all entities, one camera offset. No classes, no frameworks. Render everything with canvas 2D, end card as HTML overlay. Integer pixel positions throughout for arcade jitter.

**Tech Stack:** Vanilla HTML/CSS/JS, Canvas 2D API, Web Audio API.

---

## File Structure

- **Modify:** `game.html` — full rebuild, single file. All styles in `<style>`, all JS in one `<script>`.

---

### Task 1: Core Scaffolding + Game Loop

**Files:**
- Modify: `game.html` (full rewrite)

- [ ] **Step 1: Wipe game.html and write the shell**

Replace entire contents of `game.html` with:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Keshav's Game</title>
  <link href="https://fonts.googleapis.com/css2?family=DM+Serif+Display:ital@0;1&family=Inter:wght@400;500;600&display=swap" rel="stylesheet" />
  <style>
    *, *::before, *::after { margin: 0; padding: 0; box-sizing: border-box; }
    html, body { width: 100%; height: 100%; overflow: hidden; background: #1a1a2e; }
    canvas {
      display: block;
      position: absolute; top: 0; left: 0;
      image-rendering: pixelated;
      image-rendering: crisp-edges;
    }
    /* End card overlay */
    #endCard {
      display: none;
      position: fixed; inset: 0;
      background: rgba(26, 26, 46, 0.82);
      align-items: center; justify-content: center;
      z-index: 100;
      font-family: 'Inter', sans-serif;
    }
    #endCard.show { display: flex; }
    .card {
      background: #EDEAE3;
      border: 1.5px solid rgba(42,37,53,0.35);
      border-radius: 16px;
      padding: 40px 48px;
      max-width: 420px;
      width: 90%;
      text-align: center;
    }
    .card-tag {
      font-family: 'Inter', sans-serif;
      font-size: 10px;
      font-weight: 600;
      letter-spacing: 0.12em;
      text-transform: uppercase;
      color: #7A7490;
      margin-bottom: 16px;
    }
    .card-headline {
      font-family: 'DM Serif Display', serif;
      font-style: italic;
      font-size: 36px;
      color: #2A2535;
      line-height: 1.15;
      margin-bottom: 10px;
    }
    .card-score {
      font-size: 13px;
      color: #7A7490;
      margin-bottom: 8px;
    }
    .card-body {
      font-size: 14px;
      color: #7A7490;
      line-height: 1.6;
      margin-bottom: 28px;
    }
    .card-cta {
      display: inline-block;
      background: #9B2D7A;
      color: #EDEAE3;
      font-family: 'Inter', sans-serif;
      font-size: 13px;
      font-weight: 600;
      letter-spacing: 0.04em;
      padding: 12px 28px;
      border-radius: 100px;
      text-decoration: none;
      border: none; cursor: pointer;
      margin-bottom: 12px;
    }
    .card-secondary {
      display: block;
      font-size: 12px;
      color: #7A7490;
      cursor: pointer;
      background: none; border: none;
      font-family: 'Inter', sans-serif;
      text-decoration: underline;
      margin: 0 auto;
    }
  </style>
</head>
<body>
  <canvas id="g"></canvas>

  <!-- End card -->
  <div id="endCard">
    <div class="card">
      <div class="card-tag" id="cardTag"></div>
      <div class="card-headline" id="cardHeadline"></div>
      <div class="card-score" id="cardScore"></div>
      <div class="card-body" id="cardBody"></div>
      <a class="card-cta" id="cardCta" href="mailto:keshavanand100@gmail.com?subject=Free Design Audit">Claim your free audit</a>
      <button class="card-secondary" id="cardBack" onclick="window.location.href='index.html'">Back to portfolio</button>
    </div>
  </div>

  <script>
  const canvas = document.getElementById('g');
  const ctx = canvas.getContext('2d');

  /* ── WORLD ── */
  const W = 4800, H = 700;
  const GROUND = 560;

  /* ── CONSTANTS ── */
  const GRAVITY    = 0.52;
  const JUMP_FORCE = -11;
  const SPEED      = 3.2;
  const FRICTION   = 0.80;

  /* ── COLORS ── */
  const C = {
    sky1: '#74B9FF', sky2: '#A29BFE',
    ground: '#2A2535',
    p1: ['#4CAF50','#FF9800','#E040FB','#FF5252','#26C6DA'],
    coin: '#FFD700',
    artifact: ['#00BCD4','#FF4081','#FF9800','#69F0AE'],
    blob:   ['#4CAF50','#FF9800','#CE93D8'],
    spike:  '#FF5252',
    bolt:   '#FFD700',
    shield: '#26C6DA',
    proj:   '#FFD700',
    heart:  '#FF5252',
    hudBg:  'rgba(237,234,227,0.90)',
    fg:     '#2A2535',
    muted:  '#7A7490',
    accent: '#9B2D7A',
  };

  /* ── STATE ── */
  let cam = 0;           // camera X
  let lives = 3;
  let score = 0;
  let coins = 0;
  let state = 'PLAY';   // PLAY | DYING | END
  let deathTimer = 0;
  let checkpointX = 40;
  let frame = 0;

  /* ── AUDIO ── */
  let audioCtx = null;
  function getAudio() {
    if (!audioCtx) audioCtx = new (window.AudioContext || window.webkitAudioContext)();
    return audioCtx;
  }
  function beep(freq, dur, type='square', vol=0.18, slide=0) {
    try {
      const ac = getAudio();
      const o = ac.createOscillator();
      const g = ac.createGain();
      o.connect(g); g.connect(ac.destination);
      o.type = type;
      o.frequency.setValueAtTime(freq, ac.currentTime);
      if (slide) o.frequency.linearRampToValueAtTime(slide, ac.currentTime + dur);
      g.gain.setValueAtTime(vol, ac.currentTime);
      g.gain.exponentialRampToValueAtTime(0.001, ac.currentTime + dur);
      o.start(); o.stop(ac.currentTime + dur);
    } catch(e) {}
  }
  function sfxJump()     { beep(200, 0.08, 'sine', 0.15, 420); }
  function sfxDie()      { beep(400, 0.4,  'square', 0.2, 60); }
  function sfxCoin()     {
    [523,659,784].forEach((f,i) => setTimeout(() => beep(f, 0.07, 'square', 0.12), i*55));
  }
  function sfxArtifact() {
    [523,659,784,1047].forEach((f,i) => setTimeout(() => beep(f, 0.09, 'sine', 0.15), i*60));
  }
  function sfxPowerup()  { beep(300, 0.25, 'sawtooth', 0.18, 900); }
  function sfxThrow()    { beep(800, 0.08, 'sawtooth', 0.14, 200); }
  function sfxHit()      { beep(150, 0.06, 'square', 0.15); }
  function sfxWin()      {
    [523,659,784,1047].forEach((f,i) => setTimeout(() => beep(f, 0.18, 'square', 0.18), i*120));
  }
  function sfxLose()     {
    [400,300,200,100].forEach((f,i) => setTimeout(() => beep(f, 0.15, 'square', 0.15), i*130));
  }

  /* ── RESIZE ── */
  let vw, vh, scale, offX, offY;
  function resize() {
    const dpr = window.devicePixelRatio || 1;
    vw = window.innerWidth; vh = window.innerHeight;
    const wa = W / H, va = vw / vh;
    if (va > wa) { scale = vh / H; offX = (vw - W * scale) / 2; offY = 0; }
    else          { scale = vw / W; offX = 0; offY = (vh - H * scale) / 2; }
    canvas.width  = vw * dpr; canvas.height = vh * dpr;
    canvas.style.width = vw + 'px'; canvas.style.height = vh + 'px';
    ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
  }
  window.addEventListener('resize', resize); resize();

  /* ── HELPERS ── */
  function sx(wx) { return Math.floor((wx - cam) * scale + offX); }
  function sy(wy) { return Math.floor(wy * scale + offY); }
  function sw(w)  { return Math.ceil(w * scale); }
  function sh(h)  { return Math.ceil(h * scale); }
  function hit(a, b) {
    return a.x < b.x+b.w && a.x+a.w > b.x && a.y < b.y+b.h && a.y+a.h > b.y;
  }

  /* ── GAME LOOP SHELL ── */
  function update() { frame++; }
  function render() {
    ctx.fillStyle = '#1a1a2e';
    ctx.fillRect(0, 0, canvas.width, canvas.height);
  }
  function loop() { update(); render(); requestAnimationFrame(loop); }
  requestAnimationFrame(loop);
  </script>
</body>
</html>
```

- [ ] **Step 2: Open browser at http://localhost:3000/game.html**

Verify: dark canvas, no errors in console, fonts loading.

- [ ] **Step 3: Commit**

```bash
cd /Users/keshavanand/Desktop/Portfolio
git add game.html
git commit -m "feat(game): scaffolding — canvas, loop, audio, end card shell"
```

---

### Task 2: Sky + Platform Rendering + Camera

**Files:**
- Modify: `game.html` — replace update/render, add platforms array, add camera logic

- [ ] **Step 1: Add platforms array and camera after the helpers block (before game loop shell)**

```javascript
/* ── PLATFORMS ── */
// { x, y, w, h, col, moving, dx, minX, maxX }
const platforms = [
  // ── Zone 1: Ground intro (x 0–800) ──
  { x:0,    y:560, w:300, h:20, col:C.p1[0] },
  { x:340,  y:560, w:200, h:20, col:C.p1[0] },
  { x:580,  y:560, w:260, h:20, col:C.p1[0] },
  { x:400,  y:480, w:120, h:16, col:C.p1[0] },
  { x:560,  y:420, w:100, h:16, col:C.p1[0] },

  // ── Zone 2: Multi-tier climb (x 800–2000) ──
  { x:840,  y:560, w:200, h:20, col:C.p1[1] },
  { x:1080, y:500, w:140, h:16, col:C.p1[1] },
  { x:1260, y:430, w:140, h:16, col:C.p1[1] },
  { x:1440, y:360, w:140, h:16, col:C.p1[1] },
  { x:1620, y:430, w:120, h:16, col:C.p1[1] },
  { x:1780, y:500, w:200, h:20, col:C.p1[1] },
  { x:1980, y:560, w:100, h:20, col:C.p1[1] },

  // ── Zone 3: Moving platforms over pit (x 2000–3200) ──
  { x:2100, y:560, w:120, h:20, col:C.p1[2] },
  { x:2300, y:480, w:100, h:16, col:C.p1[2], moving:true, dx:1.2, minX:2300, maxX:2480 },
  { x:2550, y:420, w:100, h:16, col:C.p1[2], moving:true, dx:-1.0, minX:2480, maxX:2650 },
  { x:2750, y:480, w:100, h:16, col:C.p1[2], moving:true, dx:1.4, minX:2700, maxX:2900 },
  { x:2960, y:430, w:120, h:16, col:C.p1[2] },
  { x:3100, y:560, w:150, h:20, col:C.p1[2] },

  // ── Zone 4: Dense zone (x 3200–4000) ──
  { x:3260, y:560, w:180, h:20, col:C.p1[3] },
  { x:3480, y:500, w:120, h:16, col:C.p1[3] },
  { x:3640, y:440, w:120, h:16, col:C.p1[3] },
  { x:3800, y:500, w:120, h:16, col:C.p1[3] },
  { x:3960, y:560, w:100, h:20, col:C.p1[3] },

  // ── Zone 5: Final sprint (x 4000–4800) ──
  { x:4060, y:560, w:160, h:20, col:C.p1[4] },
  { x:4260, y:500, w:100, h:16, col:C.p1[4] },
  { x:4400, y:440, w:100, h:16, col:C.p1[4] },
  { x:4560, y:500, w:120, h:16, col:C.p1[4] },
  { x:4700, y:560, w:100, h:20, col:C.p1[4] },
];
```

- [ ] **Step 2: Replace update() and render() with working camera + sky + platforms**

```javascript
/* ── PLAYER (temporary spawn for camera test) ── */
const player = { x:40, y:500, w:14, h:26, vx:0, vy:0, onGround:false, facing:1, wt:0, wf:0 };

function update() {
  frame++;
  // Camera: keep player at ~35% from left
  const targetCam = player.x - vw / scale * 0.35;
  cam = Math.max(0, Math.min(targetCam, W - vw / scale));

  // Moving platforms
  platforms.forEach(p => {
    if (!p.moving) return;
    p.x += p.dx;
    if (p.x <= p.minX || p.x + p.w >= p.maxX) p.dx *= -1;
  });
}

function drawSky() {
  const grad = ctx.createLinearGradient(0, offY, 0, offY + H * scale);
  grad.addColorStop(0, '#74B9FF');
  grad.addColorStop(1, '#A29BFE');
  ctx.fillStyle = grad;
  ctx.fillRect(offX, offY, W * scale, H * scale);
}

function drawPlatforms() {
  platforms.forEach(p => {
    ctx.fillStyle = p.col;
    ctx.fillRect(sx(p.x), sy(p.y), sw(p.w), sh(p.h));
    // Shadow gradient under platform
    const grad = ctx.createLinearGradient(0, sy(p.y), 0, sy(p.y) + sh(p.h));
    grad.addColorStop(0, 'rgba(0,0,0,0.18)');
    grad.addColorStop(1, 'rgba(0,0,0,0.0)');
    ctx.fillStyle = grad;
    ctx.fillRect(sx(p.x), sy(p.y), sw(p.w), sh(p.h));
  });
}

function render() {
  ctx.fillStyle = '#1a1a2e';
  ctx.fillRect(0, 0, canvas.width, canvas.height);
  drawSky();
  drawPlatforms();
}
```

- [ ] **Step 3: Verify in browser**

Sky gradient visible, platforms in correct colors across zones, camera math compiles. Open DevTools → no errors.

- [ ] **Step 4: Commit**

```bash
git add game.html
git commit -m "feat(game): sky gradient, platform layout, camera offset"
```

---

### Task 3: Player Physics + Input + Collision

**Files:**
- Modify: `game.html` — replace temporary player block with full physics

- [ ] **Step 1: Replace temporary player block with full player object + input**

After the `const platforms = [...]` block, add:

```javascript
/* ── INPUT ── */
const keys = {};
window.addEventListener('keydown', e => {
  keys[e.code] = true;
  if (['Space','ArrowUp','ArrowDown','ArrowLeft','ArrowRight'].includes(e.code)) e.preventDefault();
});
window.addEventListener('keyup', e => { keys[e.code] = false; });

/* ── PLAYER ── */
const SPAWN = { x:40, y:500 };
const player = { x:SPAWN.x, y:SPAWN.y, w:14, h:26, vx:0, vy:0, onGround:false, facing:1, wt:0, wf:0 };

function spawnPlayer() {
  player.x = checkpointX; player.y = checkpointX > 2000 ? 300 : 500;
  player.vx = 0; player.vy = 0; player.onGround = false;
}

function updatePlayer() {
  if (state !== 'PLAY') return;
  let mx = 0;
  if (keys['ArrowLeft']  || keys['KeyA']) mx = -1;
  if (keys['ArrowRight'] || keys['KeyD']) mx =  1;
  player.vx += mx * SPEED * 0.35;
  player.vx *= FRICTION;
  if (mx) player.facing = mx;

  if (Math.abs(player.vx) > 0.5 && player.onGround) { player.wt++; player.wf = player.wt; }
  else { player.wt = 0; player.wf = 0; }

  if ((keys['ArrowUp'] || keys['KeyW'] || keys['Space']) && player.onGround) {
    player.vy = JUMP_FORCE; player.onGround = false; sfxJump();
  }

  player.vy += GRAVITY;
  player.x += player.vx;
  player.onGround = false;

  // Horizontal collision
  platforms.forEach(p => {
    if (!hit(player, p)) return;
    player.x = player.vx > 0 ? p.x - player.w : p.x + p.w;
    player.vx = 0;
  });

  player.y += player.vy;

  // Vertical collision
  platforms.forEach(p => {
    if (!hit(player, p)) return;
    if (player.vy > 0) { player.y = p.y - player.h; player.vy = 0; player.onGround = true; }
    else               { player.y = p.y + p.h; player.vy = 0; }
  });

  // Clamp world X
  player.x = Math.max(0, Math.min(player.x, W - player.w));

  // Fall into pit
  if (player.y > H + 40) die();
}
```

- [ ] **Step 2: Add drawPlayer() after drawPlatforms()**

```javascript
function drawPlayer() {
  if (state === 'DYING' && Math.floor(deathTimer / 4) % 2 === 0) return;
  const cx = sx(player.x + player.w / 2);
  const top = sy(player.y);
  const s = scale;
  const f = player.facing;
  const walk = player.onGround && Math.abs(player.vx) > 0.5;

  ctx.save(); ctx.translate(cx, top);
  ctx.strokeStyle = C.accent; ctx.fillStyle = C.accent;
  ctx.lineWidth = Math.max(1, Math.ceil(2.2 * s)); ctx.lineCap = 'square';

  // Head
  const hr = Math.ceil(5 * s);
  ctx.fillRect(-hr, Math.ceil(2*s), hr*2, hr*2);
  ctx.fillStyle = '#fff';
  ctx.fillRect(f * Math.ceil(2*s), Math.ceil(2*s), Math.ceil(2*s), Math.ceil(2*s));

  // Body
  const bt = Math.ceil(9*s), bb = Math.ceil(18*s);
  ctx.strokeStyle = C.accent;
  ctx.beginPath(); ctx.moveTo(0, bt); ctx.lineTo(0, bb); ctx.stroke();

  // Arms
  const ay = bt + Math.ceil(3*s);
  const sw2 = walk ? Math.sin(player.wf * 0.4) * Math.ceil(4*s) : 0;
  ctx.beginPath(); ctx.moveTo(0, ay); ctx.lineTo(f * Math.ceil(6*s), ay + Math.ceil(5*s) + sw2); ctx.stroke();
  ctx.beginPath(); ctx.moveTo(0, ay); ctx.lineTo(-f * Math.ceil(5*s), ay + Math.ceil(5*s) - sw2); ctx.stroke();

  // Legs
  if (walk) {
    const ls = Math.sin(player.wf * 0.4) * Math.ceil(5*s);
    ctx.beginPath(); ctx.moveTo(0, bb); ctx.lineTo(ls, bb + Math.ceil(8*s)); ctx.stroke();
    ctx.beginPath(); ctx.moveTo(0, bb); ctx.lineTo(-ls, bb + Math.ceil(8*s)); ctx.stroke();
  } else if (!player.onGround) {
    ctx.beginPath(); ctx.moveTo(0, bb); ctx.lineTo(-Math.ceil(3*s), bb + Math.ceil(6*s)); ctx.stroke();
    ctx.beginPath(); ctx.moveTo(0, bb); ctx.lineTo(Math.ceil(3*s), bb + Math.ceil(6*s)); ctx.stroke();
  } else {
    ctx.beginPath(); ctx.moveTo(0, bb); ctx.lineTo(-Math.ceil(3*s), bb + Math.ceil(8*s)); ctx.stroke();
    ctx.beginPath(); ctx.moveTo(0, bb); ctx.lineTo(Math.ceil(3*s), bb + Math.ceil(8*s)); ctx.stroke();
  }
  ctx.restore();
}
```

- [ ] **Step 3: Wire updatePlayer() into update() and drawPlayer() into render()**

In `update()`: add `updatePlayer();` after `cam = ...` line.
In `render()`: add `drawPlayer();` after `drawPlatforms();`.

- [ ] **Step 4: Verify in browser**

Player spawns, walks left/right, jumps, lands on platforms, falls into gaps, can't walk through platforms. Facing direction flips. Walk animation plays.

- [ ] **Step 5: Commit**

```bash
git add game.html
git commit -m "feat(game): player physics, input, platform collision"
```

---

### Task 4: Enemies + Spikes + Death

**Files:**
- Modify: `game.html` — add enemies array, spikes array, die() function

- [ ] **Step 1: Add enemies and spikes arrays after platforms array**

```javascript
/* ── ENEMIES ── */
// { x, y, w, h, minX, maxX, speed, dir, type, platY }
const enemies = [
  // Zone 1
  { x:420, w:18, h:18, minX:360, maxX:540, speed:1.0, dir:1, type:0 },
  // Zone 2
  { x:900, w:14, h:14, minX:840, maxX:1020, speed:1.4, dir:1, type:1 },
  { x:1320, w:14, h:14, minX:1260, maxX:1380, speed:1.2, dir:-1, type:1 },
  { x:1660, w:20, h:20, minX:1620, maxX:1780, speed:0.9, dir:1, type:2 },
  // Zone 3
  { x:2960, w:14, h:14, minX:2960, maxX:3080, speed:1.3, dir:1, type:0 },
  // Zone 4
  { x:3320, w:18, h:18, minX:3260, maxX:3440, speed:1.5, dir:1, type:1 },
  { x:3520, w:14, h:14, minX:3480, maxX:3600, speed:1.3, dir:-1, type:2 },
  { x:3850, w:20, h:20, minX:3800, maxX:3960, speed:1.0, dir:1, type:0 },
  // Zone 5
  { x:4120, w:14, h:14, minX:4060, maxX:4220, speed:1.6, dir:1, type:1 },
  { x:4310, w:18, h:18, minX:4260, maxX:4400, speed:1.4, dir:-1, type:2 },
  { x:4610, w:14, h:14, minX:4560, maxX:4690, speed:1.5, dir:1, type:1 },
];

// Snap enemies to platform surfaces
enemies.forEach(e => {
  const plat = platforms.find(p => e.x >= p.x && e.x <= p.x + p.w);
  e.y = plat ? plat.y - e.h : GROUND - e.h;
  e.baseY = e.y;
});

/* ── SPIKES ── */
// { x, y, w, h } — static hazards on platform surfaces
const spikes = [
  // Zone 2 ceiling passages
  { x:1100, y:484, w:12, h:16 },
  { x:1120, y:484, w:12, h:16 },
  { x:1280, y:414, w:12, h:16 },
  // Zone 4
  { x:3500, y:484, w:12, h:16 },
  { x:3660, y:424, w:12, h:16 },
  { x:3680, y:424, w:12, h:16 },
  // Zone 5
  { x:4280, y:484, w:12, h:16 },
  { x:4420, y:424, w:12, h:16 },
  { x:4580, y:484, w:12, h:16 },
];
```

- [ ] **Step 2: Add die() and updateEnemies() functions**

After `spawnPlayer()`:

```javascript
function die() {
  if (state !== 'PLAY') return;
  sfxDie();
  lives--;
  if (lives <= 0) { endGame(false); return; }
  state = 'DYING'; deathTimer = 50;
}

function updateEnemies() {
  if (state !== 'PLAY') return;
  enemies.forEach(e => {
    e.x += e.speed * e.dir;
    if (e.x <= e.minX || e.x + e.w >= e.maxX) e.dir *= -1;
    // Snap to moving platform if on one
    const plat = platforms.find(p => p.moving && e.baseY === p.y - e.h &&
      e.x + e.w/2 >= p.x && e.x + e.w/2 <= p.x + p.w);
    if (plat) e.y = plat.y - e.h;
  });
}
```

- [ ] **Step 3: Add drawEnemies() and drawSpikes()**

```javascript
const ENEMY_COLS = [C.blob[0], C.blob[1], C.blob[2]];

function drawEnemies() {
  enemies.forEach(e => {
    const bob = Math.sin(frame * 0.08 + e.x) * 2 * scale;
    ctx.fillStyle = ENEMY_COLS[e.type];
    ctx.fillRect(sx(e.x), sy(e.y) + bob, sw(e.w), sh(e.h));
    // Pixel eye
    ctx.fillStyle = '#fff';
    const ew = Math.ceil(4 * scale), eh = Math.ceil(4 * scale);
    ctx.fillRect(sx(e.x + (e.dir > 0 ? e.w - 6 : 2)), sy(e.y + 4) + bob, ew, eh);
    ctx.fillStyle = '#000';
    ctx.fillRect(sx(e.x + (e.dir > 0 ? e.w - 4 : 3)), sy(e.y + 5) + bob, Math.ceil(2*scale), Math.ceil(2*scale));
  });
}

function drawSpikes() {
  ctx.fillStyle = C.spike;
  spikes.forEach(s => {
    // Triangle spike
    const bx = sx(s.x), by = sy(s.y), bw = sw(s.w), bh = sh(s.h);
    ctx.beginPath();
    ctx.moveTo(bx, by + bh);
    ctx.lineTo(bx + bw / 2, by);
    ctx.lineTo(bx + bw, by + bh);
    ctx.closePath();
    ctx.fill();
  });
}
```

- [ ] **Step 4: Add enemy + spike collision in updatePlayer(), and handle death timer in update()**

At end of `updatePlayer()` add:

```javascript
  // Enemy collision
  const shielded = powerup && powerup.type === 'shield';
  enemies.forEach(e => {
    if (!shielded && hit(player, { x:e.x+2, y:e.y+2, w:e.w-4, h:e.h-4 })) die();
  });
  // Spike collision
  spikes.forEach(s => {
    if (!shielded && hit(player, s)) die();
  });
```

In `update()` add after `updatePlayer()`:

```javascript
  updateEnemies();
  if (state === 'DYING') {
    deathTimer--;
    if (deathTimer <= 0) { spawnPlayer(); state = 'PLAY'; }
  }
```

In `render()` add after `drawPlatforms()` (before `drawPlayer()`):

```javascript
  drawSpikes();
  drawEnemies();
```

- [ ] **Step 5: Verify in browser**

Enemies patrol their ranges, bob gently. Spikes render as triangles. Walking into enemy triggers death blink then respawn. Falling into pit dies. Three deaths trigger endGame (next task).

- [ ] **Step 6: Commit**

```bash
git add game.html
git commit -m "feat(game): enemies, spikes, death + respawn system"
```

---

### Task 5: Coins, Artifacts, Powerups + Score

**Files:**
- Modify: `game.html` — add collectibles arrays + pickup logic

- [ ] **Step 1: Add coins, artifacts, powerup spawns after spikes array**

```javascript
/* ── COLLECTIBLES ── */
let powerup = null; // active powerup { type, timer }

const coinDefs = [
  // Zone 1
  60,120,180,240,420,460,500,600,640,680,
  // Zone 2
  900,950,1090,1140,1270,1320,1450,1500,1630,1680,1790,
  // Zone 3
  2110,2160,2310,2570,2760,2970,3110,
  // Zone 4
  3270,3320,3490,3650,3810,3870,3970,
  // Zone 5
  4070,4120,4270,4410,4570,4640,4720,
].map(x => ({ x, y:0, w:10, h:10, alive:true }));

// Snap coins onto platforms
coinDefs.forEach(c => {
  const plat = platforms.find(p => c.x >= p.x && c.x <= p.x + p.w);
  c.y = plat ? plat.y - c.h - 12 : GROUND - c.h - 12;
});

const artifactDefs = [
  { x:560,  label:'F', col:C.artifact[0] },
  { x:1440, label:'P', col:C.artifact[1] },
  { x:2960, label:'G', col:C.artifact[2] },
  { x:4400, label:'C', col:C.artifact[3] },
].map(a => {
  const plat = platforms.find(p => a.x >= p.x && a.x <= p.x + p.w);
  return { ...a, y: plat ? plat.y - 22 : GROUND - 22, w:16, h:16, alive:true };
});

const powerupDefs = [
  { x:1780, type:'bolt' },
  { x:3260, type:'shield' },
  { x:4260, type:'bolt' },
].map(p => {
  const plat = platforms.find(pl => p.x >= pl.x && p.x <= pl.x + pl.w);
  return { ...p, y: plat ? plat.y - 20 : GROUND - 20, w:16, h:16, alive:true };
});
```

- [ ] **Step 2: Add updateCollectibles() function**

```javascript
function updateCollectibles() {
  if (state !== 'PLAY') return;

  // Coins
  coinDefs.forEach(c => {
    if (!c.alive) return;
    if (hit(player, c)) { c.alive = false; score++; coins++; sfxCoin(); }
  });

  // Artifacts
  artifactDefs.forEach(a => {
    if (!a.alive) return;
    if (hit(player, a)) { a.alive = false; score += 5; sfxArtifact(); }
  });

  // Powerup spawns
  powerupDefs.forEach(p => {
    if (!p.alive) return;
    if (hit(player, p)) {
      p.alive = false;
      powerup = { type: p.type, timer: p.type === 'bolt' ? 8 : 5, maxTimer: p.type === 'bolt' ? 8 : 5 };
      score += 2; sfxPowerup();
    }
  });

  // Active powerup countdown
  if (powerup) {
    powerup.timer -= 1/60;
    if (powerup.timer <= 0) powerup = null;
  }
}
```

- [ ] **Step 3: Add projectiles array + throw logic + updateProjectiles()**

After `powerupDefs`:

```javascript
const projectiles = [];

window.addEventListener('keydown', e => {
  if (e.code === 'KeyX' && powerup && powerup.type === 'bolt') {
    sfxThrow();
    projectiles.push({ x: player.x + player.w/2, y: player.y + 8, w:8, h:8, vx: player.facing * 7, alive:true });
  }
});

function updateProjectiles() {
  for (let i = projectiles.length - 1; i >= 0; i--) {
    const p = projectiles[i];
    if (!p.alive) { projectiles.splice(i, 1); continue; }
    p.x += p.vx;
    if (p.x < cam - 20 || p.x > cam + vw/scale + 20) { p.alive = false; continue; }
    // Hit enemy
    enemies.forEach(e => {
      if (hit(p, e)) { p.alive = false; sfxHit(); score += 3; }
    });
    // Hit spike
    spikes.forEach(s => { if (hit(p, s)) p.alive = false; });
  }
}
```

- [ ] **Step 4: Wire into update() and render()**

In `update()` add:
```javascript
  updateCollectibles();
  updateProjectiles();
```

In `render()` add after drawEnemies():
```javascript
  drawCollectibles();
  drawProjectiles();
```

- [ ] **Step 5: Add draw functions**

```javascript
function drawCollectibles() {
  const bob = Math.sin(frame * 0.07) * 3 * scale;

  coinDefs.forEach(c => {
    if (!c.alive) return;
    ctx.fillStyle = C.coin;
    const r = Math.ceil(5 * scale);
    ctx.beginPath();
    ctx.arc(sx(c.x + 5), sy(c.y + 5) + bob, r, 0, Math.PI * 2);
    ctx.fill();
    ctx.fillStyle = 'rgba(255,255,255,0.4)';
    ctx.beginPath();
    ctx.arc(sx(c.x + 3), sy(c.y + 3) + bob, Math.ceil(2*scale), 0, Math.PI * 2);
    ctx.fill();
  });

  artifactDefs.forEach(a => {
    if (!a.alive) return;
    ctx.fillStyle = a.col;
    ctx.fillRect(sx(a.x), sy(a.y) + bob, sw(a.w), sh(a.h));
    ctx.fillStyle = '#fff';
    ctx.font = `bold ${Math.ceil(10*scale)}px monospace`;
    ctx.textAlign = 'center'; ctx.textBaseline = 'middle';
    ctx.fillText(a.label, sx(a.x + 8), sy(a.y + 8) + bob);
  });

  powerupDefs.forEach(p => {
    if (!p.alive) return;
    ctx.fillStyle = p.type === 'bolt' ? C.bolt : C.shield;
    ctx.fillRect(sx(p.x), sy(p.y) + bob, sw(p.w), sh(p.h));
    ctx.fillStyle = '#fff';
    ctx.font = `bold ${Math.ceil(10*scale)}px monospace`;
    ctx.textAlign = 'center'; ctx.textBaseline = 'middle';
    ctx.fillText(p.type === 'bolt' ? '⚡' : '★', sx(p.x + 8), sy(p.y + 8) + bob);
  });
}

function drawProjectiles() {
  ctx.fillStyle = C.proj;
  projectiles.forEach(p => {
    if (!p.alive) return;
    ctx.fillRect(sx(p.x), sy(p.y), sw(p.w), sh(p.h));
  });
}
```

- [ ] **Step 6: Verify in browser**

Coins scattered on platforms, artifacts visible with letter labels, powerup icons visible. Walking over coin plays arpeggio and increments score. Picking up bolt powerup, pressing X fires a projectile. Shield powerup prevents enemy damage.

- [ ] **Step 7: Commit**

```bash
git add game.html
git commit -m "feat(game): coins, artifacts, powerups, projectiles, score"
```

---

### Task 6: HUD + Checkpoint + Win Condition + Flag

**Files:**
- Modify: `game.html` — HUD, checkpoints, flag, win trigger

- [ ] **Step 1: Add flag and checkpoints after powerupDefs**

```javascript
/* ── FLAG ── */
const FLAG = { x: 4740, y: 520 };

/* ── CHECKPOINTS ── */
const CHECKPOINTS = [0, 840, 2100, 3260, 4060];
```

- [ ] **Step 2: Add checkpoint detection in updatePlayer() and flag collision**

At end of `updatePlayer()` (after spike collision):

```javascript
  // Checkpoints
  CHECKPOINTS.forEach(cx => {
    if (player.x > cx && cx > checkpointX) checkpointX = cx;
  });

  // Flag — win
  if (player.x + player.w > FLAG.x && player.x < FLAG.x + 20 &&
      player.y + player.h > FLAG.y && player.y < FLAG.y + 60) {
    endGame(true);
  }
```

- [ ] **Step 3: Add drawHUD() and drawFlag()**

```javascript
function drawHUD() {
  const m = Math.ceil(14 * scale);
  const bw = Math.ceil(130 * scale), bh = Math.ceil(28 * scale);
  const bx = offX + m, by = offY + m;

  ctx.fillStyle = C.hudBg;
  ctx.beginPath();
  ctx.roundRect(bx, by, bw, bh, Math.ceil(6*scale));
  ctx.fill();

  // Hearts
  for (let i = 0; i < 3; i++) {
    ctx.fillStyle = i < lives ? C.heart : 'rgba(42,37,53,0.15)';
    const hx = bx + Math.ceil((10 + i * 22) * scale);
    const hy = by + bh / 2;
    const r = Math.ceil(6 * scale);
    drawHeart(hx, hy, r);
  }

  // Score
  ctx.fillStyle = C.fg;
  ctx.font = `600 ${Math.ceil(10*scale)}px Inter, sans-serif`;
  ctx.textAlign = 'left'; ctx.textBaseline = 'middle';
  ctx.fillText(`${score} pts  ·  ${coins} coins`, bx + Math.ceil(78*scale), by + bh/2);

  // Powerup timer bar
  if (powerup) {
    const bx2 = offX + m, by2 = by + bh + Math.ceil(6*scale);
    const maxW = Math.ceil(130*scale);
    const fillW = Math.ceil(maxW * (powerup.timer / powerup.maxTimer));
    ctx.fillStyle = 'rgba(237,234,227,0.7)';
    ctx.fillRect(bx2, by2, maxW, Math.ceil(5*scale));
    ctx.fillStyle = powerup.type === 'bolt' ? C.bolt : C.shield;
    ctx.fillRect(bx2, by2, fillW, Math.ceil(5*scale));
  }
}

function drawHeart(cx, cy, r) {
  ctx.beginPath();
  ctx.moveTo(cx, cy + r*0.4);
  ctx.bezierCurveTo(cx, cy-r*0.4, cx-r, cy-r*0.6, cx-r, cy);
  ctx.bezierCurveTo(cx-r, cy+r*0.5, cx, cy+r*0.8, cx, cy+r);
  ctx.bezierCurveTo(cx, cy+r*0.8, cx+r, cy+r*0.5, cx+r, cy);
  ctx.bezierCurveTo(cx+r, cy-r*0.6, cx, cy-r*0.4, cx, cy+r*0.4);
  ctx.closePath(); ctx.fill();
}

function drawFlag() {
  const fx = sx(FLAG.x), fy = sy(FLAG.y);
  const s = scale;
  const wave = Math.sin(frame * 0.06) * Math.ceil(3*s);

  ctx.strokeStyle = '#fff'; ctx.lineWidth = Math.ceil(2.5*s); ctx.lineCap = 'square';
  ctx.beginPath(); ctx.moveTo(fx, fy); ctx.lineTo(fx, fy - Math.ceil(50*s)); ctx.stroke();

  ctx.fillStyle = C.accent;
  ctx.beginPath();
  ctx.moveTo(fx, fy - Math.ceil(50*s));
  ctx.lineTo(fx + Math.ceil(32*s), fy - Math.ceil(38*s) + wave);
  ctx.lineTo(fx, fy - Math.ceil(26*s));
  ctx.closePath(); ctx.fill();
}
```

- [ ] **Step 4: Wire drawHUD() and drawFlag() into render()**

In `render()` add:
```javascript
  drawFlag();
  drawHUD();
```

- [ ] **Step 5: Verify in browser**

HUD shows 3 hearts + score + coins. Flag visible at end of level. Walking into flag or falling to 0 lives should try to call endGame (next task). Powerup timer bar visible when powerup active.

- [ ] **Step 6: Commit**

```bash
git add game.html
git commit -m "feat(game): HUD, flag, checkpoints, win detection"
```

---

### Task 7: End Card + Score Tiers + endGame()

**Files:**
- Modify: `game.html` — implement endGame(), populate end card HTML

- [ ] **Step 1: Add endGame() function after die()**

```javascript
function endGame(won) {
  state = 'END';
  won ? sfxWin() : sfxLose();

  const tiers = [
    { min:0,  tag:'EXPLORER',  headline:'You explored.', body:'Not bad for a first run. That curiosity? I can work with it. Claim a free design audit and let\'s see what you\'re building.' },
    { min:21, tag:'INSTINCT',  headline:'Good instincts.', body:'You moved like you knew what you were doing. That reads in product too. Let\'s do a free audit and sharpen what you\'ve got.' },
    { min:51, tag:'SHARP EYE', headline:'Sharp eye.', body:'You found things most people miss. That\'s a rare skill. Book a free design audit and let\'s put it to use.' },
    { min:81, tag:'RARE',      headline:'You see what most miss.', body:'That was precise. Intentional. The kind of thing that separates good products from great ones. Let\'s talk.' },
  ];

  const tier = [...tiers].reverse().find(t => score >= t.min) || tiers[0];

  const winTag  = won ? tier.tag : 'NOT DONE YET';
  const winLine = won ? tier.headline : 'So close.';
  const winBody = won
    ? tier.body
    : `${score} points, ${coins} coins. That\'s a solid run. Come back and claim what\'s yours — or grab a free audit anyway.`;

  document.getElementById('cardTag').textContent      = winTag;
  document.getElementById('cardHeadline').textContent = winLine;
  document.getElementById('cardScore').textContent    = `${score} pts · ${coins} coins`;
  document.getElementById('cardBody').textContent     = winBody;

  const card = document.getElementById('endCard');
  card.classList.add('show');

  // Retry button for lose state
  const back = document.getElementById('cardBack');
  if (!won) {
    back.textContent = 'Try again';
    back.onclick = () => location.reload();
  }
}
```

- [ ] **Step 2: Verify end card in browser**

Play to win (walk to flag) — end card appears with correct tier copy, score, coins. Test lose by dying 3 times. Retry reloads, Back goes to index.html. Card is centered, design matches system (DM Serif italic headline, Inter body, accent pill CTA).

- [ ] **Step 3: Commit**

```bash
git add game.html
git commit -m "feat(game): end card, score tiers, audit CTA"
```

---

### Task 8: Polish — Pixel Jitter, Shield Flicker, Zone Backgrounds

**Files:**
- Modify: `game.html` — visual polish pass

- [ ] **Step 1: Add zone-tinted sky bands to drawSky()**

Replace `drawSky()` with:

```javascript
function drawSky() {
  const grad = ctx.createLinearGradient(0, offY, 0, offY + H * scale);
  grad.addColorStop(0, '#74B9FF');
  grad.addColorStop(1, '#A29BFE');
  ctx.fillStyle = grad;
  ctx.fillRect(offX, offY, W * scale, H * scale);

  // Zone color bands (subtle tint on horizon)
  const zones = [
    { x:0,    w:840,  col:'rgba(76,175,80,0.08)' },
    { x:840,  w:1160, col:'rgba(255,152,0,0.08)' },
    { x:2000, w:1200, col:'rgba(224,64,251,0.08)' },
    { x:3200, w:800,  col:'rgba(255,82,82,0.08)' },
    { x:4000, w:800,  col:'rgba(38,198,218,0.08)' },
  ];
  zones.forEach(z => {
    ctx.fillStyle = z.col;
    ctx.fillRect(sx(z.x), offY, sw(z.w), H * scale);
  });
}
```

- [ ] **Step 2: Add shield flicker to drawPlayer()**

At top of `drawPlayer()`, after the blink check:

```javascript
  const shielded = powerup && powerup.type === 'shield';
  if (shielded && Math.floor(frame / 3) % 2 === 0) {
    ctx.save();
    ctx.globalAlpha = 0.5;
  }
```

At bottom of `drawPlayer()`, before `ctx.restore()`:

```javascript
  if (shielded) ctx.globalAlpha = 1;
```

- [ ] **Step 3: Verify in browser**

Zone tints visible as subtle color washes behind platforms. Shield flickers player transparency when active. Pixel rendering still crisp (no blur).

- [ ] **Step 4: Commit**

```bash
git add game.html
git commit -m "feat(game): zone tints, shield flicker, visual polish"
```

---

### Task 9: Bug Testing + Game Testing

**Files:**
- Modify: `game.html` — fix any bugs found during testing

- [ ] **Step 1: Edge case checklist — run each and fix any issues**

Test each scenario in the browser:

1. **Clip through platform:** Run at full speed into a platform edge. Player should stop, not pass through.
2. **Moving platform edge:** Stand on moving platform at its turning point. Should not clip through floor.
3. **Projectile off-screen:** Fire projectile, let it travel to world edge. No console error, cleans up.
4. **Die on moving platform:** Get hit while on a moving platform. Respawns at checkpoint cleanly.
5. **All 3 lives lost:** Die 3 times. End card appears, retry works.
6. **Win with 0 coins:** Walk straight to flag ignoring all coins. End card shows 0 pts tier correctly.
7. **Win with max coins:** Collect everything. End card shows highest tier.
8. **Powerup expires mid-throw:** Fire X as timer hits 0. No crash.
9. **Resize mid-game:** Drag browser window. Canvas redraws correctly.
10. **Zone 3 wide pit:** Jump across all moving platforms. Should always be completable — if not, adjust platform positions.

- [ ] **Step 2: Game tester pass — dopamine audit**

Play through as a first-time player. Evaluate:

- Are coins spaced so there's always one visible ahead? (Draws the player forward)
- Do artifacts feel hidden enough to be a reward when found?
- Is Zone 3 (moving platforms) frustrating or exciting? Adjust `dx` values if it feels unfair.
- Does the end card feel warm and personal? Read all 4 tier messages aloud.
- Is the HUD readable without being distracting?

Make any adjustments identified. Common fixes:
- Move coins that are in mid-air with no platform under them: check `coinDefs` snap logic
- Enemy that clips into wall: adjust `minX/maxX` range
- Powerup that spawns inside a platform: adjust `y` manually if needed

- [ ] **Step 3: Final commit**

```bash
git add game.html docs/
git commit -m "feat(game): bug fixes + game testing pass"
```

---

### Task 10: Mobile Touch Controls

**Files:**
- Modify: `game.html` — on-screen touch buttons + touch event wiring

- [ ] **Step 1: Add mobile control CSS inside `<style>`**

```css
/* Mobile touch controls */
#mControls {
  display: none;
  position: fixed;
  bottom: 0; left: 0; right: 0;
  padding: 16px 20px max(16px, env(safe-area-inset-bottom));
  z-index: 50;
  pointer-events: none;
  justify-content: space-between;
  align-items: flex-end;
}
@media (pointer: coarse) { #mControls { display: flex; } }

.m-btn {
  pointer-events: all;
  width: 56px; height: 56px;
  border-radius: 50%;
  background: rgba(237,234,227,0.18);
  border: 2px solid rgba(237,234,227,0.35);
  color: #EDEAE3;
  font-size: 20px;
  display: flex; align-items: center; justify-content: center;
  user-select: none; -webkit-user-select: none;
  cursor: pointer;
  transition: background 0.1s;
}
.m-btn:active, .m-btn.pressed { background: rgba(155,45,122,0.55); }
.m-dpad { display: flex; gap: 8px; }
.m-right { display: flex; gap: 8px; }
```

- [ ] **Step 2: Add touch control HTML after the end card div**

```html
<!-- Mobile controls -->
<div id="mControls">
  <div class="m-dpad">
    <button class="m-btn" id="btnLeft">◀</button>
    <button class="m-btn" id="btnRight">▶</button>
  </div>
  <div class="m-right">
    <button class="m-btn" id="btnThrow">⚡</button>
    <button class="m-btn" id="btnJump">▲</button>
  </div>
</div>
```

- [ ] **Step 3: Add touch wiring in the script, after the keyboard event listeners**

```javascript
/* ── TOUCH CONTROLS ── */
function bindBtn(id, code) {
  const el = document.getElementById(id);
  if (!el) return;
  const press   = e => { e.preventDefault(); keys[code] = true;  el.classList.add('pressed'); };
  const release = e => { e.preventDefault(); keys[code] = false; el.classList.remove('pressed'); };
  el.addEventListener('touchstart',  press,   { passive: false });
  el.addEventListener('touchend',    release, { passive: false });
  el.addEventListener('touchcancel', release, { passive: false });
  el.addEventListener('mousedown',  press);
  el.addEventListener('mouseup',    release);
  el.addEventListener('mouseleave', release);
}

bindBtn('btnLeft',  'ArrowLeft');
bindBtn('btnRight', 'ArrowRight');
bindBtn('btnJump',  'Space');

// Throw button fires projectile directly (same as KeyX)
const btnThrow = document.getElementById('btnThrow');
if (btnThrow) {
  const throwFn = e => {
    e.preventDefault();
    if (powerup && powerup.type === 'bolt') {
      sfxThrow();
      projectiles.push({ x: player.x + player.w/2, y: player.y + 8, w:8, h:8, vx: player.facing * 7, alive:true });
    }
  };
  btnThrow.addEventListener('touchstart', throwFn, { passive: false });
  btnThrow.addEventListener('mousedown',  throwFn);
}
```

- [ ] **Step 4: Verify on mobile (or Chrome DevTools → toggle device toolbar)**

Controls appear at bottom on touch devices. Left/right move player. Jump button works. Throw button fires projectile when bolt powerup active. Buttons don't block the HUD or end card. Safe area padding respected on iPhone notch.

- [ ] **Step 5: Commit**

```bash
git add game.html
git commit -m "feat(game): mobile touch controls"
```

---

### Task 11: End Card CTA — Screenshot Score + Email

**Files:**
- Modify: `game.html` — update end card copy and CTA to prompt screenshot + email

- [ ] **Step 1: Update the card CTA HTML in the end card**

Replace:
```html
<a class="card-cta" id="cardCta" href="mailto:keshavanand100@gmail.com?subject=Free Design Audit">Claim your free audit</a>
<button class="card-secondary" id="cardBack" onclick="window.location.href='index.html'">Back to portfolio</button>
```

With:
```html
<p class="card-screenshot-hint" id="cardHint"></p>
<a class="card-cta" id="cardCta" href="mailto:keshavanand100@gmail.com?subject=Free Design Audit">Claim your free audit →</a>
<button class="card-secondary" id="cardBack" onclick="window.location.href='index.html'">Back to portfolio</button>
```

Add this CSS:
```css
.card-screenshot-hint {
  font-size: 12px;
  color: #9B2D7A;
  font-weight: 500;
  margin-bottom: 16px;
  letter-spacing: 0.01em;
}
```

- [ ] **Step 2: Update endGame() to set hint text and mailto with score pre-filled**

In `endGame()`, after setting `cardBody`, add:

```javascript
  document.getElementById('cardHint').textContent =
    'Screenshot your score and send it with your audit request.';

  const subject = encodeURIComponent(`Free Design Audit — ${score} pts`);
  const body = encodeURIComponent(
    `Hi Keshav,\n\nI scored ${score} points and collected ${coins} coins.\n\n[Attach your screenshot here]\n\nHere's what I'm building: `
  );
  document.getElementById('cardCta').href =
    `mailto:keshavanand100@gmail.com?subject=${subject}&body=${body}`;
```

- [ ] **Step 3: Verify end card**

Win or lose — end card shows pink hint text "Screenshot your score and send it with your audit request." CTA mailto pre-fills subject with score and body with a template. Score and coins are correct.

- [ ] **Step 4: Final commit**

```bash
git add game.html Assets/ docs/
git commit -m "feat(game): full rebuild — platformer with coins, powerups, mobile controls, score-tiered audit end card"
```
