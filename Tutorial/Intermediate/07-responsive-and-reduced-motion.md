# 07 — Responsive and reduced motion

A wave that animates everywhere, at every size, ignoring the user's preference, is a wave that gets flagged in a code review. Let's handle size and motion respect.

## Scaling by container

The tokens are variable-driven, so "responsive siri-wave" is mostly "move the tokens around with media queries":

```fscss
@media (max-width: 640px) {
  :root {
    --siri-wave-container-height: 96px;
    --siri-wave-container-max-width: 340px;
    --blob-green-height: 60px;
    --blob-filter: blur(7px);
  }
}
```

Smaller box, calmer blobs on phones. Nothing else changes — the mixins do the resizing for you.

## Don't forget the blobs' size limits

The container shrinks but blob widths are absolute (`--blob-green-width: 90px`). On a tight box, six × ~70-90px blobs overflow into the radius corners (clipped, remember). So when you scale down, also scale blob widths/heights — or the wave looks cramped inside its pill.

A cheap approach for sizing density:

```fscss
@media (max-width: 640px) and (orientation: portrait) {
  :root {
    --blob-magenta-width: 50px;  --blob-magenta-height: 26px;
    --blob-cyan-width: 44px;     --blob-cyan-height: 24px;
    --blob-green-width: 56px;    --blob-green-height: 32px;
    --blob-purple-width: 46px;   --blob-purple-height: 26px;
    --blob-orange-width: 42px;   --blob-orange-height: 24px;
    --blob-blue-width: 46px;     --blob-blue-height: 26px;
  }
}
```

Repetitive, but explicit, and easy to audit in one look.

## Reduced motion: the good citizen switch

The whole library is one animation, so respecting `prefers-reduced-motion` is child's play:

```fscss
@media (prefers-reduced-motion: reduce) {
  :root {
    --blob-anim: none;
  }
}
```

That kills the pulse for everyone who asked for less motion. The blobs render static — still recognizable as a wave, just not a moving one.

Want less extreme than "off"? A 60%-slower, gentler loop respects intent without killing motion:

```fscss
@media (prefers-reduced-motion: reduce) {
  :root {
    --blob-anim: siri-pulse 8s infinite ease-in-out alternate;
    --blob-opacity: 0.4;
  }
}
```

Subtle breathing instead of pulsing. Pick the version that fits your audience.

## Bonus: respect el tab's focus

Aesthetic a11y points:

- Don't rely on motion for meaning. If the wave is your one "loading" indicator, pair it with `aria-busy="true"` on a wrapper or a `role="status"` offscreen message.
- The wave is decorative 99% of the time → `aria-hidden="true"` on the container.

## Check your understanding

1. Why does shrinking only the container leave the wave looking cramped?
2. What does `--blob-anim: none` actually produce for a blob?

Answer 1: blob *sizes and positions are absolute token values* — they don't auto-scale with the box. Answer 2: a static, still-styled blob — shape, blur, blend, and position remain; only the cycle stops.

Next: [08 — siri meets plain CSS](08-siri-meets-plain-css.md).