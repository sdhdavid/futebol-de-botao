# Futebol de Botão

A top-down button football (futebol de botão) table you play in the browser. One file, no build step: open `index.html`.

## Rules implemented

- Pull back from one of your buttons and release to flick it.
- Possession: you keep playing while your team was the last to touch the ball, up to 5 flicks per possession (shown as dots under the team name). Miss it, let it touch an opponent last, or use your 5 touches, and the turn passes.
- Falta: hitting an opposing outfield button before your button reaches the ball is a foul. The fouled button gets a free kick with the ball placed on the line to the opposing goal, and the fouling team is pushed back.
- Declared shots: press "Declare shot" before shooting. A shot can only be declared while the ball is in the opponent's half (the button is hidden otherwise). The defending side's goalkeeper is pre-placed facing the ball; it can be dragged anywhere in its zone and turned with a round handle (or the mouse wheel) before pressing Ready. A goal from an undeclared shot is void and gives a goal kick. Own goals always count.
- Out of play: the felt runs 64 units past the touchlines as run-off, so buttons can chase the ball there. A ball fully over a touchline is a throw-in (lateral) to the team that did not touch it last; over a goal line it is a corner (escanteio) if the defenders touched it last, otherwise a goal kick (tiro de meta). For a throw-in or corner the kicking team's nearest button steps up behind the ball in the run-off. The button that takes a throw-in or corner cannot be flicked again until something else has touched the ball (football's double-touch rule).
- Goalkeeper kick: on a goal kick (ball on the edge of the small area), or whenever the ball rests in your own small area on your turn, you may flick the ball itself.
- 8 per side (7 buttons and a goalkeeper). At kick-off the striker has a partner beside the ball on the halfway line and a midfielder behind for a back pass. First to 3 goals. Modes: vs CPU, or 2 players on one device.

## Online play

Menu, then "Play online". One player creates a room and sends the invite link (or the 5-character code); the other opens it. The host plays Casa, the guest Visitante, and the guest's table is turned round so each player has their own goal nearest to them.

- The two browsers connect directly over WebRTC using [PeerJS](https://peerjs.com), loaded from a CDN only when online play is used. PeerJS's free public broker introduces the two players; no game data passes through it and there is no server of our own.
- Only inputs are sent: a flick, a shot declaration, a goalkeeper placement. Each side runs the flick in its own physics, then the side that flicked sends the resting position and the other adopts it, so the two tables cannot drift apart.
- Either player can reload the page and carry on: the guest rejoins from the invite link, the host reopens the same room (same browser tab), and the position is taken from whoever still has it.
- Limits: some networks (mostly corporate or mobile ones) block direct connections, and there is no relay server to fall back on. There is no protection against a modified client, no matchmaking and no turn clock.
- Testing: `?net=local` replaces WebRTC with a `BroadcastChannel` between two tabs of the same browser.

The version is shown at the bottom of the menu (`VERSION` and `BUILD` in the script); bump it with each change while the game is in development.

## Code map (`index.html`)

Everything is in one `<script>`.

- Between `//CORE-START` and `//CORE-END`: pure game core with no DOM access. Constants and geometry (logical table is 1180x736, pitch 1040x580 inside the lines), `makeState`, `step` (fixed 1/240 s physics step), `kpush` (disc vs. tilted box), `simulate`, and `pickGen`/`pick` (the CPU: for each of its buttons it lists lines that send the ball at the goal mouth or fan it out for passes and wing play, drops lines blocked by another button (`firstHit`), simulates each at four strengths, then looks one flick further for the six most promising: its own follow-up if it keeps the ball, the opponent's best reply if it loses it. At a throw-in or corner it uses the button placed over the ball. Resting positions are scored by `evalRest`: distance to goal, whether a button is lined up behind the ball for the next flick (`support`), open lines to either goal (`lane`), and how dangerous the ball is if the opponent gets it. Declared shots are re-simulated slightly off-angle so it prefers shots that survive an imperfect flick; the page runs the generator a few milliseconds per frame so the table never freezes while the CPU thinks).
- After that, one IIFE: match flow (`launch`, `endFlick`, `onGoal`, `onOut`, `freeKick`, `setPiece` for throw-ins, corners and goal kicks, keeper placement), canvas drawing (static table pre-rendered in `buildBg`; the view rotates 90 degrees on portrait screens via `setT`; `resize` makes the table fill the screen and keeps the floating scoreboard and status bars off it only when that costs little space, otherwise they float over the frame and the status line fades out), pointer input, the HUD, and online play (`transport`, `pump`, `snap`/`applySnap`).

The core block can be extracted and run in Node for headless tests (for example CPU vs CPU matches).
