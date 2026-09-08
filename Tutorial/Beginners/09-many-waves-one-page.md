# 09 — Many waves, one page

One wave is nice. Three is a design. Because the preset is just a parameterized factory, more waves is barely more work.

## The core observation

Every mixin from `siri-wave-preset` down to `siri-blob-variant` takes a selector argument. That's not an accident — the library was written so you can point a wave at any class.

```fscss
@siri-tokens()                /* once. tokens are global. */
@siri-wave-preset(.hero-wave)
@siri-wave-preset(.footer-wave)
```

That + matching HTML gives you two independent waves sharing the same tokens and keyframes.

```html
<div class="hero-wave">
    <div class="siri-line"></div>
    <div class="blob magenta"></div>
    <!-- ...blob-cyan, -green, -purple, -orange, -blue -->
</div>

<div class="footer-wave">
    <div class="siri-line"></div>
    <div class="blob magenta"></div>
    <!-- ... -->
</div>
```

Same seven-div skeleton, different container class. Done.

## Wait — don't the selectors collide?

The rules generated are `.hero-wave .siri-line`, `.hero-wave .blob.green`, etc. — all scoped under the container class. Nothing leaks. That scoping is *the* reason the preset takes a selector instead of hardcoding `.siri-wave`.

## Per-wave differences

Global tokens hit both waves. To make them differ, scope overrides under the container:

```fscss
.hero-wave {
  --blob-green-height: 110px;
}
.footer-wave {
  --blob-green-height: 30px;
}
```

CSS custom properties cascade per element. The hero's green blob reads the hero-scoped value; the footer's reads its own. Same token name, two opinions, no conflict.

## Same page, different scales

```fscss
:root {
  --siri-wave-container-height: 160px;
}
.hero-wave {
  --siri-wave-container-height: 240px;
}
.mini-wave {
  --siri-wave-container-height: 64px;
}
```

Now an `.mini-wave` preset hmm — three presets, one global default for everything else. Authored once, scaled per context.

## A wrapper to keep the HTML clean

Tired of writing seven divs per wave? Use a bit of FSCSS variable to build the blob list once:

```fscss
$blobs: magenta cyan green purple orange blue;
```

...or just accept the seven divs. The library intentionally avoids generating markup (tokens and selectors only). Most real projects wrap the wave in a component and write the divs in one template spot.

## The one-call-per-tokens rule, restated

`@siri-tokens()` must still run exactly once, because it defines `@keyframes siri-pulse` and the global defaults. Calling it twice is harmless (same values overwrite), but pointless. Presets are cheap; tokens are a one-time thing. Keep that split.

## Check your understanding

1. Why can't a mistyped `.hero-wave` inside the preset styles a completely different element than you meant?
2. How does `.mini-wave { --siri-wave-container-height: 64px }` override a 160px default from `:root`?

Answer 1: scoping — rules only match under the container ancestor you named. Answer 2: descendant element's custom property wins over the root's for itself and its children — closer always beats farther.

Next: [10 — Mini project: a now-playing loader](10-mini-project-now-playing-loading.md).