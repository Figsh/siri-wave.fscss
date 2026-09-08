# 01 — What is siri-wave?

Before you write a single line, let's get clear on what we're working with.

siri-wave.fscss is a small FSCSS library that builds that Apple-style pulsing wave you see when Siri is listening — a soft, glowing baseline line with several blurred color blobs pushing up and down around it. It looks alive, like somebody's talking. It's a loader, a visualizer, a status indicator. All of it is plain CSS once compiled.

## What it ships

One file, `siri-wave.fscss`, with eight mixins:

| Mixin | What it does |
|---|---|
| `siri-tokens()` | Writes all the design tokens (colors, sizes, timing) and the `siri-pulse` keyframes |
| `siri-base()` | A tiny scoped reset: margin, padding, border-box |
| `siri-wave()` | The outer container — rounded, centered, dark |
| `siri-line()` | The glowing baseline line under the blobs |
| `siri-blob()` | The shared blob look: blur, blend, opacity, animation |
| `siri-blob-variant()` | One blob sized/colored from a named token set |
| `siri-blob-colors()` | Generates all six default color variants |
| `siri-wave-preset()` | The one-call shortcut that composes everything above |

In practice you almost never call all of them. You call `siri-tokens()` once per page and then `siri-wave-preset()` under whatever class name your markup uses. The other six mixins are the machinery inside the preset — and the reason this tutorial exists is that you'll eventually read them, then rebuild them.

## The six blobs

You get six colored blobs out of the box:

```
magenta, cyan, green, purple, orange, blue
```

Each one has its own token set — width, height, background color, horizontal position, animation delay, and animation duration. They're generated in a loop, not hardcoded. That's important because it's the FSCSS pattern you'll reuse everywhere: define an array, loop over it, synthesize token names.

## Why you'd use it

- You want a genuinely good-looking wave and you don't want to hand-scribe keyframes
- You want the whole thing themable through CSS variables
- You want a loader that moves the same way on every browser because it's just CSS

## Why you wouldn't

- You need per-blob animated shapes beyond a pulse — you're fighting the library
- You want a waveform from actual audio data **without writing any JS**. CSS can't read microphones. (Spoiler for Advanced level: feed the data in with ~10 lines of JS and siri-wave becomes a live audio visualizer. The wave itself stays pure CSS.)

That last point is the whole philosophy here: siri-wave owns the *look and motion*, your code (if any) owns the *data*.

## Check your understanding

Look at the mixin table above. Which one call, run after `siri-tokens()`, puts a complete working wave on the page with zero extra wiring?

Answer: `siri-wave-preset()`. We'll use it in lesson 03. First, setup — see [02 — Setting up FSCSS](02-setting-up-fscss.md).