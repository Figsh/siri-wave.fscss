# 07 — The JS bridge: live audio

Now the headliner. FSCSS can't read a microphone — but it made every blob dimension a CSS variable, and JavaScript can write CSS variables. That gap is where the live-audio wave is born. The wave stays pure CSS; JS just feeds it numbers.

## The anatomy of the bridge

```
Web Audio API ──> AnalyserNode ──> frequency bins ──> setProperty("--blob-<name>-height", …px) ──> siri-wave animates
```

Six blobs → six frequency bands → six heights, each frame.

## The full page

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>siri · live audio</title>
    <script src="https://cdn.jsdelivr.net/npm/fscss@1.2.0/runtime.min.js" defer></script>
    <style>
@import((*) from siri-wave)

@siri-tokens()
@siri-wave-preset(.siri-wave)

body {
  background: #0d0d14;
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  margin: 0;
}

.siri-wave .blob { transition: height 0.12s ease-out; }
    </style>
</head>
<body>
    <button id="listen">listen</button>
    <div class="siri-wave">
        <div class="siri-line"></div>
        <div class="blob magenta"></div>
        <div class="blob cyan"></div>
        <div class="blob green"></div>
        <div class="blob purple"></div>
        <div class="blob orange"></div>
        <div class="blob blue"></div>
    </div>
    <script>
        const root = document.documentElement;
        const colors = ['magenta', 'cyan', 'green', 'purple', 'orange', 'blue'];

        document.getElementById('listen').addEventListener('click', async () => {
            const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
            const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
            const source = audioCtx.createMediaStreamSource(stream);
            const analyser = audioCtx.createAnalyser();
            analyser.fftSize = 256;
            analyser.smoothingTimeConstant = 0.75;
            source.connect(analyser);

            const bins = new Uint8Array(analyser.frequencyBinCount);
            const bandSize = Math.floor(bins.length / colors.length);

            const loop = () => {
                analyser.getByteFrequencyData(bins);
                for (let i = 0; i < colors.length; i++) {
                    let sum = 0;
                    for (let j = i * bandSize; j < (i + 1) * bandSize; j++) sum += bins[j];
                    const avg = sum / bandSize;
                    const height = Math.max(8, Math.round((avg / 255) * 110));
                    root.style.setProperty('--blob-' + colors[i] + '-height', height + 'px');
                }
                requestAnimationFrame(loop);
            };
            loop();
        });
    </script>
</body>
</html>
```

Allow the mic, then speak or hum. Six blobs track the spectrum. You built a Siri, roughly.

## Why the details here matter

- **`fftSize: 256`** → 128 bins. Cheap enough for `requestAnimationFrame` every frame; six bands of ~21 bins each.
- **`smoothingTimeConstant: 0.75`** → averages bins over time. Without it the wave twitches hyperkinetically. Raise toward 0.9 for laggy, 0.5 for snappy.
- **`Math.max(8, …)`** → silence doesn't collapse blobs to 0px.
- **`.blob { transition: height .12s }`** → the per-frame jumps glide instead of snapping. This single line is 50% of the "smooth" feel.
- **The button exists to satisfy browsers' autoplay policy** — `getUserMedia` needs a user gesture typically.

## The rule: JS feeds tokens, never styles

Sure, JS *could* set widths inline. But then your design logic forks to two places. The stable contract: JS owns the *data*, updates the *tokens*, and siri-wave owns the *styling*. You can take the same JS array and drive flux-wave's `--flux-band*` tokens tomorrow without touching the audio code.

## Doesn't this break "pure CSS"?

No — the wave is pure CSS. The audio *input* is external data, exactly like the `--blob-magenta-height: 90px` you typed in Beginners, just arriving 60×/sec via JS. If "pure CSS, data-driven" feels like a contradiction, flip it: nearly every production visualizer is "pure component, JS data." siri-wave just makes *CSS* the component.

## Check your understanding

1. What would `smoothingTimeConstant: 0` do to the wave?
2. Why is the film-smoothness line a CSS property, not JS?

Answer 1: each frame starts from raw bins — visible twitch, less coherent. Answer 2: smoothing is presentation, and presentation is CSS; JS sticking to data keeps them decoupled.

Next: [08 — Debugging with `exec()`](08-debugging-with-exec.md).