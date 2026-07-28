# Soft Bubble Pop — Design Spec

**Date:** 2026-07-28  
**Goal:** A zero-install browser mini-game for casual stress relief: pop pastel bubbles with light score/combo feedback and no failure state.

## Decisions (locked)

| Topic | Choice |
|-------|--------|
| Interaction | Click / tap to pop bubbles |
| Pressure | Score + combo; no timer, no Game Over |
| Visual | Soft pastel + slight glass sheen |
| Delivery | Single `index.html` (embedded CSS + JS) |
| Audio | Soft pop SFX via Web Audio API; mute toggle |

## Experience

- Full-viewport soft gradient background (blush pink → mint → lavender).
- Semi-transparent pastel bubbles drift upward at gentle speeds.
- Click/tap pops a bubble: soft particle burst + short pop sound.
- Score increases on pop; rapid pops raise a combo multiplier (`x2`, `x3`…).
- Combo decays after ~800ms without a pop; multiplier resets to `x1`. Reset is calm (no punishment UI).
- Occasional larger “calm bubble” (rarer, bigger): more particles, higher base points.
- HUD (top-right): score, best combo this session, mute button.
- Optional tiny title top-left: “啵啵” / “Soft Bubbles” — brand-soft, not loud marketing.
- Mobile-friendly: `touch-action: none`, full-screen canvas, works on phone and desktop.

## Non-goals

- No accounts, leaderboards, ads, or levels.
- No Game Over, lives, or countdown.
- No build tooling (no npm/Vite).
- No external asset CDN dependency (fonts via system stack is OK; prefer one distinctive webfont from Google Fonts only if it loads gracefully with fallback).

## Technical shape

- One file: `index.html` at repo root (or `game/index.html` if keeping root clean — **use repo root `index.html`** for easiest open).
- Canvas 2D for bubbles + particles; HUD as HTML overlay for crisp text.
- Game loop via `requestAnimationFrame`.
- Resize canvas to window; maintain devicePixelRatio for sharpness.
- Persist nothing except optional `localStorage` high score (nice-to-have; include session best combo only is fine — **persist high score** lightly).

## Scoring rules

- Base points = `round(radius * 0.4)` (larger = slightly more).
- Calm bubble: base × 3.
- Final gain = `base * comboMultiplier`.
- Combo: each pop within 800ms of previous increments multiplier by 1, capped at `x10`.
- Display floating “+N” near pop for immediate feedback.

## Audio

- Synthesize short noise-burst / sine blip with Web Audio (no mp3 files).
- Mute button sets a flag; respect muted state before scheduling sounds.
- First sound may require a user gesture (browser policy) — first click both pops and unlocks audio context.

## Success criteria

1. Double-click / open `index.html` in a browser and play immediately.
2. Popping feels soft and satisfying within 10 seconds.
3. Score and combo update correctly; game never ends on its own.
4. Mute works; layout usable on a phone-width viewport.
