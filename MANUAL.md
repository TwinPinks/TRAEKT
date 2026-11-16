# TRAEKT

**TWIN PINKS TRAEKT** is a spatial waveform generator for VCV Rack. Two
independently moving points travel along a generated three-dimensional
trajectory, and their X, Y and Z coordinates become six output voltages. Each
point has its own frequency: one can be an LFO while the other runs at audio
rate. Rotation turns the trajectory; Spatial Fold reshapes it.

## Ports

The upper row of sockets is point 1, the lower row point 2.

| Port | Function |
|---|---|
| **FREQ** | Frequency CV, 1 V/oct; adds to the frequency set for that point |
| **TRIG** | Resets that point to the position set by POS |
| **MOD** | Modulation input; its destination and depth are selected separately for each point |
| **X** | X-coordinate output |
| **Y** | Y-coordinate output |
| **Z** | Z-coordinate output, or a combination of X and Y selected in Third Wave |

Output level and waveform depend on the figure, its orientation, Fold and
modulation, so neither is fixed from shape to shape. The Normalization setting
in the menu holds the level at ±5 V instead.

## Controls

Buttons choose which field to edit; the encoder changes its value. Drag the
encoder vertically or use the mouse wheel. The selected field is highlighted on
the screen.

| Control | Function |
|---|---|
| **NEW SHAPE** | Generates another figure using the selected Shape Algo |
| **VIEW** | Switches between Space and Waves |
| **ROTATE** | Selects the rotation speed field; pressing it again starts or stops automatic rotation |
| **Point 1 / Point 2 buttons** | Select a point; repeated presses cycle through FREQ, POS, MOD and DEPTH |
| **FOLD button** | Selects the shared base Fold field |

The corresponding fields at the top and bottom of the screen are also clickable.

### Point fields

| Field | Range | Function |
|---|---|---|
| **FREQ** | 0.01–10240 Hz | Laps around the trajectory per second; defaults: point 1 = 0.60 Hz, point 2 = 0.04 Hz |
| **POS** | 0–99% | Position on the trajectory to which TRIG returns the point; defaults: 0% and 50% |
| **MOD** | FOLD / FM / AM / RING | Selects the destination of that point’s MOD input |
| **DEPTH** | 0–100% | Sets the modulation amount; default: 0% |

Hold **Shift** while adjusting FREQ for finer increments. When selecting a MOD
type with the encoder, turn through the selection interval until the next type
appears; the selection deliberately changes more slowly than other fields.

POS sets the reset location, not the current position of a freely running point.
Changing POS alone does not restart it.

### Fold and rotation

**FOLD** ranges from **×1 to ×5**. At ×1 the figure is unchanged. Increasing it
expands the figure and folds it back through its radial boundary. The X, Y and Z
waveforms change together because their source geometry changes.

The base Fold setting is shared, but each point adds its own modulation on top
of it, so at any moment the two points can be folded by different amounts.

**ROTATE** ranges from **×0 to ×20**, with ×1 as the default. A new module starts
with rotation running. This is a rotation speed multiplier, not an oscillator
frequency. Setting it to ×0 stops automatic rotation. Stopping rotation does not
stop either point.

In **Space**, drag the figure to change its orientation by hand. Automatic
rotation continues from the adjusted orientation.

## Trigger and hard sync

Each TRIG input works independently. A rising signal crossing **1 V** resets
its point to POS. It must fall to **0.1 V** or below before another rising edge
can trigger a reset. Holding a gate high does not hold or stop the point.

At low rates, TRIG restarts a modulation contour. At audio rates it gives hard
sync: repeated resets cut the trajectory short, and the section between them
becomes the waveform. The point keeps looping after a reset.

## Modulation

Patch a signal into the MOD input of the point being modulated, select its MOD
type and raise DEPTH. At 0% the input has no effect.

The points are not internally connected to each other’s MOD inputs. To make one
modulate the other, patch a cable; both directions at once are fine. Any other
CV or audio source works the same way.

The depth figures below assume a modulation signal of **±5 V**.

| Type | Behaviour |
|---|---|
| **FOLD** | Adds modulation to Fold. At 100% depth, ±5 V adds ±4 to the base amount. The lower limit depends on the Fold modulation menu setting. |
| **FM** | Linear through-zero frequency modulation. At 100% depth, ±5 V produces a deviation of ±4 times the point’s base frequency. |
| **AM** | Crossfades from the unmodulated signal to multiplication by a unipolar version of the MOD signal. |
| **RING** | Crossfades from the unmodulated signal to multiplication by the bipolar MOD signal. At full depth, negative modulation reverses output polarity. |

Through-zero FM can slow a point to a stop and reverse its direction without
resetting its position. This is independent of through-zero Fold, which inverts
the geometry rather than the direction of travel.

Ring modulation can reduce the output level as DEPTH increases. Nothing
compensates for that automatically: Normalization is measured on the
unmodulated signal, not on the modulated result.

## Screen

**Space** shows the trajectory, its orientation and the two moving points.
The large pink marker is point 1, the smaller light one point 2.

**Waves** shows the three coordinate waveforms for the current shape, orientation
and base Fold setting, with markers for both points. The third trace follows
the Third Wave setting. The **pp** readout gives the peak-to-peak voltage of the
displayed curve. Dragging a point’s position marker changes its POS.

The display is not an oscilloscope of the final outputs: the curves leave out
each point’s MOD-input processing. Audio and CV run independently of the screen
refresh rate.

## Menu (right-click)

### Shape Algo

* **Harmonic** — curves built from combinations of harmonics.
* **Lissajous** — sinusoidal motion along the three axes.
* **Torus Knot** — closed paths around a torus.
* **Closed Spline** — smooth paths through generated control points.
* **Chaos Capture** — a captured section of chaotic motion formed into a closed path.

Selecting an algorithm generates a figure. Changing a figure while listening may
cause a discontinuity.

### Fold modulation

* **Limit (floor ×1)** — default. Modulation cannot reduce Fold below ×1.
* **Through zero** — modulation can reduce Fold below ×1, contract the geometry
  to zero and continue into a centrally inverted form.

In Through zero mode, a negative Fold value uses the same folding magnitude as
the corresponding positive value, with the coordinates inverted through the
centre. Modulated Fold has a protective limit of **−12 to +12**; this is separate
from the base control’s ×1–×5 range. In Limit mode the lower boundary is ×1.

### Output

* **Off (raw projection)** — keeps the raw projected coordinates, DC offset and
  all.
* **Centering (no DC offset)** — default. Subtracts each output’s mean so it
  sits around zero.
* **Normalization (±5 V per axis)** — centres each output and scales it to a
  ±5 V peak.

Centering matters when an output drives an audio-rate modulator, where a DC
component biases the destination. It is not exact: while Fold or rotation move
quickly, the mean still drifts.

Normalization brings X, Y and Z to the same peak no matter how far the figure
reaches along each axis. Only the level changes; the waveform and its harmonics
stay as they are. An axis the figure barely moves along is raised to full scale
too, which is why this is a choice rather than the default.

Two things stay outside it. AM and RING can only pull the level down, so ±5 V
remains the ceiling as long as the MOD input stays within ±5 V. The CMP, AND and
XOR settings of Third Wave already produce fixed ±5 V levels and are left alone.

### Third Wave

This setting affects both Z output sockets. X and Y remain unchanged.

| Mode | Signal at Z |
|---|---|
| **Z** | Z coordinate; default |
| **AVG** | (X + Y) / 2 |
| **DIFF** | (X − Y) / 2 |
| **MIN** | Lower instantaneous value of X and Y |
| **MAX** | Higher instantaneous value of X and Y |
| **CMP** | Positive when X ≥ Y; negative otherwise |
| **AND** | Positive when both X and Y are positive; negative otherwise |
| **XOR** | Positive when exactly one of X and Y is positive; negative otherwise |

## Patches and presets

Rack patches and module presets save the figure’s algorithm and seed, the field
values, the orientation and the menu options. That is enough to rebuild the same
figure — there is no separate waveform file to keep track of. Where the two
points happen to be along the trajectory is not saved.

---

© TWIN PINKS. Concept and development by Stepan Chernov.
