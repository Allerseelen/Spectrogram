# Spectrogram Tool

A lightweight browser tool for a speech and hearing science course: record speech, view it as a spectrogram, stack utterances for comparison, play them back, and alter F0 and formant spacing to hear and see the effect.

## Language

### Audio and display

**Recording**:
One captured or derived audio signal, with a user-given name.
_Avoid_: Clip, sample, take, audio

**Spectrogram**:
The time-frequency image of a Recording. Never the audio itself.
_Avoid_: Using "spectrogram" to mean the Recording or the Track

**Track**:
One row in the Stack, showing the Spectrogram of exactly one Recording.
_Avoid_: Row, lane, channel

**Stack**:
The vertical set of Tracks being compared. All Tracks in a Stack share one time axis and one set of analysis and display settings.
_Avoid_: Group, comparison, session

**Selected Track**:
The one Track that the modulation panel and keyboard shortcuts act on. There is at most one at a time.
_Avoid_: Active track, current track, focused track

**Time Span**:
The full length of the Stack's shared time axis: 10, 15, 30 or 60 s, or fitted to the longest Track. Also the longest a new Recording can run.
_Avoid_: Recording window, analysis window, window (alone)

**Region**:
A stretch of time selected on a Spectrogram so only that part plays.
_Avoid_: Selection, clip, segment

### Analysis

**FFT Window Length**:
The duration of each short slice of audio analyzed to make one column of a Spectrogram. Set Stack-wide by Wideband, Narrowband, or a custom value (1–100 ms).
_Avoid_: Window (alone), frame size

**Wideband Spectrogram**:
A Spectrogram made with a short FFT Window Length (default 4 ms), which shows formants and glottal pulses but blurs individual harmonics.
_Avoid_: Broadband (acceptable synonym in the literature, but use one term)

**Narrowband Spectrogram**:
A Spectrogram made with a long FFT Window Length (default 40 ms), which shows individual harmonics but blurs timing.

### Modulation

**Derived Track**:
A Track whose Recording was produced by modulating another Track's Recording. The source Track is never changed.
_Avoid_: Modified track, copy

**F0 Shift**:
A change in fundamental frequency, measured in cents relative to the source Recording.
_Avoid_: Pitch shift (pitch is the percept, F0 is the physical quantity)

**Formant Dispersion Scale**:
A factor k that multiplies every formant frequency, so the average spacing between adjacent formants also scales by k. Equivalent to a vocal tract k times shorter.
_Avoid_: Formant shift, vocal tract length (its inverse)
