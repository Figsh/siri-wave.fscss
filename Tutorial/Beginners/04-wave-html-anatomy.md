# 04 — Wave HTML anatomy

Your wave is seven `<div>`s. Each one has a specific job, and the FSCSS rules key off their classes. Learn the map and you'll stop guessing why a wave looks wrong.

```html
<div class="siri-wave">                    <!-- 1. container -->
    <div class="siri-line"></div>          <!-- 2. baseline line -->
    <div class="blob magenta"></div>       <!-- 3-8. blobs -->
    <div class="blob cyan"></div>
    <div class="blob green"></div>
    <div class="blob purple"></div>
    <div class="blob orange"></div>
    <div class="blob blue"></div>
</div>
```

## The container

`div.siri-wave` is the box everything lives in.

```fscss
@siri-wave(.siri-wave) {
  position: relative;
  width: var(--siri-wave-container-width, 100%);
  max-width: var(--siri-wave-container-max-width, 560px);
  height: var(--siri-wave-container-height, 160px);
  background: var(--siri-wave-container-bg, #000);
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
  border-radius: var(--siri-wave-container-radius, 28px);
}
```

Read it top to bottom: it's a rounded, centered, `position: relative` box. `overflow: hidden` means anything that would poke out — a tall blob, a stray shadow — gets clipped to the rounded corners. Every value is a CSS variable with a fallback, so nothing here is fixed.

## The baseline line

```fscss
@siri-line(.siri-wave .siri-line) {
  position: absolute;
  width: var(--siri-wave-line-width, 88%);
  height: var(--siri-wave-line-height, 1px);
  background: var(--siri-wave-line-bg, rgba(255,255,255,0.18));
  box-shadow: var(--siri-wave-line-shadow, 0 0 6px rgba(255,255,255,0.35));
  z-index: var(--siri-wave-line-z, 1);
}
```

Absolute, 1px tall, faint white, with a soft glow. Note the `z-index: 1` — the line sits *over* the blobs, which is what sells the "blobs swim up from a line" illusion.

## The blobs

Each blob has two roles in its class list:

- `blob` — the shared shape. Grabs radius, filter, blend mode, opacity, and the animation.
- a color name — `magenta`, `cyan`, etc. Grabs that color's width, height, background, position, delay, duration.

Both roles matter. A blob without `blob` won't animate as a blob. A blob without a color name sits in the middle at 70px, colourless until you give it a variant.

## What a second wave looks like

Copy the whole block under a new container class and the structure repeats:

```html
<div class="hero-wave">
    <div class="siri-line"></div>
    <div class="blob magenta"></div>
    <div class="blob cyan"></div>
    <div class="blob green"></div>
    <div class="blob purple"></div>
    <div class="blob orange"></div>
    <div class="blob blue"></div>
</div>
```

Because the preset is scoped to a class, plain, you keep the same seven-div skeleton and just change the container class. That hook is what the rest of this course leans on.

## Gotchas

- **Nesting waves inside waves.** Don't. A `.blob` inside another `.blob` container gets styled twice and looks bonkers.
- **The line must come first** in the container so the blobs paint over it (it also has the z-index anyway, but keep the source order clean).
- **Don't put text inside blob divs.** They're shape-only elements.

## Check your understanding

Why is `overflow: hidden` on the container more than cosmetic?

Answer: it's what keeps a scaled-up blob from breaking out of the rounded box — without it, tall blobs would spill past the border-radius. Control = predictable visuals.

Next: [05 — The token system](05-the-token-system.md).