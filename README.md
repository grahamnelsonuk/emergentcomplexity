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
  distance estimate for the glowing filaments and four orbit traps for colour.
  Filled sets – the rabbits and basilicas you get when c sits inside a bulb –
  are lit from within: inside an attracting basin the derivative collapses
  geometrically, and the log of it has genuinely fractal level sets, so it
  draws contour bands that survive any zoom.
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
| move the mouse | steer c.  Horizontal is the angle around the cardioid, vertical is the distance from its boundary |
| click | dive toward that point.  Shift-click or right-click pulls back out |
| drag | pan.  Wheel or pinch zooms toward the cursor |
| click a mote | pluck it by hand |
| space | bloom – a shockwave that rings every mote it reaches, in an arpeggio ordered by distance |
| 1 – 7 | hold a drum cycle on, then off, then back under the control of flow |
| W A S D / arrows | nudge c by hand |
| R | return to the whole set |
| Q / E | zoom out and in from the keyboard |
| H | hide the panel |
| M | mute |
| G | hand it back to drift |

Once you are flown in, the camera clings to the boundary: the set moves under
the view as c changes, and without that you end up staring at dead space.  A
short spiral search runs a few times a second and eases the view target back
onto the nearest filament; if the structure has left the frame entirely, the
zoom backs off until it is in reach again, rather than hanging in the void.
The seed itself also slows down with depth, because a fast c whips the
structure clean out of a tight frame.

A click does not simply zoom at the cursor.  It looks for the nearest
filament first – a golden-angle spiral search, falling back to an outward
ray-march with bisection onto the boundary when you are deep inside a filled
basin – and flies there.  A long hop travels without zooming so you cross to
the structure before diving into it.  Filament thickness and the halo around
the set are specified in pixels and converted to plane units each frame, so
detail holds its weight however deep you go, and the iteration count rises
with the log of the zoom.

Motes hang in the field, placed inside whatever you are looking at and biased
toward the set, so they stay reachable however deep you have flown.  A mote
rings on the **rising edge** of a filament sweeping onto it, so holding still
is quiet and steering is what makes music.
Ring them in quick succession and **flow** climbs, which widens the multiplier,
thickens the bloom, raises the iteration count and adds notes.  Flow decays.
There is no fail state and nothing to lose.

Coherence accumulates through seven **epochs**, each of which changes the mode,
the tonic, the palette, the reaction–diffusion regime, the orbit traps and the
instrumentation:

| | | | | | |
|---|---|---|---|---|---|
| I | Seed | aeolian in A | 58 | – | a drone and a slow pad, no pulse yet |
| II | Filament | dorian in G | 62 | 3 | the gliding lead wakes, a shaker in 3 |
| III | Cascade | lydian in C | 67 | 3 5 | bells fall out of the orbit, toms in 5 across the 3 |
| IV | Lattice | phrygian dominant in F | 72 | 3 5 7 27 | sub-bass, kick in 7, the Cantor set on the woodblock |
| V | Nova | whole tone in B♭ | 78 | 3 5 7 11 27 n | a rim figure in 11, and the orbit's own period joins as a drum |
| VI | Aurora | pentatonic in A♭ | 84 | 3 5 7 11 13 27 n | shimmer up high, a ride in 13 over everything |
| VII | Singularity | octatonic in B | 90 | all | every cycle running at once |

Each epoch also changes the tempo, the delay subdivision, the lead's waveform,
the reverb depth, the palette, the reaction–diffusion regime, which orbit trap
colours the picture, the frequency of the escape-time banding, and the size of
the halo around the set.  The transition is a Shepard riser, so it sounds like
a climb with no top.  The guide inside the page lists all of this, built from
the same table the code runs on, so it cannot drift out of date.

## The polyrhythm

Seven cycles, each turning on its own length in sixteenth notes: 3, 5, 7, 11,
13, 27, and one borrowed from the orbit.  They are co-prime, so they phase
against each other and the whole web only comes back round on their least
common multiple – 3·5·7·11·13·27 = **135,135 sixteenths**, a little over six
hours at Singularity's tempo.  It does not repeat while you are listening.

Two of the cycles are fractal in their own right.  The 27 carries the Cantor
set: take away the middle third, twice, and play what is left, which is
`[0 2 6 8 18 20 24 26]`.  The accents follow the Thue–Morse sequence, the
parity of the number of ones in the step index's binary expansion.  And the
last cycle is the orbit's own: when c sits inside a period-n bulb the orbit
closes after n steps, and n becomes a drum, so the rhythm is the literal
period of the point you are steering.  Period 1 – a fixed point – is not a
rhythm, so it does not count, and an escaping orbit has no period at all.

Layers arrive as flow rises and leave as it decays, so the groove accumulates
while you are in it and thins when you stop.  Hits land a few milliseconds
late with a little jitter and a velocity that follows the accent pattern,
because a grid does not sound like a drummer.  The lattice at the bottom left
is the live picture of it: one row per turning cycle, a lit dot for each onset,
and a bright head where the pulse has reached.

## The music engine

Built on [Tone.js](https://tonejs.github.io/).  Nothing is sequenced by hand.

* **Melody** is read from the orbit, regenerated continuously as c moves.
* **Glissando** is the lead's whole point.  It is a monophonic voice held
  legato: consecutive notes call `setNote` rather than re-attacking, and
  portamento scales with flow, so the line swoops instead of stepping.  Blooms
  and epoch changes add an exponential filter sweep over the top.
* **Rhythm is fractal and polyrhythmic**, never metrical.  See above.  The
  bass line also falls on the Cantor set, pitched from the orbit.
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
   for the filaments, and point, line, circle and grid orbit traps.
3. **Motes** – additive `GL_POINTS`, drawn into the scene buffer so they bloom.
4. **Bloom** – threshold prefilter, then a four-level dual-filter down/up chain.
5. **Grade** – ACES tone mapping, radial chromatic aberration, vignette, grain.

Resolution adapts to the frame time, so the flow does not break on slower
machines, and the field keeps its own resolution so a quality change never
wipes the pattern.  Diving smears the frame along the radius, which is what
makes it read as travel rather than scaling.  `prefers-reduced-motion` calms
the motion and the aberration without stopping the piece.

Two things worth knowing if you read the shader.  The palette is the usual
cosine form, `a + b·cos(2π(c·t + d))`, and its bias must be at least its
amplitude or channels go negative and clamp to black.  And the distance
estimate only means anything once an orbit has escaped: using it on interior
points makes the filament term explode, which punches a hole through
everything else in the frame.

## Requirements

WebGL2 and a current browser.  Audio starts on the Begin click, as browsers
require.  The page says so plainly if WebGL2 is missing rather than showing a
black screen.  Best score is kept in `localStorage`, which the page works
without.

The crackle that survived every scheduling fix was not timing at all, and the
page's own diagnostics said so: zero late sixteenths, zero rebuilds.  A
spectrum measurement found it.  The two loudest bands in the whole mix were
20&ndash;40 Hz and 40&ndash;80 Hz, sitting fifteen decibels above everything
over 200 Hz &ndash; the drone's lower voice was a sine an octave below the
root, which on the opening chord is 27.5 Hz, and the kick was at 31 Hz.  No
small speaker can reproduce either: the driver reaches its excursion limit and
rattles, and the rattle is heard as crackle.  It was also the reason nothing
about scheduling helped and why it started the moment the page did.

The drone's lower voice is now a triangle an octave *above* the root, the kick
is an octave up at around 62 Hz, and everything passes a 38 Hz high-pass on the
way out so nothing inaudible reaches the speakers or eats headroom.  The
melodic bus was also being low-passed at 700 Hz whenever flow was low, which is
what left the mix as bass and rumble with nothing in the middle; it now opens
at 1.4 kHz.  Measured result: the 20&ndash;40 Hz band is down twenty-five
decibels, the mids are up nine, and true peak is 0.79 with no clipped samples.

The guide has keys to take out the drone, the drums and the wet effects one at
a time, so a listener can localise a problem that I cannot hear.

The mix is built for headroom rather than loudness, because the first version
crackled when the set moved fast and threw a lot of notes at once.  Every
voice sits well below unity, a compressor holds the peaks, a tanh waveshaper
soft-clips anything still over the top so it saturates instead of breaking up,
and a limiter catches the rest.  The drums are all monophonic and dry, the
reverb's impulse response is under five seconds rather than eight, and the
melodic and percussive voices draw on separate refilling budgets so a burst of
mote rings can neither drown the audio thread nor punch a hole in the groove.

One round of these fixes made things worse, and the useful clue was that the
mute button stopped working.  I had replaced Tone's audio context to get a
bigger hardware buffer, but `Tone.Destination` is a singleton bound to the
*original* context, so mute was ramping a node nothing was connected to &ndash;
and, far worse, two AudioContexts were left running and competing for the
audio device, which crackles from the first second with no interaction at all.
Never swap the context; set `lookAhead` on the one Tone already owns.  Mute now
goes through an output gain in our own chain, with an explicit linear ramp,
because `rampTo` on a gain is an exponential approach that never actually
arrives at zero.

Glitching on movement turned out to be scheduling, not levels.  Tone's
sequencer callback runs on the main thread, and with the default 100 ms
lookahead any main-thread stall longer than that makes a sixteenth arrive
after its own scheduled time, at which point everything in it fires at once.
Three things were causing that, all measurable:

* **Reallocating render targets** took the better part of a second, and it
  happened whenever adaptive quality nudged the render scale.  The targets are
  now allocated once at full size and a quality change only shrinks the
  viewport drawn into, with each pass scaling its sampling into the live
  region.  Measured worst frame went from 690 ms to 3 ms.
* **A high-polling mouse.**  Reporting at up to 1 kHz, every event was doing
  steering maths and touching the DOM.  The handler now only records the
  latest position and the frame applies it once.
* **Notes scheduled from the render loop.**  A mote ring fired at whatever
  `Tone.now()` happened to be, so several rings in one frame landed on the
  same audio timestamp and retriggered monophonic voices mid-note.  Rings are
  queued and fired from the sequencer instead, spread across the step, which
  also locks them to the groove rather than floating over it.

On top of that the context is created with `latencyHint: 'balanced'` and a
200 ms lookahead, the voices are fixed round-robin banks rather than
Tone's `PolySynth` (which allocates and garbage-collects voices, so a bursty
part rebuilds nodes mid-performance), and layer gating has hysteresis so a
wobbling flow cannot strobe the groove.  Late sixteenths under a hard mouse
sweep went from three, worst case 453 ms late, to none.

A convolution reverb that receives a single NaN sample stays silent for good,
and a starved audio thread can produce one, so a watchdog rebuilds the graph if
that happens.  Twice and it drops the convolver and runs a plainer chain.  It
inspects the master waveform for a non-finite *sample*, rather than reading a
level in decibels: true silence and a NaN both read as -Infinity on a meter, so
a level-based test cannot tell a poisoned graph from a quiet passage, and this
piece has quiet passages by design.  A run of exact zeros is the secondary net
and has to persist for twenty-four seconds before it counts.

The guide has a short diagnostics line: how many sixteenths arrived late, how
many times the graph was rebuilt, the sample rate and the scheduling slack.
Late sixteenths are the number worth reporting, because they mean the main
thread stalled; if that count is zero and something still sounds wrong, the
cause is elsewhere.

There is a short note from me in the guide, under the controls and the epochs.
It is about why the whole piece runs on one equation, and about the thin shell
around the boundary where anything interesting happens.

Tone.js loads from cdnjs.  Everything else, including every line of GLSL, is in
the file.
