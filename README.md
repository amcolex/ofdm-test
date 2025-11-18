# OFDM Decoding Exercise

You are given a SigMF capture of a single OFDM frame. Recover the ASCII payload
that was transmitted.

## Files
- `*.sigmf-data`: interleaved IQ samples stored as little-endian 32-bit floats.
- `*.sigmf-meta`: SigMF metadata describing the capture.

## Signal Summary
- Sample rate: 30.72 Msps at a 2.4 GHz RF center.
- Single channel, complex baseband samples with float32 precision.
- Frame Structure is a 2-symbol OFDM frame:
  - Symbol 0: Block Pilot
  - Symbol 1: Payload Data
- The recording is a small time sequence which contains the OFDM frame somewhere within it.

## OFDM Parameters
- FFT size: 2048 points with a 512-sample cyclic prefix.
- Active carriers: 1200 centered around DC (indices -600 to -1 and +1 to +600).
- Block pilot: a CAZAC (Zadoff-Chu) symbol of length 1200 with root 25. We generate exactly 1200 complex samples and place them sequentially onto the active bins returned by the FFT mapping (lowest negative bin up through the highest positive bin, DC omitted), so you see the CAZAC tones directly on the usable carriers. Because the length is even, the pilot uses the closed-form sequence
  ```
  x[n] = exp(-jπ · 25 · n (n + 1) / 1200),  0 ≤ n < 1200.
  ```
- Payload: single symbol with QPSK data on every active carrier; the text is encoded in ASCII, MSB first, mapped to QPSK using Gray mapping `00→(+)+j(+), 01→(+)+j(-), 11→(-)+j(-), 10→(-)+j(+)`.
- Mapping order: start with the lowest negative-frequency carrier (−600) and progress upward, skipping DC.

## Impairments
- Realistic multipath channel derived from field measurements (lots of multipath!) and noise.
- Simulating 2 real radios which might have small oscillators differences..


## Payload Hint
It contains a raw ASCII string. Figure it out!

## Bonus
Would be nice if we could also see the CFR (Channel Frequency Response) and CIR (Channel Impulse Response) plots for the channel!

