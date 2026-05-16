# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Structure

Single-file project: `outbreak-sim.html` — all HTML, CSS, and JavaScript in one file. No build system, no dependencies beyond Google Fonts loaded at runtime.

Open in any browser to run; no server required.

## Architecture

The simulation implements a **SEIRD + Vaccinated** agent-based model. Each agent carries `{state, x, y, vx, vy, daysInState}` and drifts with gentle Brownian motion inside the canvas bounds.

### State machine

```
S → E → I → R
               ↘ D
V (immune from day 0, never transitions)
```

Transitions are stochastic each day:
- S→E: probability = β × (nI / N)
- E→I: probability = σ (= 1 / latent_period)
- I→D: probability = μ × γ
- I→R: probability = γ (= 1 / infectious_period)

### Key data structures

- `agents[]` — array of all individuals; mutated in place each step
- `history[]` — array of `{S,E,I,R,D,V}` count snapshots, one per simulated day

### Key functions (top-down order in the script block)

| Function | Role |
|---|---|
| `wireSlider` | Binds a range input to its display label |
| `init()` | Allocates agents, seeds V and I states, resets history |
| `snapshot()` | Counts agents by state → one history entry |
| `stepModel()` | Advances one day: applies drift, runs state transitions, pushes snapshot |
| `drawPopCanvas()` | Renders agent dots on `#pop-canvas` |
| `drawLineChart()` | Renders SEIRD time-course curves on `#line-canvas` |
| `updateUI()` | Updates sidebar counts and ticker bar |
| `isOver()` | Returns true when E + I = 0 |
| `loop(ts)` | `requestAnimationFrame` callback; accumulates elapsed time to fire `stepModel` at the configured days/second rate |
| `start()` / `stop()` | Start and stop the animation loop |

### CSS variables (theming)

All colors are defined as CSS custom properties on `:root`: `--s-color`, `--e-color`, `--i-color`, `--r-color`, `--d-color`, `--v-color`, plus layout colors `--bg`, `--panel`, `--border`, `--accent`, `--dim`.

The `COLORS` object in the JS mirrors these six state colors for canvas rendering.

### Canvas sizing

Both canvases read `clientWidth`/`clientHeight` at draw time and set their `width`/`height` attributes accordingly, so they are responsive to window resize (debounced 100 ms, only while paused).
