# 01 — Token deep-dive

Beginners trusted the tokens. Now we read the token mixin itself, top to bottom, and understand *why* it's shaped this way.

Here's `siri-tokens()` with the distracting bits trimmed:

```fscss
@define siri-tokens(root:root){
  @use(root){

    /* container */
    --siri-wave-container-width: 100%;
    --siri-wave-container-max-width: 560px;
    --siri-wave-container-height: 160px;
    --siri-wave-container-bg: #000;
    --siri-wave-container-radius: 28px;

    /* line */
    --siri-wave-line-width: 88%;
    --siri-wave-line-height: 1px;
    --siri-wave-line-bg: rgba(255, 255, 255, 0.18);
    --siri-wave-line-shadow: 0 0 6px rgba(255, 255, 255, 0.35);
    --siri-wave-line-z: 1;

    /* shared blob */
    --blob-radius: 50%;
    --blob-filter: blur(10px);
    --blob-blend: screen;
    --blob-opacity: 0.9;
    --blob-anim: siri-pulse 2.2s infinite ease-in-out alternate;
    --blob-will-change: transform, opacity;

    /* per-color blobs */
    --blob-magenta-width: 78px;  --blob-magenta-height: 42px;
    --blob-magenta-bg: #c42bff;  --blob-magenta-left: 22%;
    --blob-magenta-delay: 0s;    --blob-magenta-duration: 1.9s;
    /* ...cyan, green, purple, orange, blue—same six keys each */
  }

  @keyframes siri-pulse { ... }
}
```

## What the shape tells you

**One mixin dumps two kinds of things: values and a keyframe block.** The keyframes don't belong to CSS custom properties — `@keyframes` isn't a variable. So siri-tokens bundles the two things every wave needs globally: the knobs *and* the motion definition. Call it once, both land.

**The namespace split is the design.** `--siri-wave-*` = the shell. `--blob-*` = the contents. When you define your own overlay components later, follow the same split: your module's outer box gets `--<mod>-*`, inner pieces get their own prefix. It makes grep the documentation.

**Every value has a matching fallback in the mixins.** `--siri-wave-container-width: 100%` in tokens, `width: var(--siri-wave-container-width, 100%)` in `siri-wave`. Two copies of every default. That's deliberate: the mixin works even if you never call `siri-tokens()` (falls back), and the token call works even if a future library version renames a mixin. Duplication buys resilience.

## The per-color block is the subtle part

Six colors × six keys each = 36 declarations, hand-typed, eye-searing. Notice how regular they are:

```
--blob-<name>-width   --blob-<name>-height   --blob-<name>-bg
--blob-<name>-left    --blob-<name>-delay    --blob-<name>-duration
```

Perfectly flat, predictable, machine-readable shape. Why write them by hand rather than generate them here like `siri-blob-colors` generates blobs? Because hand-typed tokens you can read in one go, and because FSCSS arrays generate *selectors* better than they generate declaration lists. The library author chose readability of the knob list over cleverness. Good call.

## When you'd edit this file

You almost never edit `siri-tokens()` in place — you override after the fact. But in Advanced you'll *rewrite* it to add a seventh blob, or rename the palette, or split timing out of the color tokens. When you do, keep the six-key-per-color regularity, or your custom `siri-blob-colors` loop will have no pattern to feed on.

## The shape matters more than the names

If someone renames every `--siri-wave-*` token to `--mywave-*` but keeps the structure (scope, fallbacks, per-color regularity), the library still behaves identically. The structure *is* the contract, not the letters in the names.

## Check your understanding

1. Why does `siri-tokens()` ship `@keyframes` inside a mixin that otherwise only writes CSS variables?
2. What breaks if you add `--blob-magenta-margin: 4px` in the token block but no mixin reads it?

Answer 1: keyframes can't be stored as a custom property, so they ride along with the one thing every wave needs. Answer 2: nothing breaks — the token is ignored. But now your "knobs" file documents a knob nothing turns, which is how docs rot.

Next: [02 — The array loop, inside](02-the-array-loop-inside.md).