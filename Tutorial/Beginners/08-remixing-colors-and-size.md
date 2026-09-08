# 08 — Remixing colors and size

Enough poking at defaults — let's make it look like ours. Everything here is token overrides after `siri-tokens()`. Library untouched.

## Repaint the whole wave

```fscss
@siri-tokens()

:root {
  --siri-wave-container-bg: #0a0a12;
  --blob-magenta-bg: #ff2ecb;
  --blob-cyan-bg: #22d3ee;
  --blob-green-bg: #4ade80;
  --blob-purple-bg: #a78bfa;
  --blob-orange-bg: #fb923c;
  --blob-blue-bg: #38bdf8;
}
```

That's a branded, brighter palette in six lines. `:root` sits after `siri-tokens()`, so the overrides beat the defaults (same cascade that always wins — later + same specificity).

## Size it up

```fscss
:root {
  --siri-wave-container-width: 720px;
  --siri-wave-container-height: 240px;
  --blob-green-width: 120px;
  --blob-green-height: 90px;
}
```

Big container, one dominant blob. For a hero section, `--siri-wave-container-max-width` is your cap:

```fscss
:root {
  --siri-wave-container-max-width: 900px;
}
```

## Adjust the glow

The blobs' softness is the `--blob-filter`:

```fscss
:root {
  --blob-filter: blur(18px);  /* dreamier */
  --blob-opacity: 1;          /* punchier */
}
```

And the line's presence:

```fscss
:root {
  --siri-wave-line-bg: rgba(255, 255, 255, 0.32);
  --siri-wave-line-height: 2px;
  --siri-wave-line-shadow: 0 0 10px rgba(255, 255, 255, 0.5);
}
```

## Change the spread

Blobs are positioned by `--blob-<color>-left` percentages. Pull them into a tighter band or push them wide:

```fscss
:root {
  --blob-magenta-left: 30%;
  --blob-cyan-left: 36%;
  --blob-green-left: 42%;
  --blob-purple-left: 48%;
  --blob-orange-left: 54%;
  --blob-blue-left: 60%;
}
```

Even spacing across the middle. The wave now reads as a tight cluster rather than a spread.

## A shortcut for repeating overrides

Six lefts is verbatim, but you'll notice the pattern — and patterns are where you reach for variables instead:

```fscss
$blobStart: 42%;

:root {
  --blob-magenta-left: $blobStart!;
  --blob-cyan-left: $blobStart!;
  --blob-green-left: $blobStart!;
  /* ... */
}
```

`$blobStart!` inlines the value at compile time — that's FSCSS's `$` variable, and `!` says "evaluate here." Real arithmetic (offsets like `42% - 6%`) goes through `num()`, which we'll properly meet in Advanced. For now, know `$` exists so six-numbers-by-hand isn't your only tool.

## Actually: keep a copy of the defaults

Before you go wild, copy the default token table from lessons 05–06 into a comment in your stylesheet. When an override makes something look insane, you'll want the one-line revert without re-reading this repo.

## Check your understanding

1. Where must `:root { }` sit relative to `siri-tokens()` and why?
2. Which two tokens would you touch to make blobs leak less light onto each other?

Answer 1: after — your values must beat the defaults. Answer 2: reduce `--blob-opacity` and lower `--blob-filter` blur.

Next: [09 — Many waves, one page](09-many-waves-one-page.md).