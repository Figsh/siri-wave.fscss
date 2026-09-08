# 02 — Write your own keyframes

`siri-pulse` isn't the only motion possible in a wave — it's just the one the author shipped. Now you'll author motion that *feels* like siri but isn't bound to the stock keyframes.

## Deconstruct `siri-pulse` first

```css
@keyframes siri-pulse {
  0%   { transform: scaleY(0.25) scaleX(0.75) translateX(-8px); opacity: 0.35; }
  35%  { transform: scaleY(1.35) scaleX(1.15) translateX(4px);  opacity: 0.95; }
  70%  { transform: scaleY(0.7)  scaleX(0.95) translateX(10px); opacity: 0.65; }
  100% { transform: scaleY(0.45) scaleX(0.85) translateX(-4px); opacity: 0.5;  }
}
```

What makes it read "siri":

- **Peak is not at 50%.** It's at 35%, then a controlled fall. Asymmetry = life.
- **X-move together with Y-scale.** Blobs drift sideways as they stretch — organic, not a jack-in-the-box.
- **Opacities never go exactly 0 or 1.** Always mid-air, always some light.
- **Same property set each frame** (transform, opacity) → GPU-friendly, compositor-clean.

## Author a variant: "breaker"

A roll-in wave for page transitions:

```css
@keyframes siri-break {
  0%   { transform: scaleY(0.2) scaleX(0.6) translateX(-16px) rotate(-6deg); opacity: 0; }
  30%  { transform: scaleY(1.5) scaleX(1.1) translateX(2px)  rotate(3deg);  opacity: 0.9; }
  65%  { transform: scaleY(0.8) scaleX(0.9) translateX(8px);               opacity: 0.7; }
  100% { transform: scaleY(0.5) scaleX(1)   translateX(-2px);              opacity: 0.55; }
}
```

Same discipline, new personality — rotation adds a swim. `rotate` joins `transform`, so the GPU trick still applies (still one `transform` property per frame; the browser handles the combined matrix).

## Wire it in like a pro

Instead of bottlenecking through `--blob-anim` (which changes *all* blobs), use your variant-plus from Advanced/02:

```fscss
:root {
  --blob-magenta-anim-name: siri-break;
  --blob-cyan-anim-name: siri-break;
}
```

Two blobs glide-swim; the rest keep the classic pulse. Multi-keyframe personality per blob, no forks.

## The compact keyframe shorthand

FSCSS has a one-block form that defines *and* applies:

```fscss
$(@keyframes siri-breathe, .wave .blob, &[6s ease-in-out infinite alternate]) {
  from { transform: scaleY(0.6); }
  to   { transform: scaleY(1.25); }
}
```

That emits the keyframes **and** the animation property on `.wave .blob`. Great for a quick prototype; for library-grade work I'd still write them explicitly — the shorthand hides the "what runs on what" split.

## Motion checklist before you ship a keyframe set

1. Same properties everywhere (transform transforms; opacity fades) — no property toggling between frames.
2. Opacity stays off the hard 0/1 rails unless the design demands a flash.
3. Peak sits off-center. Boring keyframes time their peak at 50%; waves peak at 30-40%.
4. Durations vary per blob afterwards — uniform duration on your new keyframes will still look uniform.

## Check your understanding

1. Why does `rotate` in `siri-break` not break the "GPU-friendly" rule from `siri-pulse`?
2. What makes a peak at 35% feel "Siri" where 50% feels metronomic?

Answer 1: it's the same `transform` property being animated — the browser composites one matrix. Answer 2: asymmetric rise and fall — a fast grow with a slow, weighted settle — reads as organic, not counted.

Next: [03 — Build a wave library](03-build-a-wave-library.md).