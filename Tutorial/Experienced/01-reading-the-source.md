# 01 — Reading the source

Time to read `siri-wave.fscss` the way a maintainer does: top to bottom, no skipping, getting annoyed at the author where deserved. The file is ~130 lines. In one sitting.

Open the real file from this repo. I'll walk the whole thing.

## Pass 1 — the header

```fscss
/* siri-wave.fscss */
```

That's the entire copyright-free header. For a one-file lib that's fine; you're not obliged to document a file whose whole job is documented by its own tutorial course.

## Pass 2 — `siri-tokens`

```fscss
@define siri-tokens(root:root){`
  :@use(root){
    /* Container */
    --siri-wave-container-width: 100%;
    --siri-wave-container-max-width: 560px;
    --siri-wave-container-height: 160px;
    --siri-wave-container-bg: #000;
    --siri-wave-container-radius: 28px;
    /* Baseline line */
    --siri-wave-line-width: 88%;
    --siri-wave-line-height: 1px;
    --siri-wave-line-bg: rgba(255, 255, 255, 0.18);
    --siri-wave-line-shadow: 0 0 6px rgba(255, 255, 255, 0.35);
    --siri-wave-line-z: 1;
    /* Shared blob */
    --blob-radius: 50%;
    --blob-filter: blur(10px);
    --blob-blend: screen;
    --blob-opacity: 0.9;
    --blob-anim: siri-pulse 2.2s infinite ease-in-out alternate;
    --blob-will-change: transform, opacity;
    /* Individual blobs */
    --blob-magenta-width: 78px;  --blob-magenta-height: 42px; ...
  }
  @keyframes siri-pulse { ... }
`}
```

Things to actually *notice*:

- The body is a **backtick template string**. Defines that emit whole structures use it; property defines don't. Both work; the author chose ergonomics over uniform.
- Comment groups mirror the token namespaces — the file self-documents at four group headers.
- Six colors × six keys = 36 declarations, aligned but repetitive. The repetition is *data* (a knob list), and the author chose readability over generation here, unlike the blobs themselves. The file has a philosophy: *generate shapes, hand-write knobs*.

## Pass 3 — the shape mixins

```fscss
@define siri-base(st:.siri-wave){`
  @use(st), @use(st) *{
    margin: 0; padding: 0; box-sizing: border-box;
  }
`}
```

Two selectors, one reset. Note `@use(st) *` — it hits the wave and *everything inside*. That's an opinion (a scoped reset), and it's the only reason a nested component could get its box-sizing stomped — good to know for Advanced readers.

```fscss
@define siri-wave(st:.siri-wave){`
  @use(st){
    position: relative;
    width: var(--siri-wave-container-width, 100%);
    ...
    display: flex; align-items: center; justify-content: center;
    overflow: hidden;
    border-radius: var(--siri-wave-container-radius, 24px);
  }
`}
```

Notice the fallback `24px` here while the token default is `28px`. *The library ships with drift between its own fallbacks.* Harmless (one wins only if tokens never run), but it's the kind of inconsistency you'd file an issue about — and now you *can*, because you read the file.

## Pass 4 — `siri-blob-colors`, the heart

```fscss
@arr siri-colors[magenta, cyan, green, purple, orange, blue]
@arr siri-colors-i[count(6,1)]

@siri-blob-variant(@use(st).@arr.siri-colors[@arr.siri-colors-i[]], @arr.siri-colors[@arr.siri-colors-i[]])

@use(st){
  --siri-loop-index: @arr.siri-colors-i[];
}
```

Twenty lines of source produce six full variant rules. Watch the *selector synthesis* and the *token-name synthesis* meet: the same name list feeds both the `.blob.<name>` class and the `--blob-<name>-*` lookups. The decoy property is the loop driver (Inter/02). This is the one passage worth re-reading until it bores you — it's the library's entire identity.

## After the read

You should be able to answer, without opening the file:

1. What token does the container use for its glow? (Answer: there is none — the container is a dumb box; the glow lives on line + blobs.)
2. How many variable declarations are per-color? (Six.)
3. What happens if `siri-tokens()` never runs? (Fallbacks everywhere; blobs at generic sizes; no keyframes so no motion.)

If you got all three, you've stopped *using* siri-wave and started *knowing* it. Next: [02 — Write your own keyframes](02-write-your-own-keyframes.md).