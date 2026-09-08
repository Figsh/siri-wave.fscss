# 10 — Capstone: production feature

Everything trains toward this. The task: ship a **production-grade voice-visualizer feature** — not a demo — using the real siri-wave, in a real project shape, with professional-grade details. This is the last step before the big three in `final-project.md`, so it's calibrated to be hard but finishable in one careful sitting.

## The brief

Build a "voice meter" widget that:

1. Renders siri-wave without a blackbox — the container, line, and blobs all themed to its host page.
2. Reacts to real microphone input via the Advanced/07 bridge.
3. Signals three states visually: idle (waiting), live (listening), overload (too loud).
4. Respects `prefers-reduced-motion` (calm, no pulse).
5. Ships as precompiled CSS (Experienced/07-A) plus a framework-friendly component or a documented plain-JS variant.
6. Passes the audit from Experienced/08: no dead tokens, override/scoping discipline, reduced-motion smoke-tested, token table current.

## Constraints that make it "production"

- **No `!important`.** If you need it, your scoping is wrong.
- **No inline styles from JS.** JS writes `--blob-*` tokens only (Advanced/07 rule).
- **Reduce-motion must visibly work**, not just exist.
- **The compiled CSS survives without the runtime.** Test by serving only the `.css`.
- **40fps+ on a mid-range phone** on the "live" path (Experienced/04) — or justify the tax loudly in a comment.

## Suggested file layout

```
voice-meter/
  voice-tokens.fscss     → palette @fun, your overrides, state tokens
  voice-meter.fscss      → your preset wrapping @siri-wave-preset + states
  app.fscss              → imports, compile entry
  voice-meter.css        → precompiled output (fscss app.fscss voice-meter.css)
  index.html             → demo
  README.md              → install, usage, state API, reduced-motion note
```

## The state model in tokens

```fscss
.vm-idle {
  --blob-opacity: 0.45;
  --blob-anim: breathe-pulse 5s infinite ease-in-out alternate;
}
.vm-live {
  --blob-opacity: 1;
  --blob-anim: siri-pulse 2.2s infinite ease-in-out alternate;
}
.vm-loud {
  --blob-filter: blur(18px);
  --blob-magenta-bg: #ffffff;
}
```

JS toggles the class on the widget; the token scopes do the rest. Loud gets hazy and white-hot — your flavor.

## The audio bridge, tight version

```js
function startMeter(handler, { smooth = 0.75 } = {}) {
  const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
  return navigator.mediaDevices.getUserMedia({ audio: true })
    .then((stream) => {
      const source = audioCtx.createMediaStreamSource(stream);
      const analyser = audioCtx.createAnalyser();
      analyser.fftSize = 256;
      analyser.smoothingTimeConstant = smooth;
      source.connect(analyser);
      handler(analyser, audioCtx, stream);
    });
}
```

Handler turns bins into `--blob-<name>-height` and flips the state class when the average crosses "loud" thresholds. Cleanup returns cancel + `stream.getTracks().forEach(t => t.stop())`.

## The grading rubric (grade yourself)

| Check | Pass |
|---|---|
| Compiles clean (no `_warn`) | ☐ |
| Tokens all read (audit) | ☐ |
| Reduced-motion visibly calm | ☐ |
| No `!important`, no inline style from JS | ☐ |
| CSS-only serve works | ☐ |
| States observable + documented | ☐ |

Six checks. All six → you've shipped to the Experienced bar. One fail → fix that one and harden the pattern; you've still done real work.

## After this

You are done with the course. The `final-project.md` waits with three harder, real-world problem builds — now that you've built a feature yourself, they'll feel like a step up instead of a cliff. Go when you're ready.

Next: [../final-project.md](../final-project.md).