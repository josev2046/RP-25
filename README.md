# RP-25 Radial Pitch Mapper

[![DOI](https://zenodo.org/badge/1361404148.svg)](https://doi.org/10.5281/zenodo.22662557)

## 1. What this is

The RP-25 is a browser-based reference tone generator. It exists to give you a stable, precisely-tuned pitch that you can compare by ear against an unquantised analogue sequencer (e.g. Korg SQ-10) or synthesiser oscillator, so you can tune the hardware to match.

It runs entirely in the browser using the Web Audio API. No installation or internet connection is required once the page has loaded.

## 2. SQ-10 Continuous Edition changes

This edition has been heavily updated from the original discrete-note RP-25 to behave like an authentic analogue control voltage source:

- **Continuous ±5V sweep:** The knob is now completely unquantised, sweeping smoothly across a full 10-octave range.
- **Decoupled envelopes:** Sweeping the knob glides the pitch without re-triggering the sound envelope, allowing seamless audio tracking.
- **Expanded radial map:** The dial now features 121 clickable markers. Large outer keys represent whole volts (C octaves), whilst inner ticks represent exact semitones.
- **Live telemetry:** The display now shows precise voltage output (e.g. `+2.45V`) and live cent deviation from the nearest semitone (e.g. `C4 +45¢`).
- Includes previous tuning upgrades: a **sine wave** option, **fine-tune control**, **master volume**, and a live **oscilloscope**.

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
| 2 | Readout (Volt / Note / Freq / Fine) | Display |
| 3 | Hold toggle | Button |
| 4 | Waveform selector | Button |
| 5 | Octave stepper | +/− buttons |
| 6 | Fine-tune stepper | +/− buttons |
| 7 | Master volume | Slider |
| 8 | Radial key dial | 121 clickable markers |
| 9 | Rotary knob | Continuous drag control |

## 5. Controls reference

| Control | Behaviour |
|---|---|
| **Radial keys (8)** | 121 markers spanning 10 octaves. The large outer keys mark integer voltages (−5V to +5V, always C notes). The inner ticks mark exact semitones. Click any marker to instantly snap the pitch to that precise note and trigger the sound. |
| **Rotary knob (9)** | Drag vertically to sweep the pitch continuously. The voltage is unquantised, meaning you can glide smoothly between semitones. Dragging updates the pitch without re-triggering the envelope, making it ideal for sweeping by ear towards a hardware match. |
| **Hold (3)** | Toggles drone mode. When on, the current note sustains indefinitely after you release the key or knob, freeing both hands for the hardware synthesiser. When off, sound stops shortly after release. |
| **Wave (4)** | Cycles through **SAW → TRI → SIN** on each press. Saw is harmonically rich and shows tuning discrepancies clearly; triangle is a purer tone for matching fundamentals; sine is the purest reference, with almost no harmonic content. |
| **Octave (5)** | Shifts the whole output range down two octaves or up three octaves (range −2 to +3). Changing octave applies immediately to a sounding note if Hold is on. |
| **Fine tune (6)** | Adjusts pitch in cents, from −50¢ to +50¢, in single-cent steps. Press and hold either button to step continuously. Fine-tune changes apply immediately to a note that is currently sounding. |
| **Volume (7)** | Sets the master output level. Takes effect immediately. |

## 6. Reading the display

The display shows four lines, updated live as you sweep or click:

- **VOLT** — the current simulated control voltage, from `-5.00V` to `+5.00V`.
- **NOTE** — the nearest standard note name, octave, and its cent deviation based on your continuous sweep position, e.g. `C3 +45¢`.
- **FREQ** — the exact frequency in Hertz, to two decimal places, e.g. `587.36 Hz`.
- **FINE** — the global fine-tune offset applied via the Fine controls, e.g. `+0¢`.

The oscilloscope panel to the left shows the live output waveform, taken after the master volume stage. It runs continuously once the audio engine has started, and will show a flat line when nothing is sounding.

### Frequency calculation

The RP-25 operates on a standard 1 Volt per Octave (1V/Oct) scale, working from a base of 0V = C2 (65.41 Hz). The frequency is calculated dynamically:

```
frequency = 65.41 × 2^(voltage + octave) × 2^(fine / 1200)
```
where `voltage` is the continuous dial position (−5.0 to +5.0), `octave` is the octave offset stepper (−2 to +3), and `fine` is the fine-tune offset in cents (−50 to +50).

## 7. Audio signal path

<img width="2000" height="920" alt="signal-flow-diagram" src="https://github.com/user-attachments/assets/6f0849cf-81a0-491c-be11-ce8d8fc1f83d" />

Signal flows: **Oscillator → Lowpass filter → Amplifier (with envelope) → Master volume → Oscilloscope tap and audio output.**

Two points worth knowing:

- The lowpass filter's cutoff is tied to the waveform: it opens further for triangle than for saw, and further still for sine, so each wave type reaches the output with an appropriate amount of harmonic content.
- The amplifier applies a short 20-millisecond rise on note-on, and a 100-millisecond exponential fall on note-off (when Hold is off), to avoid clicks.

## 8. Recommended tuning workflow

1. Connect the hardware sequencer's control voltage output to the synthesiser oscillator you want to tune.
2. Click the exact semitone marker you wish to target on the RP-25's radial dial, and switch **Hold** on so the reference tone sustains.
3. Set the RP-25's **Wave** and **Octave** to roughly match the character of the hardware oscillator.
4. Adjust the unquantised dial on your physical hardware sequencer (e.g., Korg SQ-10) until the hardware pitch matches the precise semitone generated by the RP-25. 
5. Listen closely to the combined sound; adjust your hardware dial until the auditory beating (a slow pulsing or "wah-wah") slows down and completely stops.
6. Switch **Hold** off, or click the next radial marker to tune the next step.

## 9. Compatibility notes

- The RP-25 is built for pointer and touch input. It has been tested with mouse, trackpad, and touchscreen interaction.
- It requires a browser with Web Audio API support. Older browsers, or browsers with Web Audio disabled, will not produce sound.
- Because the dial now covers an immense 120-semitone continuous range, drag sensitivity is purposefully high (`0.015V` per pixel of movement). Use the clickable markers to snap to exact notes reliably.

## 10. Troubleshooting

| Symptom | Likely cause | What to do |
|---|---|---|
| No sound at all | The audio engine hasn't started yet | Click a radial key or drag the knob once — browsers block audio until the first interaction. |
| Sound was working, then stopped after switching tabs | The browser suspended the audio context | Interact with the dial again to resume it. |
| Note doesn't change pitch when I press Octave | A note is sounding but Hold is off | Octave changes only apply live when Hold is on; otherwise they take effect on the next note. |
| Waveform on the scope looks flat | Nothing is currently sounding, or volume is at zero | Play a note and check the volume slider. |
| Knob feels extremely sensitive | 10-octave continuous map | To hit an exact, perfectly in-tune note without cent drift, click the radial markers rather than dragging the knob. |

## 11. Credits

RP-25 Radial Pitch Mapper (SQ-10 Continuous Voltage Edition)  
Created and developed by Jose Velazquez MA  
Voltage & Wave — [voltageandwave.co.uk](https://voltageandwave.co.uk/)

