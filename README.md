# Futebol de Botão

A top-down button football (futebol de botão) table you play in the browser. One file, no build step: open `index.html`.

## Rules implemented

- Pull back from one of your buttons and release to flick it.
- Possession: you keep playing while your team was the last to touch the ball, up to 5 flicks per possession (shown as dots under the team name). Miss it, let it touch an opponent last, or use your 5 touches, and the turn passes.
- Falta: hitting an opposing outfield button before your button reaches the ball is a foul. The fouled button gets a free kick with the ball placed on the line to the opposing goal, and the fouling team is pushed back.
- Declared shots: press "Declare shot on goal" before shooting. The defending side's goalkeeper is pre-placed facing the ball; it can be dragged anywhere in its zone and turned with a round handle (or the mouse wheel) before pressing Ready. A goal from an undeclared shot is void and gives a goal kick. Own goals always count.
- Out of play: the felt runs 64 units past the touchlines as run-off, so buttons can chase the ball there. A ball fully over a touchline is a throw-in (lateral) to the team that did not touch it last; over a goal line it is a corner (escanteio) if the defenders touched it last, otherwise a goal kick (tiro de meta). For a throw-in or corner the kicking team's nearest button steps up behind the ball in the run-off.
- Goalkeeper kick: on a goal kick (ball on the edge of the small area), or whenever the ball rests in your own small area on your turn, you may flick the ball itself.
- 8 per side (7 buttons and a goalkeeper). At kick-off the striker has a partner beside the ball on the halfway line and a midfielder behind for a back pass. First to 3 goals. Modes: vs CPU, or 2 players on one device.

## Code map (`index.html`)

Everything is in one `<script>`.

- Between `//CORE-START` and `//CORE-END`: pure game core with no DOM access. Constants and geometry (logical table is 1180x736, pitch 1040x580 inside the lines), `makeState`, `step` (fixed 1/240 s physics step), `kpush` (disc vs. tilted box), `simulate`, and `pickGen`/`pick` (the CPU: it simulates a spread of flicks and keeps the best outcome; the page runs the generator a few milliseconds per frame so the table never freezes while the CPU thinks).
- After that, one IIFE: match flow (`launch`, `endFlick`, `onGoal`, `onOut`, `freeKick`, `setPiece` for throw-ins, corners and goal kicks, keeper placement), canvas drawing (static table pre-rendered in `buildBg`; the view rotates 90 degrees on portrait screens via `setT`; `resize` makes the table fill the screen and keeps the floating scoreboard and status bars off it only when that costs little space, otherwise they float over the frame and the status line fades out), pointer input, and the HUD.

The core block can be extracted and run in Node for headless tests (for example CPU vs CPU matches).
