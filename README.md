# RP-25 Radial Pitch Mapper

[![DOI](https://zenodo.org/badge/1361404148.svg)](https://doi.org/10.5281/zenodo.22662557)

## 1. What this is

The RP-25 is a browser-based reference tone generator. It exists to give you a stable, precisely-tuned pitch that you can compare by ear against an unquantised analogue sequencer (e.g. Korg SQ-10) or synthesiser oscillator, so you can tune the hardware to match.

It runs entirely in the browser using the Web Audio API. No installation or internet connection is required once the page has loaded.

## 2. Tuning Edition changes

This edition adds four things to the original RP-25:

- A **sine wave** option, in addition to saw and triangle.
- A **fine-tune control**, adjustable in cents, for correcting small pitch discrepancies.
- A **master volume control**.
- An **oscilloscope** on the display, showing the live waveform.

## 3. Requirements

- A modern desktop or mobile browser with Web Audio API support (Chrome, Firefox, Safari, Edge).
- Audio output (speakers or headphones).
- A pointing device: mouse, trackpad, or touchscreen. **The interface has no keyboard controls** — every control must be operated with a pointer or touch.

Most browsers block audio until the page has been interacted with. The RP-25 starts its audio engine the first time you press a key or the knob — see [Section 10, Troubleshooting](#10-troubleshooting) if you get no sound.

## 4. Panel layout

<img width="1800" height="1520" alt="panel-layout-diagram" src="https://github.com/user-attachments/assets/24208276-600e-418b-afc1-16e77c19c7c0" />

| # | Control | Type |
|---|---|---|
| 1 | Oscilloscope | Live waveform display |
| 2 | Note / frequency / fine readout | Display |
| 3 | Hold toggle | Button |
| 4 | Waveform selector | Button |
| 5 | Octave stepper | +/− buttons |
| 6 | Fine-tune stepper | +/− buttons |
| 7 | Master volume | Slider |
| 8 | Radial key arc | 25 clickable notes |
| 9 | Rotary knob | Drag control |

## 5. Controls reference

| Control | Behaviour |
|---|---|
| **Radial keys (8)** | 25 notes arranged over a 270-degree arc, spanning two octaves plus one note. Press and hold a key to sound its pitch; release to stop it, unless Hold is on. |
| **Rotary knob (9)** | Drag up or down to sweep continuously through the 25-note range. The knob snaps to the nearest semitone and sounds each note as you cross it, which is useful for sweeping by ear towards a match. Releasing the knob stops the note, unless Hold is on. |
| **Hold (3)** | Toggles drone mode. When on, the current note sustains indefinitely after you release the key or knob, freeing both hands for the hardware synthesiser. When off, sound stops shortly after release. |
| **Wave (4)** | Cycles through **SAW → TRI → SIN** on each press. Saw is harmonically rich and shows tuning discrepancies clearly; triangle is a purer tone for matching fundamentals; sine is the purest reference, with almost no harmonic content. |
| **Octave (5)** | Shifts the whole 25-note range down two octaves or up three octaves (range −2 to +3) to match the operating range of your hardware. Changing octave while a note is sounding only updates the pitch immediately if Hold is on; otherwise it takes effect on the next note you play. |
| **Fine tune (6)** | Adjusts pitch in cents, from −50¢ to +50¢, in single-cent steps. Press and hold either button to step continuously. Fine-tune changes apply immediately to a note that is currently sounding, whether or not Hold is on. |
| **Volume (7)** | Sets the master output level. Takes effect immediately. |

## 6. Reading the display

The display shows three lines, updated whenever a note sounds:

- **NOTE** — the note name and octave, e.g. `D5`.
- **FREQ** — the exact frequency in Hertz, to two decimal places, e.g. `587.36 Hz`.
- **FINE** — the current fine-tune offset in cents, e.g. `+0¢`.

The oscilloscope panel to the left shows the live output waveform, taken after the master volume stage. It runs continuously once the audio engine has started, and will show a flat line when nothing is sounding.

### Frequency calculation

The RP-25 works from a base of C2 (65.41 Hz) in equal temperament. The frequency for a given note is:

```
frequency = 65.41 × 2^((key + octave×12) / 12) × 2^(fine / 1200)
```

where `key` is the note's position (0–24) on the radial arc, `octave` is the octave offset (−2 to +3), and `fine` is the fine-tune offset in cents (−50 to +50).

## 7. Audio signal path

<img width="2000" height="920" alt="signal-flow-diagram" src="https://github.com/user-attachments/assets/6f0849cf-81a0-491c-be11-ce8d8fc1f83d" />

Signal flows: **Oscillator → Lowpass filter → Amplifier (with envelope) → Master volume → Oscilloscope tap and audio output.**

Two points worth knowing:

- The lowpass filter's cutoff is tied to the waveform: it opens further for triangle than for saw, and further still for sine, so each wave type reaches the output with an appropriate amount of harmonic content.
- The amplifier applies a short 20-millisecond rise on note-on, and a 100-millisecond exponential fall on note-off (when Hold is off), to avoid clicks.

## 8. Recommended tuning workflow

1. Connect the sequencer's control voltage output to the synthesiser oscillator you want to tune.
2. Select the target note on the RP-25 and switch **Hold** on so the reference tone sustains.
3. Set the RP-25's **Wave** and **Octave** to match the range and character of the hardware oscillator — saw or triangle is usually easiest for spotting beating; sine is useful once you're close, for a cleaner final check.
4. Adjust the sequencer's unquantised voltage control until the hardware pitch matches the RP-25, listening for the beating (a slow pulsing or "wah-wah" in the combined sound) to slow down and stop.
5. If you need to nudge the RP-25 itself — for example, to match a hardware oscillator that is a few cents off standard tuning — use **Fine tune** rather than re-selecting the note.
6. Switch **Hold** off, or select the next note, to move on.

## 9. Compatibility notes

- The RP-25 is built for pointer and touch input. It has been tested with mouse, trackpad, and touchscreen interaction.
- It requires a browser with Web Audio API support. Older browsers, or browsers with Web Audio disabled, will not produce sound.
- The rotary knob's sensitivity is fixed; a full 25-note sweep corresponds to roughly 160 pixels of vertical drag.

## 10. Troubleshooting

| Symptom | Likely cause | What to do |
|---|---|---|
| No sound at all | The audio engine hasn't started yet | Press a key or the knob once — most browsers block audio until the first interaction with the page. |
| Sound was working, then stopped after switching tabs | The browser suspended the audio context | Press a key or the knob again to resume it. |
| Note doesn't change pitch when I press Octave | A note is sounding but Hold is off | Octave changes only apply live when Hold is on; otherwise they take effect on the next note. |
| Waveform on the scope looks flat | Nothing is currently sounding, or volume is at zero | Play a note and check the volume slider. |
| Knob feels too sensitive or not sensitive enough | Fixed drag sensitivity | This is set in the code (0.15 units of pitch per pixel of vertical drag) and isn't user-adjustable in this version. |

## 11. Credits

RP-25 Radial Pitch Mapper (Tuning Edition)
Created and developed by Jose Velazquez MA
Voltage & Wave — [voltageandwave.co.uk](https://voltageandwave.co.uk/)
