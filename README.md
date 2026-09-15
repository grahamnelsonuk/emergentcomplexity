# Strange Loom

An audiovisual organism in a single HTML file.  One complex number drives the
picture, the reaction–diffusion skin stretched over it, and the music, all at
the same time.  Open `index.html` in a browser and press Begin.

No build step, no bundler, no server.  It is one file.

## The idea

Everything on screen and everything you hear comes from the same iteration:

```
z ← z² + c
```

* **The picture** is the Julia set of that map, drawn by escape time with a
  distance estimate for the glowing filaments and three orbit traps for colour.
* **The music** is the orbit of z itself.  The angle of each iterate picks a
  scale degree, its modulus picks an octave and a velocity, and an escape is a
  rest.  When c sits inside a period-n bulb the orbit is periodic, so the melody
  is a riff of exactly n notes.  Move c and the riff changes length.
* **The field** is a Gray–Scott reaction–diffusion system running in a
  ping-pong framebuffer, advected by a curl-and-vortex flow, lit by the same
  palette, and used to warp the fractal very slightly.  Every mote you strike
  injects into it.

Where c can go is the one design decision that makes the whole thing work.  It
rides a fixed distance from the boundary of the Mandelbrot set's main cardioid,

```
c(θ) = e^(iθ)/2 − e^(2iθ)/4,   offset along the outward normal by d
```

so every angle you can steer to is close to the boundary and therefore
interesting.  Positive d sits outside: dendrites and dust, orbits that escape.
Negative d sits inside: a filled set with an interior, orbits that stay bounded.
The page steps over d = 0 itself, where escape takes forever.

## Playing it

| | |
|---|---|
| move or drag | steer c.  Horizontal is the angle around the cardioid, vertical is the distance from its boundary |
| space | bloom – a shockwave that rings every mote it reaches, in an arpeggio ordered by distance |
| W A S D / arrows | nudge c by hand |
| H | hide the panel |
| M | mute |
| G | hand it back to drift |

Motes hang in the field.  A mote rings on the **rising edge** of a filament
sweeping onto it, so holding still is quiet and steering is what makes music.
Ring them in quick succession and **flow** climbs, which widens the multiplier,
thickens the bloom, raises the iteration count and adds notes.  Flow decays.
There is no fail state and nothing to lose.

Coherence accumulates through seven **epochs**, each of which changes the mode,
the tonic, the palette, the reaction–diffusion regime, the orbit traps and the
instrumentation:

| | | | |
|---|---|---|---|
| I | Seed | aeolian | drone and pad |
| II | Filament | dorian | the gliding lead wakes |
| III | Cascade | lydian | bells fall out of the orbit |
| IV | Lattice | phrygian dominant | sub-bass on the Cantor pulse |
| V | Nova | whole tone | a Thue–Morse pulse joins |
| VI | Aurora | pentatonic | shimmer across the high register |
| VII | Singularity | octatonic | everything at once |

## The music engine

Built on [Tone.js](https://tonejs.github.io/).  Nothing is sequenced by hand.

* **Melody** is read from the orbit, regenerated continuously as c moves.
* **Glissando** is the lead's whole point.  It is a monophonic voice held
  legato: consecutive notes call `setNote` rather than re-attacking, and
  portamento scales with flow, so the line swoops instead of stepping.  Blooms
  and epoch changes add an exponential filter sweep over the top.
* **Rhythm is fractal, not metrical.**  The bass and kick fall on the Cantor
  set over 27 sixteenths (remove the middle third, twice), and the hats follow
  the Thue–Morse sequence, the parity of the number of 1 bits in the step index.
  Neither is a 4/4 pattern and neither repeats the way one sounds like it will.
* **Epoch changes** play a Shepard riser: four octave-stacked oscillators
  sweeping two octaves under a bell-shaped gain, which reads as an endless rise.
* A 512-bin FFT on the master bus feeds bass, mid and high energy back into the
  shader, so the bloom and the warp breathe with what you are hearing.

## The renderer

Hand-written WebGL2, no 3D library, because the whole piece is full-screen
fragment shaders and a points pass:

1. **Field** – Gray–Scott in a half-resolution RGBA16F ping-pong pair, with
   spatially varying feed and kill so several regimes coexist in one frame.
2. **Fractal** – escape time with smooth iteration count, a distance estimate
   for the filaments, and point, line and circle orbit traps.
3. **Motes** – additive `GL_POINTS`, drawn into the scene buffer so they bloom.
4. **Bloom** – threshold prefilter, then a four-level dual-filter down/up chain.
5. **Grade** – ACES tone mapping, radial chromatic aberration, vignette, grain.

Resolution adapts to the frame time, so the flow does not break on slower
machines, and the field keeps its own resolution so a quality change never
wipes the pattern.  `prefers-reduced-motion` calms the motion and the
aberration without stopping the piece.

## Requirements

WebGL2 and a current browser.  Audio starts on the Begin click, as browsers
require.  The page says so plainly if WebGL2 is missing rather than showing a
black screen.  Best score is kept in `localStorage`, which the page works
without.

Tone.js loads from cdnjs.  Everything else, including every line of GLSL, is in
the file.
