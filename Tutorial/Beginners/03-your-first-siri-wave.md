# 03 — Your first siri-wave

Time to put a wave on a page. It's three lines of FSCSS and one chunk of HTML.

## The full page

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>my first wave</title>
    <script src="https://cdn.jsdelivr.net/npm/fscss@1.2.0/runtime.min.js" defer></script>
    <style>
@import((*) from siri-wave)

@use(root){
  background: #0d0d14;
}

@siri-tokens()
@siri-wave-preset(.siri-wave)
    </style>
</head>
<body>
    <div class="siri-wave">
        <div class="siri-line"></div>
        <div class="blob magenta"></div>
        <div class="blob cyan"></div>
        <div class="blob green"></div>
        <div class="blob purple"></div>
        <div class="blob orange"></div>
        <div class="blob blue"></div>
    </div>
</body>
</html>
```

Open it. You should get a dark rounded rectangle with a faint glowing line and six colored blobs pulsing gently around it.

## What did we actually write?

```fscss
@siri-tokens()
```

Puts the design tokens and the `siri-pulse` keyframes on the page. Run it once per page, even if you have ten waves.

```fscss
@siri-wave-preset(.siri-wave)
```

The big one. It expands into five internal calls:

```fscss
@siri-base(.siri-wave)
@siri-wave(.siri-wave)
@siri-line(.siri-wave .siri-line)
@siri-blob(.siri-wave .blob)
@siri-blob-colors(.siri-wave .blob)
```

Read the selector arguments carefully, because they map 1:1 to the HTML:

- `.siri-wave` → the container
- `.siri-wave .siri-line` → the line
- `.siri-wave .blob` → every blob, plus each color variant

## The "no matching rule" trap

The preset generates rules that point at `<div class="blob magenta">` etc. If you leave a blob's color class off, or spell `.magenta` as `.Magenta`, that blob gets the shared blob styling but none of its color sizing — it'll sit at the `70px` fallback and look wrong. The order in the class attribute is irrelevant. The class *names* have to match exactly.

## Try changing one thing

Change the preset anchor to a different class and update the HTML to match:

```fscss
@siri-wave-preset(.boot-wave)
```

```html
<div class="boot-wave"> <!-- rest identical --> </div>
```

Still works. The preset is just a parameterized factory — you own the class name.

## Check your understanding

1. Why do we call `siri-tokens()` only once for many waves?
2. What happens to a blob whose color class doesn't match a variant?

Answers: (1) it writes shared tokens to `:root` — CSS variables are inherited, so one call covers everything; (2) it falls back to generic blob sizing instead of its color token set.

Next: [04 — Wave HTML anatomy](04-wave-html-anatomy.md), where we pull the markup apart piece by piece.