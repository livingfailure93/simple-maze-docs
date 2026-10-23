# Changelog: Simple Maze

All notable changes to this package.

---

## [1.0.0]

First release.

### Boards

- **Four grids:** Square, Hex (pointy-top), Triangle (alternating up and down) and Circular (a centre cell inside rings
  that split as they grow). Square, Hex and Triangle go from 2 to 64 a side, and Circular from 2 to 40 rings, with a
  choice of 3 to 12 cells in the first ring.
- **Floors:** any grid stacked 2 to 8 times, joined by 1 to 4 spiral stairs between each pair of floors. The first stair
  goes where the floor's maze is farthest from the stairs you arrived by, so every floor gets crossed.
- **Shapes:** cut any board to a circle, ring, heart, star, diamond or cross, a word in the built-in 5 by 7 font, or a
  custom mask. A mask can be typed as rows or made from any picture, using its dark, light or opaque areas. When a shape
  falls apart into pieces, the pieces are joined by the shortest bridges or only the largest is kept.
- **Carving:** six algorithms (Recursive Backtracker, Prim, Kruskal, Wilson, Hunt and Kill, and Growing Tree with a
  branchiness setting), plus braid (dead-end removal), extra loops, up to 12 open rooms, holes that never split the
  board, and weave crossings on Square boards.
- **Start and goal:** farthest apart, centre out, outside in, or fixed cells, with an optional entrance and exit cut in
  the outer wall for printed mazes.

### Players and movement

- **Step:** one cell per move on every grid, including through tunnels and up and down stairs.
- **Roll:** every player slides in a straight line until a wall, a locked door, a shut gate, a one-way side, the border
  or another player stops it (Square and Hex, one floor). Players picking things up share them.
- **Up to 4 players,** racing to one goal or each heading to its own. Race starts are picked so every player's par is
  exactly the same. With gimmicks, the other players' starts are chosen after the gimmicks are placed, using each cell's
  real par.

### Gimmicks

- Up to 6 **keys and doors**, 4 **switches** with up to 3 **gates** each (decoys included), 8 **gems**, **traps**, 24
  **one-way sides** and 4 **portal pairs**.
- Placed one type at a time and proven after each, so a type that can't fit is dropped with a note and never drags the
  others down.
- By default every door and every switch's first gate is truly in the way. **Shortcut doors** allows ones a player can
  walk around.
- **No soft-locks** only accepts mazes where no player can reach a position it can't finish from.

### Solving and rating

- An exact solver over every reachable position (where the players are, who is home, the keys held, the gems taken and
  the switches flipped). It gives the **proven par**, the number of best solutions with a completeness flag, whether the
  best solution is **unique**, a best solution, and whether anyone **can get stuck**.
- A state budget (500,000 positions by default, up to 5,000,000). A maze that runs past it is reported as not verified,
  never guessed.
- A **1 to 5 star rating** from the par, the real decisions on the way (forks whose wrong way runs 3 or more cells deep)
  and the gimmicks, calibrated on a sweep of 576 Step and 288 Roll boards so each star covers about a fifth of everyday
  mazes.

### Generation

- `MazeGenerator.Generate`, `GenerateVerified` (keeps trying seeds until a maze meets the target stars, coverage, no
  soft-locks and a fair race) and `GenerateBatch`, each with a background-thread `Async` version that can be cancelled.
- Deterministic: the same settings and seed always give the same maze, and every maze stores the seed that made it.
- Every adjustment is written to the maze's notes. Nothing is quietly downgraded.
- 19 presets, and **Auto**, which fixes settings that fight each other and says what it changed.

### Playing

- `MazeRules`: the play rules your game uses, the same ones the solver counted with. It covers stepping, rolling, gates,
  hints from any position (`Hint`), the win check and a data validator.

### Editor

- The **Maze Generator** window: every setting with tooltips, presets, Auto, a seed you can keep, batches of up to 20
  generated on a background thread with progress and Cancel, and Save as a maze or a pack.
- The **analysis pane**, in the window and in both inspectors: the verdict, a picture of the maze with a legend and a
  tab per floor, hover to see what is in a cell, a slider that steps through the solution, the move list with Copy
  solution, the facts, and the generation report.
- **Export** as PNG or SVG: a printable sheet, an answer key, or the full colour view. A pack exports every maze with
  its answer key in one go.
- **Inspectors** with Verify (solve again and restamp the verdict) and Re-roll (a new maze from the same settings),
  including re-rolling every maze in a pack that missed its request.

### Demo

- One scene and one component, `MazeDemo`, which builds everything in code with no prefabs, models, textures or audio
  files.
- A welcome screen, then five modes: Classic, Dungeon, Roll, Tower and Race. Each has Endless mazes built to your choice
  of grid and difficulty, and a level pack. The demo ships with 50 levels.
- Undo, restart, hints, an auto-solver, moves against par with a rating, turn-based races, stairs, and a camera you can
  turn, tilt and zoom that follows the player and the floors.
- Sounds and effects made in code, the same in every render pipeline.
- Works in URP, HDRP and the Built-in pipeline. On HDRP a small volume gives it the same look.

### Documentation

- Offline HTML documentation (Home, Quick Start, Generating, API Reference, Demos, Troubleshooting and About, plus a
  single-page version), opened from **Window > Living Failure > Simple Maze > Documentation**.
