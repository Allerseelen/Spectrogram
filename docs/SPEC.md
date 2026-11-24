# Spectrogram Tool: Specification

A lightweight browser tool for a speech and hearing science course. Students and instructors record speech, view it as a Spectrogram, stack utterances for comparison, play them back, and (phase 2) alter F0 and formant spacing to hear and see the effect.

Terms in **bold** are defined in [GLOSSARY.md](../GLOSSARY.md). Decisions with lasting trade-offs live in [docs/adr/](adr/).

## Users and setting

- **Instructor** demoing live on a projector during lecture.
- **Students** on their own laptops, in lab or at home.
- Laptop and desktop screens only. No phone layout.
- Must stay readable on a projector: large labels, high contrast.

## Platform

- Static single-page web app. No server, no accounts.
- TypeScript + Vite. No heavy UI framework.
- Spectrograms drawn on Canvas. FFT work runs in a Web Worker so the UI stays responsive.
- Hosted as static files on GitHub Pages, linked from the course site.
- Browser support: Chrome and Edge fully supported. Firefox and Safari best-effort.

## Phases

| Phase | Scope |
|---|---|
| 1 | Recording, display, Stack, playback, naming, zoom, analysis modes, import/export, hover readout |
| 2 | F0 Shift and Formant Dispersion Scale sliders, Derived Tracks, F0 contour overlay, save/load Stack |
| Out of scope | Live voice modulator, automatic formant tracks (LPC), per-Track setting overrides |

---

## Phase 1

### 1. Stack and Tracks

- The app shows one **Stack**: a vertical list of **Tracks**.
- Each Track shows the **Spectrogram** of exactly one **Recording**.
- Each Track has a fixed minimum height. The page scrolls when Tracks exceed the viewport.
- Above 8 Tracks, show a non-blocking warning that the Stack may not be legible on a projector. No hard limit.
- **Selected Track**: clicking a Track selects it (highlighted border). At most one Selected Track.
- **Rename**: click the Track name to edit inline. Enter confirms, Esc cancels.
- **Reorder**: drag a Track to a new position.
- **Delete**: an X button in the top-right corner of each Track deletes *that* Track (not necessarily the Selected Track). Confirmation prompt:
  > Are you sure you want to delete the Track "<name>"?

  In phase 2, if the Track has Derived Tracks, append: "Its N Derived Tracks will be kept."

### 2. Recording

- **Record new Track** button (shortcut **R**) appends a new Track at the bottom of the Stack and starts recording immediately.
- Recording stops automatically when it reaches the **Time Span**, or earlier when the user presses Stop (or **Esc**).
- No pause during recording.
- The Spectrogram draws left to right on a fixed time axis while recording. It does not scroll.
- When recording stops, the Track's name field is focused with default text "Track N" selected (N = next unused number). Typing replaces it; Enter keeps it.
- Re-recording into a Track that already holds a Recording replaces it after a confirmation prompt.
- **Microphone capture**:
  - `echoCancellation`, `noiseSuppression`, and `autoGainControl` all **off**. These alter the speech spectrum.
  - Mono, at the device's native sample rate (typically 44.1 or 48 kHz).
  - Show a live input level meter with a clipping indicator.
  - If mic permission is denied or no device exists, show a plain message explaining how to allow access. Import still works.
- All Stack settings are locked while recording.

### 3. Time axis

- All Tracks share one time axis. Every Track starts at t = 0 at the left edge. 1 s is the same width in every Track. Shorter Tracks end early.
- **Time Span** options: 10, 15, 30, 60 s. Default: 10 s.
- **Fit to signal** sets the Time Span to the duration of the longest Track.
- The Time Span is also the maximum length of a new Recording.

### 4. Zoom

- Zoom affects the time axis only, and applies to every Track at once.
- The Time Span is the fully zoomed-out view.
- Controls:
  - **+ / −** buttons.
  - **Ctrl + scroll wheel**, centered on the cursor.
  - **Zoom to Region**: fills the view with the current Region.
  - **Zoom out to Time Span**: resets.
- When zoomed in, one horizontal scrollbar (shared by all Tracks) pans the view.

### 5. Analysis settings (Stack-wide)

All settings below apply to the whole Stack. There are no per-Track overrides. Changing any setting after recording redraws every Track from stored audio without re-recording.

**Mode / FFT Window Length**

| Mode | FFT Window Length |
|---|---|
| Wideband (default) | 4 ms |
| Narrowband | 40 ms |
| Custom | 1–100 ms, numeric input |

- Window shape: Gaussian (Praat-style).
- Time step between frames: chosen automatically from the FFT Window Length. Not exposed.
- Pre-emphasis: on (boosts high frequencies so upper formants are visible).
- The Stack header shows the active mode, e.g. "Wideband 4 ms" or "Custom 12 ms".

**Frequency axis**

- Scales: **Linear** (default), **Log**, **Bark**, **Mel**.
- Range: editable min and max in Hz. Default 20 Hz – 10 kHz.
- Minimum allowed value is 20 Hz (needed for Log, Bark, Mel).
- Presets: **20 Hz – 5 kHz** (vowel formants) and **20 Hz – 10 kHz**.
- Tick labels are always in Hz, positioned according to the scale. Axis title names the scale, e.g. "Frequency (Hz, Bark scale)".

**Color and level**

- Colormap: **Inferno** (default) or **Grayscale**.
- Dynamic range: 70 dB.
- Each Track is normalized to its own peak level, so quiet recordings stay visible. Consequence: brightness is not comparable across Tracks. This is a fixed rendering rule, not a user setting.

### 6. Playback

- **Per Track**: Play, Pause, Stop.
  - Pause keeps the playhead position. Stop returns it to 0.
- **Play Stack** (shortcut **Shift+Space**): plays Tracks top to bottom in sequence, with a 0.5 s gap. The playhead moves through each Track in turn.
- **Space**: play/pause the Selected Track.
- **Esc**: stop playback (or recording).
- A moving playhead line shows the current position.
- **Click** on a Spectrogram moves the playhead there.
- **Drag** across a Spectrogram selects a **Region**. Play then plays only the Region.

### 7. Measurement readout

- Hovering over a Spectrogram shows time (s), frequency (Hz), and level (dB) at the cursor.
- A vertical crosshair line at the cursor's time is drawn across all Tracks.

### 8. Files

- **Import** WAV or MP3 into a new Track. Default name: the file name without extension.
- **Export Track** as WAV.
- **Export Stack** as PNG (for lab reports), including axes and Track names.
- No automatic saving. If the Stack holds any Tracks, the browser warns before the page closes or reloads.

---

## Phase 2

### 9. Modulation

See [ADR 0001](adr/0001-praat-style-offline-modulation.md).

- A modulation panel acts on the Selected Track.
- Two sliders:

| Slider | Range | Default | Normal-speech zone (marked) |
|---|---|---|---|
| **F0 Shift** | −2400 to +2400 cents | 0 | ±1200 cents |
| **Formant Dispersion Scale** | 0.25× to 4× | 1.0× | 0.8× to 1.25× |

- Formant Dispersion Scale multiplies every formant frequency by k (uniform scaling; equivalent to a vocal tract k times shorter).
- Method: Praat "Change gender" style. Resample by k to scale formants, then PSOLA to set F0 independently.
- Moving sliders creates nothing.
  - **Preview** plays the modulated audio without adding a Track.
  - **Apply** inserts a **Derived Track** directly below its source Track, auto-named e.g. `vowel a (F0 +400¢, ×1.15)`. The name is editable like any Track.
- The source Track is never changed.
- Selecting a Derived Track loads its slider values. Any new modulation is computed from the **original** Recording, never from the Derived Track's audio. Artifacts do not compound.
- Deleting a source Track keeps its Derived Tracks. The original Recording stays in memory (hidden) while any Derived Track refers to it.
- Expect audible artifacts near the slider extremes. This is acceptable and can be used as a teaching point.

### 10. F0 contour overlay

- Optional toggle that draws the measured F0 contour on each Spectrogram.
- Uses the same pitch tracker that PSOLA requires.

### 11. Save / load Stack

- Save the whole Stack (Recordings, names, order, Stack settings, modulation parameters) to a single file, and load it back.
- File format to be decided at phase 2 start.

---

## Testing

Automated tests cover parts that can be checked numerically:

- **FFT / Spectrogram**: a pure tone produces a peak at the correct frequency bin. Wideband vs Narrowband resolve (or blur) two close tones as expected.
- **Frequency scales**: Hz ↔ Log / Bark / Mel conversions round-trip and match reference values.
- **Modulation (phase 2)**: a 200 Hz tone (or synthetic vowel) shifted +1200 cents measures 400 Hz. Formant Dispersion Scale of k moves a synthetic vowel's spectral peaks by factor k.
- **Time axis**: Fit to signal produces the longest Track's duration.

UI behavior is checked manually in Chrome and Edge, including a projector-size check.

## Out of scope

- **Live voice modulator** (real-time processing of mic input). Dropped: needs headphones, adds latency, needs a real-time version of the modulation method.
- **Automatic formant tracks** (LPC). Unreliable enough to mislead students.
- **Per-Track overrides** of any Stack setting.
- **Phone layout.**
