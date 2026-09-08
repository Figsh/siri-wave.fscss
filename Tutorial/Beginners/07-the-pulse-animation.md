# 07 — The pulse animation

Everything we've styled so far sits still until `siri-pulse` runs. Let's read the keyframes like you'd read a recipe.

```css
@keyframes siri-pulse {
  0%   { transform: scaleY(0.25) scaleX(0.75) translateX(-8px); opacity: 0.35; }
  35%  { transform: scaleY(1.35) scaleX(1.15) translateX(4px);  opacity: 0.95; }
  70%  { transform: scaleY(0.7)  scaleX(0.95) translateX(10px); opacity: 0.65; }
  100% { transform: scaleY(0.45) scaleX(0.85) translateX(-4px); opacity: 0.5;  }
}
```

## Reading it frame by frame

- **0%** — squashed to a quarter of its height, three-quarters wide, nudged left. Becomes nearly transparent.
- **35%** — puffed up to 1.35× height and 1.15× wide, bright and opaque. This is the *peak of the pulse*.
- **70%** — mid-tier: 0.7 tall, shifted right.
- **100%** — settles back toward squashed, dimmed, drifting left again.

That's the Siri illusion in motion: a blob standing up tall sheen, then relaxing back down. Nothing jumps — every step transforms the same properties, so the browser interpolates cleanly.

## The animation value

Remember the shared token:

```
--blob-anim: siri-pulse 2.2s infinite ease-in-out alternate;
```

Bite it apart:

- `siri-pulse` — name of the keyframes
- `2.2s` — one cycle
- `infinite` — never stops
- `ease-in-out` — soft at both ends, no snapping
- `alternate` — plays forward, then *reversed*. This is a big one. Without it the blob would smash back from peak to squash instantly; with it, the down-motion is smooth. A scale goes up, comes back, goes up again.

Keyframes at 0→100 define one *forward* animation. `alternate` doubles that into a relaxed pendulum.

## Why per-color durations exist

Each blob runs the same keyframes but at its own speed and phase:

```fscss
--blob-magenta-duration: 1.9s;
--blob-cyan-duration: 2.1s;
--blob-green-duration: 2.3s;
```

Different durations + different delays = blobs never line up. When they do almost-line-up it reads as a wave passing through them. That emergent effect is the whole point — nobody scripted a "wave", the timing just makes it appear.

## Try it: feel the difference

Make every blob identical in timing:

```fscss
:root {
  --blob-magenta-duration: 2.2s;
  --blob-cyan-duration: 2.2s;
  --blob-green-duration: 2.2s;
  --blob-purple-duration: 2.2s;
  --blob-orange-duration: 2.2s;
  --blob-blue-duration: 2.2s;
  --blob-magenta-delay: 0s;
  --blob-cyan-delay: 0s;
  --blob-green-delay: 0s;
  --blob-purple-delay: 0s;
  --blob-orange-delay: 0s;
  --blob-blue-delay: 0s;
}
```

Suddenly everything pulses in lockstep — stiff, robotic, unmistakably "generated." Revert it and the wave breathes again. Remember that lockstep feeling; nuance is a feature.

## Try it: change the motion entirely

Swap the whole animation on one blob:

```fscss
:root {
  --blob-magenta-anim: siri-pulse 0.8s infinite ease-in-out alternate;
}
```

That overrides `--blob-anim`? No — careful. `--blob-anim` is the *shared* token; the per-blob token is `--blob-magenta-...` with no `-anim` key. The per-color set only has width/height/bg/left/delay/duration, so you can't set a per-blob animation name from a token. If you want per-blob keyframes, you'll write a custom variant — that's an Advanced lesson. For now, global `--blob-anim` replacement:

```fscss
:root {
  --blob-anim: siri-pulse 0.8s infinite ease-in-out alternate;
}
```

Entire wave speeds up. You've now fingered every timing knob the library exposes.

## Check your understanding

1. What does `alternate` buy us that `infinite` alone doesn't?
2. Why do blobs ignore a `--blob-magenta-anim` token you invent?

Answer 1: a smooth up-down cycle instead of a hard jump back to 0%. Answer 2: the color-token assembly only reads the six keys it was built to read; invented keys never get looked up.

Next: [08 — Remixing colors and size](08-remixing-colors-and-size.md).