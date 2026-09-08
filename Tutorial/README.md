# siri-wave training — 40 tutorials

A hands-on course for **siri-wave.fscss**, the Siri-style pulsing blob wave built entirely from FSCSS mixins. Four levels, ten lessons each, one final project with three real-world builds at the end.

You don't need any FSCSS background. Lesson 1 of Beginners starts from "what is a `.fscss` file" and the course walks you all the way up to shipping your own wave library.

## The four levels

| Level | Folders | What you end up able to do |
|---|---|---|
| Beginners | `Beginners/01` — `Beginners/10` | Get siri-wave on a page, understand the markup, restyle colors, size, and run several waves. |
| Intermediate | `Intermediate/01` — `Intermediate/10` | Understand the token system, the array loop, timing, accessibility, and combine waves with your own CSS. |
| Advanced | `Advanced/01` — `Advanced/10` | Rebuild the library yourself, write custom mixins/presets, do JS-driven live audio waves. |
| Experienced | `Experienced/01` — `Experienced/10` | Read the raw source fluently, write a wave library from scratch, publish it, ship production features. |

## Final project

[`final-project.md`](final-project.md) gives you three tough, real-world builds. No sandbox fluff — these are things people actually ship:

1. **A now-playing screen for a music app** where the wave reacts to the real track.
2. **A voice-assistant status panel** with idle / listening / thinking / speaking states.
3. **A live ops monitor** where the wave shows real server metrics.

Rules: the wave *must* be siri-wave. No other wave library, no canvas, no SVG animation. The JS you add can feed it data — the visuals stay siri.

## What siri-wave.fscss actually is

- **Pure CSS.** The compiled output is plain CSS. No JS is needed to animate it.
- **FSCSS mixins.** Eight `@define` blocks: `siri-tokens`, `siri-base`, `siri-wave`, `siri-line`, `siri-blob`, `siri-blob-variant`, `siri-blob-colors`, `siri-wave-preset`.
- **Design tokens.** Every color, size, and timing value lives in `--siri-*` and `--blob-*` custom properties. Override the token, restyle the wave.
- **Array-generated.** Six blobs (magenta, cyan, green, purple, orange, blue) are produced by a `count(6,1)` loop — not six hand-written blocks.

You should have the real `siri-wave.fscss` open next to you while you read. It's at the root of this repo, and you can also pull it from the FSCSS library registry with `@import((*) from siri-wave)`.

## A note on style

These tutorials are written the way we actually work — short, direct, occasionally opinionated. If a lesson says "do it this way," it's because that way has survived real use. When you find a better way, that's the real lesson working.

## References we use along the way

- FSCSS docs: https://fscss.devtem.org/docs
- Import guide: https://fscss.devtem.org/import
- FSCSS on npm: https://www.npmjs.com/package/fscss
- The FSCSS repo: https://github.com/fscss-ttr/FSCSS

Start at [Beginners/01 — what is siri-wave](Beginners/01-what-is-siri-wave.md).