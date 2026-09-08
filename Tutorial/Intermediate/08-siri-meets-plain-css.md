# 08 — siri meets plain CSS

Everything so far used the preset. But the preset is five mixins stacked — and you don't always want all five. This is the lesson that turns you from a consumer into a composer.

## The composition, split out

From the README:

```fscss
@siri-tokens()
@siri-base(.my-wave)
@siri-wave(.my-wave)
@siri-line(.my-wave .siri-line)
@siri-blob(.my-wave .blob)
@siri-blob-colors(.my-wave .blob)
```

That's exactly what `siri-wave-preset(.my-wave)` expands to. Nothing more. So "without the preset" just means calling the pieces and adding your own CSS between them.

## Use case: no base reset, I have my own

`siri-base` blasts `margin:0; padding:0; box-sizing:border-box` onto your selector and its children. On a page with a design system that already normalizes box-sizing, that reset is redundant meddling. Drop it:

```fscss
@siri-tokens()
@siri-wave(.my-wave)
@siri-line(.my-wave .siri-line)
@siri-blob(.my-wave .blob)
@siri-blob-colors(.my-wave .blob)
```

Same wave, minus the reset. Your own resets stay untouched.

## Use case: plain CSS decorating the wave

Because the mixins emit normal rules, you can (and should) dress the wave with ordinary CSS in the same block:

```fscss
@siri-wave(.my-wave)

.my-wave {
  margin: 24px auto;
  border: 1px solid rgba(255,255,255,0.08);
  box-shadow: 0 20px 60px rgba(0,0,0,0.5);
}
```

Mixins write the core; your rules add margin, borders, elevation. No conflicts — distinct properties, and even overlapping ones are just later-in-cascade.

## Use case: a wave that isn't a full pill

Run just the inner pieces over your own box. If you want the blobs *without* the branded container (e.g. sitting on a page background):

```fscss
.siri-wave-on-page {
  position: relative;
  height: 160px;
}

@siri-line(.siri-wave-on-page .siri-line)
@siri-blob(.siri-wave-on-page .blob)
@siri-blob-colors(.siri-wave-on-page .blob)
```

You've built the wave's heart inside a class you own entirely. The tokens still came from `siri-tokens()`; the styling contract is yours.

## The selector-parameter discipline

Notice every mixin took an explicit selector. That's non-negotiable when composing manually: give each piece the *same* anchor or parts will target different elements. The preset just centralizes that decision; you're now making it yourself.

## Mix-and-match rules

- Always run `siri-tokens()` first (keyframes + globals).
- `siri-base` last if you include it, so your own resets can win — or skip it.
- Keep `siri-blob` before `siri-blob-colors` so the color variants layer on top of the shared shape.

## Check your understanding

1. What does `siri-wave-preset(.x)` *actually* call, in order?
2. You skipped `siri-blob` but kept `siri-blob-colors`. What breaks?

Answer 1: base, wave, line, blob, blob-colors — five calls, same selector chain. Answer 2: color variants set sizes/positions, but the *blob-ness* (radius, filter, blend, animation) never applies — you get square, sharp, unmoving color splats.

Next: [09 — Composing with other FSCSS](09-composing-with-other-fscss.md).