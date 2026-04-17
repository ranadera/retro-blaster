# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the Games

No build step — open directly in a browser:
```
open shooter.html
open tictactoe.html
```

All logic, styles, and markup live in a single self-contained HTML file per game. There are no dependencies, bundlers, or package managers.

## Repository Structure

- `shooter.html` — Retro Blaster: 6-level top-down shooter
- `tictactoe.html` — Tic Tac Toe with 2-player and vs-CPU (minimax) modes

## shooter.html Architecture

Single `<script>` block, 800×600 `<canvas>`, no external assets. All sprites drawn programmatically with Canvas 2D API.

**Key globals:**
- `S` — game state enum (`MENU`, `PLAY`, `LEVEL_CLEAR`, `GAME_OVER`, `WIN`)
- `LEVELS[]` — wave definitions: `{ total, types[], interval }`
- `EDEFS` — enemy type definitions: `{ grunt, runner, tank }` each with `col, r, spd, hp, dmg, pts`
- `player`, `bullets[]`, `enemies[]`, `particles[]` — live entity arrays reset on `reset()`

**Game loop flow:** `requestAnimationFrame(loop)` → `update(now)` → `updatePlay(dt, now)` → `draw(now)`

**Update pipeline (per frame):**
1. Player movement + angle from mouse
2. `fireCd` countdown; `shoot()` on mouse down
3. `trySpawn(now)` — respects `LEVELS[level].interval`
4. Bullet movement + bounds culling
5. Enemy movement (toward player with wobble), bullet collision, player contact
6. Particle decay
7. Level-clear check: `spawned >= total && enemies.length === 0`

**Draw pipeline:** background grid → particles → bullets (with trail + glow) → enemies → `drawPlayer()` → `drawHUD()`

**Adding a new enemy type:** add an entry to `EDEFS`, add its key to whichever `LEVELS[n].types` arrays you want it in.

**Adding a new level:** append an entry to `LEVELS[]`. No other changes needed.

**Tuning difficulty:** adjust `spd`/`hp`/`dmg` in `EDEFS`, or `interval`/`total` in `LEVELS`.

## Git / GitHub Workflow

Every meaningful change should be committed and pushed:
```
git add -p                        # stage relevant hunks
git commit -m "short description"
git push
```

Remote: `https://github.com/ranadera/retro-blaster` (branch: `main`)
