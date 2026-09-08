# 03 — blob-variant parameters

Now the smallest mixin, and the one with the most load-bearing idea: `siri-blob-variant`.

```fscss
@define siri-blob-variant(st, color){
  @use(st){
    width: var(--blob-@use(color)-width, 70px);
    height: var(--blob-@use(color)-height, 40px);
    background: var(--blob-@use(color)-bg, #fff);
    left: var(--blob-@use(color)-left, 50%);
    animation-delay: var(--blob-@use(color)-delay, 0s);
    animation-duration: var(--blob-@use(color)-duration, 2s);
  }
}
```

## Two parameters, different species

- `st` — a *selector*. Who we're styling.
- `color` — a *string*. Which color's token set we're reading.

They're different kinds of things, and the mixin treats them differently. `st` goes straight into `@use(st)`. `color` never appears as a class — it's spliced into a **variable name** at compile time.

## Token-name synthesis

Look at the pattern in every declaration:

```
var(--blob-@use(color)-width, 70px)
```

`@use(color)` is replaced at compile time by the argument. Call it with `magenta`:

```css
var(--blob-magenta-width, 70px)
```

with `cyan`:

```css
var(--blob-cyan-width, 70px)
```

The mixin doesn't read `--blob-magenta-width` by name because it never knows "magenta" is special. It builds the name *out of* whatever it's given. The token namespace is a convention the mixin follows — `--blob-` + name + `-width`. That's why the token block's regularity from lesson Inter/01 matters so much: **the mixin predicts token names, so the tokens must be predictable.**

## The fallbacks hide a second convention

`70px, 40px, #fff, 50%, 0s, 2s`. Those aren't "magenta" values — they're *any-blob* values. If you set `--blob-X-width` and all you did was pick name `X`, you get a 70px-wide, 40px-tall, white blob at 50%? No — wait, the fallbacks kick in when `--blob-X-*` is unset. siri-tokens sets them for the six known colors, so known colors get real values, and an *unknown* name gets the generic fallback. That's the safety net: your custom colors don't break the wave, they just look neutral until you token them.

## Using it directly (not just via the loop)

You'd rarely call `siri-blob-variant` by hand, but the pattern for a single extra blob is:

```fscss
@siri-tokens()

:root {
  --blob-pink-width: 66px;
  --blob-pink-height: 40px;
  --blob-pink-bg: #ff2ecb;
  --blob-pink-left: 82%;
  --blob-pink-delay: 0.4s;
  --blob-pink-duration: 2.0s;
}

@siri-blob-variant(.wave .blob.pink, pink)
```

Now add `<div class="blob pink"></div>` to your wave and it slots in, unique, without touching the loop or the color list.

## Name-synthesis is the library's spine

`siri-blob-variant` synthesizes `--blob-<color>-*`. That's one spine. The loop in `siri-blob-colors` synthesizes *selectors* out of the same names. Both spines meet on "the color name is the key": the name maps to a class, to a token set, and to fallbacks. Learn to think "name → key" and the whole file reads at once.

## Check your understanding

1. Why does `siri-blob-variant` take `color` as its second parameter rather than just reading six hardcoded tokens?
2. You call `@siri-blob-variant(.w .blob.pink, pink)` but never define `--blob-pink-*`. What renders?

Answer 1: one mixin serves *any* color name — the library's names are data, not code. Answer 2: the pink blob renders with generic fallbacks (70×40, white, centered) — demonstrable but unstyled.

Next: [04 — Timing and easing](04-timing-and-easing.md).