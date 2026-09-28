# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A fully playable Tetris game implemented in vanilla JavaScript with HTML5 Canvas. No dependencies, no build process—just open and play.

- **Tech Stack**: HTML5, CSS3, Vanilla JavaScript (ES6+), Canvas 2D API
- **Size**: ~300 lines of game logic across 3 files
- **Target**: Browser-based (any modern browser with Canvas support)

## Getting Started

### Running the Game

Choose one option:

**Option 1: Direct file open** (limited, may have CORS issues with some features)
```bash
# Windows
start index.html

# macOS
open index.html

# Linux
xdg-open index.html
```

**Option 2: Local HTTP server** (recommended)
```bash
# Python 3
python3 -m http.server 8000

# Node.js (npx)
npx serve .

# PHP
php -S localhost:8000
```
Then visit `http://localhost:8000` in your browser.

## Architecture

The game consists of three files that work together:

### 1. `index.html` — DOM Structure
- **Canvas elements**: `#board` (300×600px for gameplay) and `#next-canvas` (120×120px for preview)
- **UI Panel**: Score, lines cleared, current level, next-piece preview, and control legend
- **Overlay**: Displays PAUSE and GAME OVER states with restart button
- **No external dependencies** — pure semantic HTML

### 2. `style.css` — Visual Styling
- **Dark theme**: `#0f0f17` background, arcade-inspired aesthetic
- **Layout**: Flexbox for main container and panel sections
- **Canvas styling**: Subtle borders, shadows, and grid background
- **Overlays**: Semi-transparent darkening with `backdrop-filter: blur(4px)`
- **Responsive**: Works on desktop; designed for 300px + 160px side panel

### 3. `game.js` — All Game Logic

#### Core Data Structures
- **board**: 2D array (ROWS × COLS) where each cell holds `0` (empty) or a color index (1–7)
- **current**: Active falling piece `{ type, shape, x, y }`
- **next**: Preview piece of same structure
- **shape**: 4×4 matrix representing piece geometry, with values being color indices

#### Key Functions

**Initialization & State**
- `init()` — Reset board, scores, spawn first piece, start game loop
- `createBoard()` — Return empty ROWS×COLS matrix

**Piece Management**
- `randomPiece()` — Generate random piece (1–7) and center it horizontally at top
- `spawn()` — Move `next` → `current`, generate new `next`, check for instant collision (Game Over)
- `rotateCW(shape)` — Rotate shape 90° clockwise using transpose + row reversal

**Physics & Collision**
- `collide(shape, ox, oy)` — Check if shape at (ox, oy) overlaps board edges or locked blocks
- `tryRotate()` — Attempt rotation with wall kicks: [0, -1, 1, -2, 2] pixel offsets

**Movement**
- Keydown listeners for arrows/space/P handle left/right/soft-drop/hard-drop/pause
- Hard drop: instant fall to lowest position, +2 points per cell
- Soft drop: manual 1-row drop, +1 point, checks collision after each row

**Piece Locking & Cleanup**
- `merge()` — Copy current piece shape to board
- `clearLines()` — Scan from bottom; remove full rows, add empty rows at top, update score/level
- `lockPiece()` — Call merge → clearLines → spawn
- Line scoring: `LINE_SCORES = [0, 100, 300, 500, 800]` × current level

**Rendering**
- `draw()` — Clear canvas, draw grid, board blocks, ghost piece, and current piece
- `drawBlock(context, x, y, colorIndex, size, alpha)` — Draw single cell with highlight
- `drawGrid()` — Subtle grid lines at 0.5px width
- `drawNext()` — Render next piece preview on separate canvas, centered in 4×4 grid
- `ghostY()` — Calculate where current piece would land; drawn at 0.2 alpha

**Game Loop**
- `loop(ts)` — Called via `requestAnimationFrame`
  - Accumulates elapsed time into `dropAccum`
  - When `dropAccum >= dropInterval`: attempt one-row drop or lock piece
  - Redraws canvas each frame

**Game States**
- `togglePause()` — Toggle pause overlay; resets `lastTime` on resume to prevent jump
- `endGame()` — Show Game Over overlay with final score, cancel animation loop

#### Constants (Tunable)
```javascript
COLS = 10              // Board width
ROWS = 20             // Board height
BLOCK = 30            // Pixel size per cell
COLORS = [null, ...]  // 7 hex colors for pieces I-L
LINE_SCORES = [...]   // Points for 1/2/3/4 lines
dropInterval          // Milliseconds between auto-drops (decreases with level)
```

**Level & Speed**: Level = `floor(lines / 10) + 1`; Speed = `max(100, 1000 - (level - 1) × 90)` ms

## Game Flow Diagram

```
init()
  ├─ createBoard() → empty ROWS×COLS matrix
  ├─ next ← randomPiece()
  ├─ spawn() → move next to current, generate new next
  └─ requestAnimationFrame(loop)
       ↓
  loop(timestamp) [called every frame]
    ├─ Accumulate elapsed time
    ├─ If time ≥ dropInterval
    │   ├─ Try move piece down 1 row
    │   └─ Else → lockPiece() → merge → clearLines → spawn
    ├─ draw() → render grid + board + ghost + current piece
    └─ Queue next frame
       ↓
  Keydown events (left/right/rotate/drop)
    └─ Update piece position/rotation, check collision
```

## Customization

Common tweaks in `game.js`:

| Parameter | Purpose | Default |
|-----------|---------|---------|
| `COLS` | Board width | 10 |
| `ROWS` | Board height | 20 |
| `BLOCK` | Pixel size per cell | 30 |
| `COLORS[1–7]` | Piece colors (hex) | 7 colors |
| `LINE_SCORES` | Points per line count | `[0,100,300,500,800]` |

**If you change `COLS`, `ROWS`, or `BLOCK`**: Update the `<canvas id="board">` dimensions in `index.html` to match:
```html
<canvas id="board" width="300" height="600"></canvas>
<!-- width = COLS × BLOCK, height = ROWS × BLOCK -->
```

## Controls

| Input | Action |
|-------|--------|
| `←` / `→` | Move left/right |
| `↑` or `X` | Rotate clockwise |
| `↓` | Soft drop (accelerated fall) |
| `Space` | Hard drop (instant fall) |
| `P` | Pause/Resume |

## Key Implementation Details

- **No wall clipping on spawn**: If a new piece collides at (x, y=0), game ends immediately. Pieces spawn already-centered.
- **Wall kicks**: Rotation tries offsets `[0, -1, 1, -2, 2]`; if any offset clears collision, rotation succeeds. Prevents awkward "stuck" feeling.
- **Ghost piece transparency**: Drawn at `globalAlpha = 0.2` for subtle preview; color index preserved.
- **Score accumulation**: Soft drop (+1/row), hard drop (+2/cell), line clears (scaled by level).
- **Pause preserves game state**: Cancels `requestAnimationFrame`, resets `lastTime` on resume to prevent time-jump.
- **Canvas rendering**: Direct pixel manipulation via fillRect; no transforms. Grid drawn separately for visual clarity.

## Testing & Debugging

Since there's no test suite, manual testing is recommended:
- Play through several games; verify pieces spawn, rotate, and lock correctly
- Test all line-clear scenarios (1, 2, 3, 4-line clears)
- Verify ghost piece shows correct landing position
- Check soft/hard drop scoring
- Pause and resume mid-game
- Inspect browser console for any errors

## Performance Considerations

- Canvas redraw every frame (~60 FPS) — lightweight, no issues on modern hardware
- No memory leaks from animation frames (properly cancelled on pause/game over)
- Board operations (clearLines, collide) are O(n) but with small constant factors (max 20×10 = 200 cells)
