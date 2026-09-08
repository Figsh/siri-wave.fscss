# 04 — Timing and easing

The wave's whole personality is timing. Let's get precise about the five knobs you have per blob and how they make six circles feel like a ripple.

## The knobs

Each blob token set carries:

- `--blob-<color>-delay` — when the blob's animation starts
- `--blob-<color>-duration` — how long one cycle takes

And globally:

- `--blob-anim` — the animation shorthand: name, duration, timing, iteration, direction

## Delay: stagger, not pause

The defaults:

```
magenta 0s,  cyan 0.25s, green 0.45s, purple 0.15s, orange 0.35s, blue 0.55s
```

Those delays mean at t=0, magenta is already mid-cycle while blue hasn't finished starting. Every blob's phase differs, so the eye sees a traveling pulse, not a group exercise.

**Positive delays** shift a blob later in time. On an *infinite* loop that's just a phase offset — no actual waiting, the cycle just starts at a different spot.

## Duration: speed per blob

Duration changes how fast a blob cycles:

```
magenta 1.9s  cyan 2.1s  green 2.3s  purple 2.0s  orange 1.8s  blue 2.4s
```

Slightly different speeds mean the phase relationship *drifts*. Blob pairs that line up at one moment slowly fall out of sync. That drift is what kills the "six metronomes" effect the lockstep experiment showed in Beginners. st: waves need drift.

## Easing and direction

From `--blob-anim`:

```
siri-pulse 2.2s infinite ease-in-out alternate
```

- `ease-in-out` — slow start, fast middle, slow end. Soft.
- `alternate` — play 0→100, then reverse back to 0, forever. The down-swing is the animation playing *backwards*, which looks like breathing instead of a jump-cut reset.

`linear` would make the pulse robotic (try `--blob-anim: siri-pulse 2.2s infinite linear alternate` — feel the difference). `alternate` + `ease-in-out` is a wave's natural rhythm.

## The negative-delay trick

Positive delays phase-shift forward in time. Negative delays phase-shift *backwards* — the blob behaves as if it's been running for that long. On infinite loops, negative delays let you pre-stagger everything so no blob is ever at rest on load:

```fscss
:root {
  --blob-blue-delay: -1.2s;   /* blue acts as if it started 1.2s ago */
}
```

Which one feels right (positive reordering vs. negative pre-tuning) is taste. Both are legitimate; the defaults use small positives, which is as good as any.

## Finding a rhythm you like

A practical process when tuning:

1. Set all durations equal (e.g. `2s`) so the wave is coherent.
2. Set delays in even steps (`0, .33, .66`) for a runner's-wave.
3. Break exact rhythm with one or two slight offsets.
4. Then vary durations ±0.3s around your base.

Step 4 is what siri-wave already ships. You're learning to *taste* it, not rewrite it.

## Check your understanding

1. Difference between `-1.2s` and `1.2s` delay on an infinite loop?
2. Why does the animation get `alternate` rather than a 0%→100%→0% keyframe set?

Answer 1: sign is which direction in time the phase shifts; on an infinite loop it's pre-tuning vs postponing. Answer 2: alternate reverses any keyframes for free — writing a mirrored 200% keyframe set would be double the code for the same motion.

Next: [05 — Layering and overlays](05-layering-and-overlays.md).