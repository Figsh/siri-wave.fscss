# siri-wave.fscss

A Siri-style animated wave loader: a baseline line with six pulsing, blurred color blobs, built entirely from FSCSS mixins. Pure CSS.

## Install

Via remote import:

```fscss
@import((*) from siri-wave)
```

Or from this repo directly

```fscss
@import((*) from "https://cdn.jsdelivr.net/gh/Koolkidkonrad/siri-wave.fscss@main/siri-wave.fscss")
```

## Usage

The fastest path is the one-call preset:

```fscss
@import((*) from siri-wave)

@siri-tokens()
@siri-wave-preset(.siri-wave)
```

```html
<div class="siri-wave">
  <div class="siri-line"></div>
  <div class="blob magenta"></div>
  <div class="blob cyan"></div>
  <div class="blob green"></div>
  <div class="blob purple"></div>
  <div class="blob orange"></div>
  <div class="blob blue"></div>
</div>
```

`siri-tokens()` writes the shared design tokens (colors, sizing, animation timing) to `:root` and only needs to be called once per page, even with multiple waves.

## Multiple waves on one page

Every mixin takes a selector parameter, so you can run the preset again under a different class:

```fscss
@siri-tokens()
@siri-wave-preset(.wave-hero)
@siri-wave-preset(.wave-footer)
```

## Customizing

Override any token after calling `siri-tokens()` to restyle without touching the mixins:

```fscss
@siri-tokens()

:root {
  --siri-wave-container-bg: #0a0a12;
  --blob-magenta-bg: #ff2ecb;
}

@siri-wave-preset(.siri-wave)
```

## Composing manually

If you don't want the full preset, the individual pieces are all public:

```fscss
@siri-tokens()
@siri-base(.my-wave)
@siri-wave(.my-wave)
@siri-line(.my-wave .siri-line)
@siri-blob(.my-wave .blob)
@siri-blob-colors(.my-wave .blob)
```

## Public mixins

| Mixin | Purpose |
|---|---|
| `siri-tokens(root:root)` | Design tokens (colors, sizing, animation timing), written once |
| `siri-base(st:.siri-wave)` | Scoped margin/padding/box-sizing reset |
| `siri-wave(st:.siri-wave)` | The outer container |
| `siri-line(st:.siri-line)` | The baseline line |
| `siri-blob(st:.blob)` | Shared blob styling (shape, blend mode, animation) |
| `siri-blob-variant(st, color)` | One blob color variant, by name |
| `siri-blob-colors(st:.blob)` | All six default color variants, generated |
| `siri-wave-preset(st:.siri-wave)` | One-call composite of everything above |

## Requirements

FSCSS `>=1.2.0`.

## License

MIT
