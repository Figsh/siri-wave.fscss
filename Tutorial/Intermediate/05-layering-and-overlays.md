# 05 — Layering and overlays

A siri-wave is really a small painting: a background box, some glowing shapes on top. Understanding the layers is how you stop fighting blends.

## The natural order

Top to bottom in the paint order:

1. **The container background** (`--siri-wave-container-bg`) — the canvas.
2. **The blobs** — blurred, screen-blended colored ellipses.
3. **The line** — a 1px glowing rule with `z-index: 1`, which paints *above* the blobs.

Wait — the blobs are painted first? No. Look at the z-indexes: blobs have none (auto → paint by DOM order), the line has `z-index: 1`. So the blobs paint in order they appear in HTML, then the line paints above them. The divs are ordered line-first in the HTML, but z-index yanks the line on top. That order is why the wave reads as "blobs growing out of a line": the line is a surface, the blobs melt into it.

## Blur and blend are where the magic hides

```fscss
--blob-filter: blur(10px);
--blob-blend: screen;
```

- `blur(10px)` softens each blob into a diffuse patch.
- `screen` blends each blob against what's *behind it*. Screen is brightening: overlapping bright patches get brighter. Black background stays black.

Because the blobs overlap each other (their `left` positions bring them within touching range), overlapping patches brighten into translucent light spills. You can't easily draw that by hand — two rules of CSS give it to you.

## Controlling the spill

Too much glow soup?

```fscss
:root {
  --blob-filter: blur(6px);     /* tighter, sharper blobs */
  --blob-opacity: 1;            /* full-strength shapes */
}
```

Want the line to sit under the blobs' light instead of on top?

```fscss
:root {
  --siri-wave-line-z: 0;        /* line drops under the blobs */
}
```

Then the blobs visibly float over the line rather than sprouting from it. Both looks are valid; you're now steering the painting.

## Overlays you'll build yourself

The container is `overflow: hidden`, border-radius, position relative. That's everything you need to *layer your own UI on a wave*:

```css
.wave-deck {
  position: relative;
}
.wave-deck .badge {
  position: absolute;
  top: 12px;
  right: 12px;
}
```

Your badge paints above the wave because it comes later in DOM order and is positioned. If the wave's container has the radius and overflow hidden, the badge inside inherits the clip — neat trick for "text pinned inside the pill."

## The three-layer mental model

1. Canvas (container bg, radius, overflow)
2. Light (blobs: filter + blend)
3. Surface (line: z-index; plus anything you layer on)

Master those and adding "a label in the corner" or "a glossy cap over the wave" stops requiring library knowledge — it's just CSS on a box you know.

## Check your understanding

1. Why does the line need `z-index: 1` when it appears *first* in the HTML?
2. What happens to blob overlap if you set `--blob-blend: normal`?

Answer 1: DOM order would leave blobs painting over the line; the explicit z-index pulls the line to the front. Answer 2: overlaps stop brightening — blobs just sit on top of each other, flat, and the wave looks like stacked discs instead of light.

Next: [06 — Root overrides, done right](06-root-overrides.md).