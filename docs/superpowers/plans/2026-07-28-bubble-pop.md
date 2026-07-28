# Soft Bubble Pop Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Ship a single-file pastel bubble-pop game openable in any browser for stress relief.

**Architecture:** One `index.html` with embedded CSS and JS. Canvas 2D renders bubbles and particles; HTML overlay shows score/combo/mute. `requestAnimationFrame` game loop; Web Audio for pop blips.

**Tech Stack:** HTML5, CSS3, Canvas 2D, Web Audio API (no npm).

## Global Constraints

- Single file only: `index.html` at repo root.
- No Game Over, timer, or lives.
- Soft pastel visuals; Web Audio pop SFX with mute.
- Score + combo (cap x10, 800ms window); calm bubbles rarer and worth more.
- Persist high score in `localStorage` key `soft-bubble-high-score`.
- Mobile touch support; canvas respects `devicePixelRatio`.

---

### Task 1: Scaffold page shell and HUD

**Files:**
- Create: `index.html`

**Interfaces:**
- Produces: DOM ids `#game`, `#score`, `#combo`, `#best`, `#mute`, `#title`

- [ ] **Step 1: Create `index.html` shell**

Include:
- `<canvas id="game">`
- HUD: title “啵啵 Soft Bubbles”, score, combo, best (high score), mute button
- Soft gradient CSS on `body`; HUD top-right; title top-left
- Use font: `"Nunito", "Segoe UI", system-ui, sans-serif` with optional Google Fonts link for Nunito
- `html, body { margin:0; height:100%; overflow:hidden; }`
- Canvas `position:fixed; inset:0; width:100%; height:100%; touch-action:none;`

- [ ] **Step 2: Manual check**

Open `index.html` in browser. Expect: gradient background, HUD labels visible, empty canvas covering viewport.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: scaffold Soft Bubble Pop shell and HUD"
```

---

### Task 2: Canvas resize + bubble spawn/update/draw

**Files:**
- Modify: `index.html` (script section)

**Interfaces:**
- Produces: `resize()`, `spawnBubble()`, `Bubble { x,y,r,vx,vy,color,calm }`, `bubbles[]`, `tick(dt)`

- [ ] **Step 1: Implement resize and pastel palette**

```javascript
const COLORS = ["#F8C8DC", "#C8E7F5", "#D4C4F7", "#C9E4CA", "#FFE5B4", "#F5D0E8"];
function resize() {
  const dpr = Math.min(window.devicePixelRatio || 1, 2);
  const w = window.innerWidth, h = window.innerHeight;
  canvas.width = w * dpr; canvas.height = h * dpr;
  canvas.style.width = w + "px"; canvas.style.height = h + "px";
  ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
}
```

- [ ] **Step 2: Spawn & draw bubbles**

- Spawn interval ~0.4–0.9s random; keep ~12–28 on screen.
- Rise from below bottom; slight horizontal drift; wrap/remove when above top.
- Calm bubble: ~8% chance, radius 48–72 (normal 18–42).
- Draw: radial gradient fill, soft white highlight arc, slight stroke.

- [ ] **Step 3: Manual check**

Bubbles float up continuously; calm bubbles visibly larger; no console errors.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: add floating pastel bubbles on canvas"
```

---

### Task 3: Pop, particles, score, combo

**Files:**
- Modify: `index.html`

**Interfaces:**
- Produces: `particles[]`, `popBubble(b)`, `onPointer(e)`, scoring helpers

- [ ] **Step 1: Pointer hit-test**

On `pointerdown`, find topmost bubble whose center distance to pointer ≤ radius; call `popBubble`.

- [ ] **Step 2: Particles + score**

On pop: remove bubble; spawn 8–16 particles with pastel color; add points:
`base = Math.round(r * 0.4) * (calm ? 3 : 1); gain = base * combo`
Combo: if `now - lastPop < 800` then `combo = min(combo+1, 10)` else `combo = 1`; update `lastPop`.
Show floating `+gain` text fading upward.
Update `#score`, `#combo` (`xN`); update high score in `localStorage` and `#best`.

- [ ] **Step 3: Manual check**

Clicks pop correct bubble; score rises; combo climbs then resets after pause; particles appear.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: add pop feedback, score, and combo"
```

---

### Task 4: Web Audio pops + mute + polish

**Files:**
- Modify: `index.html`
- Modify: `README.md` (one-line how to open)

**Interfaces:**
- Produces: `ensureAudio()`, `playPop(calm)`, mute toggles `muted`

- [ ] **Step 1: Audio**

`AudioContext` on first gesture; short sine + exponential gain for normal; slightly lower pitch / longer for calm. Mute button toggles `muted` and button label 🔊/🔇.

- [ ] **Step 2: Polish**

- Keep bubble count comfortable; never end the game.
- Ensure touch works on mobile.
- Title + empty-state feel immediately playable (no start screen required — auto-running).

- [ ] **Step 3: Manual check**

Sound on pop; mute silences; high score survives refresh; phone-width OK.

- [ ] **Step 4: Commit**

```bash
git add index.html README.md
git commit -m "feat: add pop sounds, mute, and play instructions"
```

---

## Spec coverage

| Spec item | Task |
|-----------|------|
| Single index.html | 1–4 |
| Pastel bubbles rise | 2 |
| Pop + particles | 3 |
| Score + combo no fail | 3 |
| Calm bubbles | 2–3 |
| Web Audio + mute | 4 |
| High score persist | 3 |
| Mobile touch | 3–4 |
