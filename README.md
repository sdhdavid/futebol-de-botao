# Futebol de Botão

A top-down button football (futebol de botão) table you play in the browser. One file, no build step: open `index.html`.

## Rules implemented

- Pull back from one of your buttons and release to flick it.
- Possession: you keep playing while your team was the last to touch the ball, up to 3 flicks per possession (shown as dots under the team name). Miss it, let it touch an opponent last, or use your 3 touches, and the turn passes.
- Falta: hitting an opposing outfield button before your button reaches the ball is a foul. The fouled button gets a free kick with the ball placed on the line to the opposing goal, and the fouling team is pushed back.
- Declared shots: press "Declare shot on goal" before shooting. The defending side then drags and tilts its goalkeeper. A goal from an undeclared shot is void and gives a goal kick. Own goals always count.
- Goalkeeper kick: when the ball rests in your own small area on your turn, you may flick the ball itself.
- There is no out of bounds: the felt continues past the lines as run-off up to the wooden frame.
- First to 3 goals. Modes: vs CPU, or 2 players on one device.

## Code map (`index.html`)

Everything is in one `<script>`.

- Between `//CORE-START` and `//CORE-END`: pure game core with no DOM access. Constants and geometry (logical table is 1100x640), `makeState`, `step` (fixed 1/240 s physics step), `kpush` (disc vs. tilted box), `simulate`, and `pickGen`/`pick` (the CPU: it simulates a spread of flicks and keeps the best outcome; the page runs the generator a few milliseconds per frame so the table never freezes while the CPU thinks).
- After that, one IIFE: match flow (`launch`, `endFlick`, `onGoal`, `freeKick`, `goalKick`, keeper placement), canvas drawing (static table pre-rendered in `buildBg`; the view rotates 90 degrees on portrait screens via `setT`), pointer input, and the HUD.

The core block can be extracted and run in Node for headless tests (for example CPU vs CPU matches).
