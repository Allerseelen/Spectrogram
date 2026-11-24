# Offline, Praat-style modulation (resample + PSOLA)

F0 Shift and Formant Dispersion Scale use the same method as Praat's "Change gender" command: resample the Recording by k to scale formants, then use PSOLA to set F0 independently. We chose this over the WORLD vocoder because students can reproduce the results in Praat, the method is already taught in speech science, and it needs no WebAssembly dependency. The cost is more artifacts at the extreme ends of the sliders (±2400 cents, 0.25× and 4×), which we accept as a teaching point.

## Considered Options

- **WORLD vocoder**: cleaner at the extremes, but a black box to students and a heavy WebAssembly dependency.
- **Live voice modulator** (real-time mic processing): dropped from scope. It needs headphones to avoid feedback, adds 20–50 ms of latency, and needs a real-time version of the method above.

## Consequences

- Modulation is offline only: Preview plays without adding a Track, and Apply creates a Derived Track.
- A Derived Track is always recomputed from the original Recording, never from another Derived Track, so artifacts never stack up. The original Recording must stay available while any Derived Track refers to it.
