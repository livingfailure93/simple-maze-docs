# Simple Maze

**Mazes for Unity that are generated, solved exactly and rated, on square, hex, triangle and circular grids.**

Simple Maze builds a maze from your settings, then proves it can be solved and works out its **par**, the fewest moves it
takes. It can add keys and doors, switches and gates, gems, traps, one-way sides and portals, stack up to 8 floors
joined by stairs, put up to 4 players on the board, and make the players roll like on ice instead of stepping. Every
maze is rated 1 to 5 stars and can be saved as a ScriptableObject, exported as a PNG or SVG picture, or played in the
included 3D demo.

*Documentation:* `Window > Living Failure > Simple Maze > Documentation` (offline HTML, in `Documentation/`).

---

## The four grids

| Grid | Cells | Size |
|---|---|---|
| **Square** | 4 sides. The only grid with weave crossings, where a passage tunnels under a corridor. | 2 to 64 a side |
| **Hex** | Pointy-top hexagons with 6 sides. | 2 to 64 a side |
| **Triangle** | Alternating up and down triangles with 3 sides. | 2 to 64 a side |
| **Circular** | A centre cell inside rings that split as they grow, so the cells stay roughly square from the centre to the rim. | 2 to 40 rings |

Any grid can be stacked into 2 to 8 floors joined by stairs, and cut to a shape: a circle, ring, heart, star, diamond,
cross, a word in the built-in font, or your own picture.

---

## What makes it "verified"

- **The par is real.** The solver searches every position the players can reach, counting every key, door, gate and
  portal, so a 40-move maze really takes 40 moves.
- **Gimmicks are part of the proof.** They are solved together with the maze, and by default every door and every
  switch's first gate stands in the way, so its key or switch is really needed.
- **Races are fair.** Race starts are picked so every player has exactly the same par, gimmicks included. A board where
  that's impossible is reported, and verified generation moves on to another one.
- **Getting stuck is detected.** Every maze reports whether a player can reach a position it can't finish from, and
  `NoSoftLocks` only accepts mazes where that never happens.
- **Misses are reported.** When a target star count, a coverage, a gimmick or a fair race can't be met, the maze's
  notes say what happened and what you got instead.

---

## Quick start

1. Open **Window > Living Failure > Simple Maze > Maze Generator**.
2. Press **Presets**, pick one (for example *Classic/Circular 10 rings*) and press **Generate**. Mazes are built on a
   background thread, with a progress bar and a Cancel button.
3. Read the analysis pane: the par, the number of best solutions, the stars, a picture of the maze, and a slider that
   steps through the solution.
4. **Save** the selected maze as an asset, or the whole set as a pack, and **Export** printable PNG or SVG pictures.
5. Open `Scenes/SimpleMazeDemo.unity` and press Play.

### From code

```csharp
using SimpleMaze;

var settings = new MazeSettings
{
    Grid = TopologySpec.Circle(10),
    Endpoints = Endpoints.OutsideIn,   // start on the rim, goal in the centre
    Keys = 2,
    TargetDifficulty = 3,              // keep trying until it rates 3 stars
};
MazeData maze = MazeGenerator.GenerateVerified(settings);
Debug.Log($"Par {maze.Par} steps, {maze.Difficulty} stars, solvable: {maze.Solvable}");
```

Run your own game on it with the same rules the solver used, so your game always agrees with the par:

```csharp
MazePlay play = MazeRules.NewGame(maze);
if (MazeRules.TryStep(maze, play, 0, nextCell, out string reason))
{
    if (MazeRules.IsSolved(maze, play)) Debug.Log("Solved!");
}
else Debug.Log(reason);   // a wall, a locked door, a shut gate...
```

`MazeGenerator` also has `Generate`, `GenerateBatch` and an `Async` version of each, which run on a background thread
and can be cancelled. `MazeRules.Hint` gives the next move of a best way on from any position.

---

## The demo

One scene and one component, `MazeDemo`, which builds everything in code: no prefabs, models, textures or audio files.
It opens on a welcome screen, then five modes:

| Mode | What you play |
|---|---|
| **Classic** | Plain mazes on every grid. |
| **Dungeon** | Keys and doors, switches and gates, gems, portals, traps and one-way sides. |
| **Roll** | You slide like on ice until a wall stops you (Square and Hex). |
| **Tower** | Floors joined by spiral stairs. |
| **Race** | Two players take turns racing to the same goal. |

Each mode has **Endless** mazes built to your choice of grid and difficulty, and a **Level Pack**. The demo ships with 50
levels. Play has undo, restart, hints, an auto-solver, a moves against par score, sounds and effects made in code, and
a camera you can turn, tilt and zoom.

Keys: arrows or WASD to move, Q E Z C for diagonals, Space for stairs, U undo, R restart, H hint, F solve, Tab next
player, `[` and `]` or right-drag to turn the view, the mouse wheel to zoom, V to reset the view, M to mute, Esc for the
menu.

---

## Layout

```
SimpleMaze/
├─ Runtime/
│  ├─ Api/               MazeGenerator, MazePresets (and Auto), MazeAudit
│  ├─ Data/              MazeSettings, MazeData
│  ├─ Topology/          the four grids, floors, passages, shapes
│  ├─ Generation/        builder, carving algorithms, holes, weave, gimmick placer
│  ├─ Solving/           solver, difficulty
│  ├─ Rules/             MazeRules and MazePlay: the play rules
│  ├─ Presentation/      MazeExport (PNG and SVG), MazeSketch
│  ├─ ScriptableObjects/ maze asset, pack, IPuzzleProvider
│  └─ Demo/              3D view, game, HUD, hub, sounds, effects, HDRP look
├─ Editor/               Maze Generator window, analysis pane, inspectors, documentation menu
├─ Documentation/        offline HTML docs
├─ Generated/            the demo's five level packs
└─ Scenes/               the demo scene
```

Three assemblies: `SimpleMaze` (the core, with no package dependencies), `SimpleMaze.Demo` (Input System and Unity UI,
plus HDRP when it is installed) and `SimpleMaze.Editor`.

---

## Requirements

- Unity **6000.6** or newer.
- **URP** (recommended), **HDRP** and the **Built-in** pipeline. The demo uses the active pipeline's default lit shader,
  and on HDRP it adds a small volume so it looks the same.
- Demo only: the Input System package (set Active Input Handling to *Input System Package* or *Both*) and Unity UI.

## Limits, honestly

- Rolling needs straight lines, so Roll works on one-floor Square and Hex boards. Other boards use Step, with a note.
- Weave crossings are Square only.
- Up to 4 players, 6 keys, 4 switches with up to 3 gates each, 8 gems, 24 one-way sides and 4 portal pairs. When fewer
  fit on a board, you get fewer, with a note.
- The solver has a state budget (500,000 positions by default, up to 5,000,000). Several rolling players and many keys
  and gems need the most, and a maze that runs past the budget is reported as not verified rather than guessed.
- Plain mazes generate in milliseconds even at 64 by 64. A big board full of gimmicks takes about a second, and four
  rolling players a few seconds, on a desktop PC.
- On Square boards the built-in font's diagonal strokes only touch at their corners, so a letter like M can lose its
  diagonals in a word shape.

## Support

**livingfailuregames@gmail.com**. Please include your Unity version and, for anything about a particular maze, its seed
and settings, since a seed rebuilds the maze exactly.

Licensed under the Unity Asset Store EULA. © Living Failure.
