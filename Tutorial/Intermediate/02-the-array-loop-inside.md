# 02 — The array loop, inside

The most interesting thing in the library is `siri-blob-colors`, because it's where siri-wave stops being "six written-out rules" and becomes "six rules generated from a list." Here's the whole mixin:

```fscss
@define siri-blob-colors(st:.blob){
  @arr siri-colors[magenta, cyan, green, purple, orange, blue]
  @arr siri-colors-i[count(6,1)]

  @siri-blob-variant(
    @use(st).@arr.siri-colors[@arr.siri-colors-i[]],
    @arr.siri-colors[@arr.siri-colors-i[]]
  )

  @use(st){
    /* Need a block to trigger the loop */
    --siri-loop-index: @arr.siri-colors-i[];
  }
}
```

Let's pull that apart like a dead animal — carefully.

## The two arrays

```fscss
@arr siri-colors[magenta, cyan, green, purple, orange, blue]
```

A literal list of names. This is the *data* — the six colors the library knows.

```fscss
@arr siri-colors-i[count(6,1)]
```

An **auto-indexed** list: `count(6,1)` produces `[1, 2, 3, 4, 5, 6]`. So `siri-colors-i` is "the loop counter, from 1 to 6."

Two lists, same length. That's the trick: a *names* list and a *numbers* list that line up by position.

## The loop

```fscss
@siri-blob-variant(
  @use(st).@arr.siri-colors[@arr.siri-colors-i[]],
  @arr.siri-colors[@arr.siri-colors-i[]]
)
```

Read `@arr.siri-colors[@arr.siri-colors-i[]]` out loud, left to right: *the `siri-colors` element at index returned by `siri-colors-i`*. As `siri-colors-i` walks 1, 2, 3…, the lookup walks magenta, cyan, green… One `@siri-blob-variant` call per position.

The selector is built the same way:

- position 1 → `@use(st)` + `.` + `siri-colors[1]` → `.blob.magenta`
- position 2 → `.blob.cyan`
- …position 6 → `.blob.blue`

So the loop emits (this is the compiled output in disguise):

```css
.blob.magenta { width: var(--blob-magenta-width, 70px); ... }
.blob.cyan    { width: var(--blob-cyan-width, 70px);    ... }
/* ... */
.blob.blue    { width: var(--blob-blue-width, 70px);    ... }
```

Six rules, one source of truth for color names.

## The decoy

```fscss
@use(st){
  /* Need a block to trigger the loop */
  --siri-loop-index: @arr.siri-colors-i[];
}
```

`@use(st)` runs a block, and FSCSS expands array lookups per position inside a block — that's what drives the loop forward. The property it writes (`--siri-loop-index`) is basically a stage light; nothing reads the value. The *block* is the loop engine, the property is just there to force the iteration to happen. It ships in the output (harmless, tiny), but the real output is the six color rules above.

## What this teaches you about FSCSS

- Arrays + index arrays = loops. No `for`, no `each`; you get used to it.
- You loop *selector-bearing* calls, not raw declarations.
- If the loop looks weird, remember: names list × counter, aligned by position, splices `[index]`.

## Try it: prove the generated CSS

Run any page using `@siri-wave-preset()`, open DevTools, and search for `.blob.magenta`. You'll see a rule whose declarations read `var(--blob-magenta-...)` with fallbacks. That rule was written by the loop, two mixins deep, from a four-element list you could shrink to two and break everything beautifully.

## Check your understanding

1. What do you change to make the wave use four blobs instead of six?
2. The `--siri-loop-index` thrown-away property — why not delete that block line?

Answer 1: change the `siri-colors` array (and drop two colors) or edit `count(6,1)` — but the color tokens in `siri-tokens()` must match, or you get fallbacks. Answer 2: the block *is* the driver; removing the property removes the block's reason to expand, and the loop stops firing.

Next: [03 — blob-variant parameters](03-blob-variant-params.md).