# 06 — Root overrides, done right

By now you've seen `:root { --token: value }` a dozen times. This lesson is about the rules that keep override blocks from becoming a mess.

## The cascade law that makes it work

siri-wave's mixins read `var(--token, fallback)`. Your overrides on `:root` (or anywhere) beat the fallback as long as they're *set*. It's the same cascade CSS always had — the library just offloaded every decision onto it.

Three facts to internalize:

1. **A set variable beats a fallback.** `var(--x, 5px)` uses the fallback only when `--x` isn't defined where the rule evaluates (or is `initial`).
2. **Closer scope wins.** A variable on `.wave` overrides the same variable on `:root`, for that wave and its children.
3. **`initial` is the reset switch.** Set `--blob-green-height: initial` and green's rule falls back to its default — useful for "un-override" without deleting the line.

## The keyframe gotcha

`--blob-anim` names the animation: `siri-pulse 2.2s infinite ease-in-out alternate`. Override it globally and every blob obeys. But remember from Beginners — the *per-color* token set has no `-anim` key, so `--blob-magenta-anim` is *dead code*: nothing reads it. Don't invent token keys the mixins never consume; you'll confuse a future reader into thinking they work.

## Organizing overrides

Order and grouping keep `:root` blocks sane:

```fscss
@siri-tokens()

/* 1. theme-level (colors, mood) */
:root {
  --siri-wave-container-bg: #0a0a12;
  --blob-blend: screen;
}

/* 2. layout-level (size/space) */
:root {
  --siri-wave-container-max-width: 760px;
}

/* 3. timing-level */
:root {
  --blob-anim: siri-pulse 2.4s infinite ease-in-out alternate;
}
```

Multiple `:root` blocks are legal and let you comment the *purpose* of each group rather than one giant wall. Your future self will thank you.

## Scoped overrides near the usage

The single best habit: **put per-wave overrides on the component, not in the global block.**

```fscss
.hero-wave {
  --blob-green-height: 110px;
  --siri-wave-container-height: 240px;
}
```

Now a reader glancing at `.hero-wave` sees exactly what makes it special. Globals hold the defaults; scoped blocks hold the deltas.

## When to edit the library vs override

- **Override** when you want a different *instance* or a different *look*.
- **Edit the mixin** when the library's *logic* is wrong for you (e.g. you want blob opacity to react to a value, which no token does). That's Advanced territory.

A good rule of thumb: if you edit the library file, you feel a little nervous. If you're overriding, you're playing in the open.

## Check your understanding

1. Why is `--blob-magenta-opacity: 1` (note: invented) a trap, but `--blob-opacity: 1` not?
2. You want a wave to ignore the global height token this one time. What's the clean move?

Answer 1: per-color sets don't include an opacity key; the invented token is read by nothing, while the shared one is read by every blob. Answer 2: set `--siri-wave-container-height: initial` (or a scoped value) on that wave's container class.

Next: [07 — Responsive and reduced motion](07-responsive-and-reduced-motion.md).