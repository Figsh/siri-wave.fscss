# 04 — Performance and layers

An animated wave burns frames on real phones. This is the "why is my page at 30fps" lesson. Three levers: what the animation animates, what gets painted, and how many layers you're demanding.

## Lever 1 — stay in compositor space

The browser can move, scale, and fade things on the compositor without re-painting the page — but only if the *animated* properties are `transform` and `opacity`. siri-wave's keyframes animate exactly those two. That's not an accident; that's the performance contract.

`--blob-will-change: transform, opacity` is the token telling the browser "this thing WILL move/fade" — an upgrade to its own layer early. siri-wave already ships it. If you animated `top`, `height`, or `margin` 60×/sec, you'd force layout + paint every frame — the classic 30fps wave.

**But** — live-audio (Advanced/07) drives `--blob-<color>-height`. Height isn't transform. Every frame re-layouts the blob. That's the known tax on the live wave, and we pay it silently:

```css
.siri-wave .blob { transition: height 0.12s ease-out; }
```

The transition smooths the *look* at low cost, and `will-change` keeps the now-changed-sized blob on its own layer. A better long-term bridge: animate transform scale instead (JS sets `--blob-<color>-scale`, a custom variant maps it into `transform: scale(var(--blob-magenta-scale, 1))`). Lesson for implementers: **token-drive transform, not height**, when you need 60fps.

## Lever 2 — blur and blend are paint taxes

`filter: blur(10px)` and `mix-blend-mode: screen` are GPU journeys of their own:

- Blur: gaussian of a region, re-computed when the region changes.
- Screen blend: reads backdrop, computed per-frame when things move above it.

Six blurred, blended blobs overlapping = six regions re-rolling each frame. It's *fine* for one wave; it's *sixteen* if you sprinkle ten waves page-wide. When profiling:

1. Reduce blur (`--blob-filter: blur(5-7px)`).
2. Reduce overlap by spreading `--blob-<color>-left` apart.
3. Cap the number of simultaneously-animating waves.

## Lever 3 — you choose the layer count

Each `will-change`d element is a layer. siri-wave sets it on every `.blob` (six). Add echoes (like Experienced/03) and each echo adds layers. Good habit: only `will-change` what's actually mid-animation — remove the token if you froze a wave with `prefers-reduced-motion`:

```fscss
@media (prefers-reduced-motion: reduce) {
  .siri-wave .blob {
    will-change: auto;
  }
}
```

Frozen wave, fewer layers, restful GPU.

## Reading the flame

Use DevTools → Performance / Rendering → *Paint flashing*. You want:

- Blobs and container painting **once**, then compositing only.
- After audio starts (the height-token path is active), you'll *see* paint flashes — that's the height-vs-transform tax made visible. Confirms Lever 1 in real time.

## The numbers that matter

- One wave, no audio: near-zero CPU after initial paint — compositor-only.
- One wave, live audio: layout+paint per frame — accept or switch to scale-driven variant.
- Ten waves, blur 10: expect jank on mid-range phones — trim blur/overlap/count.

## Check your understanding

1. Name two properties a compositor can animate *without* painting — and one that forces layout+paint.
2. Why does the live-audio visualizer show paint flashing that the stock wave never does?

Answer 1: `transform` and `opacity` composite; animating `height` forces layout + paint. Answer 2: audio writes `--blob-<color>-height`, a geometry change — nothing in the stock keyframes touches geometry.

Next: [05 — Publishing a library](05-publishing-to-the-ecosystem.md).