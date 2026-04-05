# Timbric Counterpoint

[![Demo video](https://vumbnail.com/1180307052.jpg)](https://vimeo.com/1180307052)

A Pure Data (Pd) sound-synthesis patch that reinterprets J. S. Bach's
*Invention No. 9 in F minor, BWV 795* as a three-voice **timbric counterpoint**:
each contrapuntal voice is rendered with a distinct synthesis technique, so
the polyphony unfolds simultaneously in pitch-space and in timbre-space.

> "Counterpoint is not only the art of combining melodies, but of combining
> ways of sounding."

---

## Overview

The patch loads three monophonic MIDI sequences (one per voice of the Bach
Invention) and routes each to an independent synthesis engine:

| Voice | Source file | Synthesis approach |
|------:|-------------|--------------------|
| 1 | `counterpoint_1_a.mid` | Wavetable / sampled-tone playback using `yamaha_c4.wav` (pitch-shifted via `tabread4~`) |
| 2 | `counterpoint_2_a.mid` | Additive / waveshaping synthesis driven by the `waveshape` array |
| 3 | `counterpoint_3_a.mid` | Subtractive synthesis (filtered noise / oscillator with `lop~` and envelopes) |

A shared transport (`metro`-driven sequencer) keeps the three voices in
rhythmic alignment, while per-voice amplitude, filter and effects parameters
are exposed as sliders on the main canvas.

### Signal-flow (high level)

```
 ┌─────────────┐   ┌──────────────────┐   ┌──────────────┐
 │ cyclone/seq │ → │ cyclone/midiparse│ → │ voice engine │ ─┐
 └─────────────┘   └──────────────────┘   └──────────────┘  │
 (× 3 voices, one per MIDI file)                            ▼
                                                      ┌───────────┐
                                                      │  mixer /  │
                                                      │  master   │ → dac~
                                                      └───────────┘
```

---

## Repository structure

```
timbric-counterpoint/
├── .gitignore
├── LICENSE              ← MIT (covers the Pd patch / code)
├── LICENSE-DATA         ← CC BY 4.0 (covers MIDI + WAV data)
├── CHANGELOG.md
├── README.md
└── patch/
    ├── timbric_counterpoint.pd   ← main patch (open this one)
    ├── counterpoint_1_a.mid      ← voice 1 (soprano)
    ├── counterpoint_2_a.mid      ← voice 2 (alto)
    ├── counterpoint_3_a.mid      ← voice 3 (bass)
    └── yamaha_c4.wav             ← C4 piano sample used by voice 1
```

---

## Requirements

- **[Pure Data](https://puredata.info/) (Pd-vanilla) ≥ 0.52** — tested on 0.54.
- **`cyclone` external library** — provides `cyclone/seq` and
  `cyclone/midiparse`, used to read the MIDI files.

### Installing `cyclone`

Inside Pd: `Help → Find externals…` → search for `cyclone` → click **Install**.
Then ensure it is loaded automatically: `Pd → Preferences → Startup…` and add
`cyclone` to the list of libraries to load at start-up.

---

## Running the patch

1. Clone the repository:
   ```bash
   git clone https://github.com/sml.pereira/timbric-counterpoint.git
   cd timbric-counterpoint
   ```
2. Open `patch/timbric_counterpoint.pd` in Pure Data.
3. Verify DSP is on (it is toggled on automatically by `loadbang`).
4. Click the **green bang** (top-left) to start playback.
   Click the **red bang** (or the `stop` message) to halt.
5. Use the on-canvas sliders to adjust per-voice level, filter cutoff,
   resonance and master gain.

If you hear silence, check that:
- `cyclone` is installed and loaded (see above);
- the Pd audio device is selected in `Media → Audio settings…`;
- the patch was opened **from inside `patch/`** so that `yamaha_c4.wav` and
  the `.mid` files resolve correctly (they are referenced by filename only).

---

## The source work

The underlying composition is **J. S. Bach — *Three-part Invention No. 9 in F minor,
BWV 795*** (public domain). The MIDI files shipped here encode a
three-voice transcription used as the driver for the three synthesis
engines. A reference score can be obtained from [IMSLP](https://imslp.org/wiki/Sinfonia_in_F_minor,_BWV_795_(Bach,_Johann_Sebastian)).

---

## Licensing

This repository uses a dual-licence scheme common to research /
art-code projects:

- **Code** — the Pure Data patch (`patch/timbric_counterpoint.pd`) and all
  other source-like files: **MIT**, see [`LICENSE`](./LICENSE).
- **Data** — the MIDI files (`patch/counterpoint_*_a.mid`) and the audio
  sample (`patch/yamaha_c4.wav`): **Creative Commons Attribution 4.0
  International (CC BY 4.0)**, see [`LICENSE-DATA`](./LICENSE-DATA).

---

## Contributing

Issues and pull requests are welcome. For substantial changes, please open
an issue first to discuss what you would like to change.

---

## Acknowledgements

- Miller Puckette and the Pure Data community.
- The `cyclone` library maintainers.
- J. S. Bach — for the counterpoint.
