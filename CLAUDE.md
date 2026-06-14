# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This repo contains two standalone browser games — no build step, no package manager, no dependencies. Each game is a single self-contained HTML file that runs by opening it directly in a browser.

| File | Game |
|------|------|
| `retro-shooter.html` | NEON ASSAULT — retro top-down shooter |
| `tic-tac-toe/index.html` | Tic Tac Toe — 2-player or vs. unbeatable CPU |

## Running the Games

Open the files directly in a browser:

```powershell
# Retro shooter
Start-Process "retro-shooter.html"

# Tic Tac Toe
Start-Process "tic-tac-toe\index.html"
```

There are no tests, no linting tools, and no build commands.

## Architecture

### retro-shooter.html

Single `<script>` block, organized top-to-bottom:

- **Sound** — Web Audio API synth (no audio files); procedural blips and noise bursts
- **Input** — `keys` object (keyboard) + `mouse` object (position + button state)
- **Entities** — `player`, `bullets[]`, `enemies[]`, `particles[]` as plain objects
- **Enemy types** — `ENEMY_TYPES` lookup table (`chaser`/`fast`/`tank`) with shape, speed, HP, score, damage
- **Wave/level system** — `buildWaveQueue()` generates a queue of enemy type keys; `toSpawn[]` is drained by a `spawnTimer` each frame
- **Game loop** — `update(dt)` → `render()` via `requestAnimationFrame`; `dt` is clamped at 50ms to handle tab-switch gaps
- **State machine** — `gameState` ∈ `{MENU, PLAYING, LEVEL_CLEAR, GAME_OVER}`
- **High score** — persisted to `localStorage` under key `"neonAssaultHigh"`

Difficulty scaling happens in two places: `buildWaveQueue()` (enemy count and type mix per level) and `spawnEnemy()` (HP and speed multipliers).

### tic-tac-toe/index.html

Single `<script>` block wrapped in an IIFE:

- **Game logic** — `board[9]` (flat array), `place()`, `postMove()`, `evaluate()` (checks `WINS` combos)
- **AI** — `bestMove()` + `minimax()` implement perfect play; the CPU is unbeatable
- **Mode** — `'2p'` (two human players) or `'cpu'` (human X vs. computer O)
- **Themes** — 4 CSS custom-property themes (`aurora`/`sunset`/`mint`/`mono`) set via `data-theme` on `<body>`; persisted to `localStorage` under `"ttt-theme"`
- **Scoreboard** — tracked in memory (`scores` object); reset on mode change or explicit reset
