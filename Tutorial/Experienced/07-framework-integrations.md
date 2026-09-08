# 07 — Framework integrations

A wave in a React/Vue/Svelte app is the same wave — the differences are *where* the FSCSS lives and *how* the component ships. Two strategies, one right answer for most teams.

## Strategy A — precompile (the grown-up choice)

FSCSS has a CLI. Compile once, ship plain CSS:

```
fscss style.fscss style.css
```

Then the wave is just CSS + your component markup:

```jsx
// React
import './style.css';

export function Wave() {
  return (
    <div className="siri-wave">
      <div className="siri-line" />
      <div className="blob magenta" />
      {/* ...six blobs */}
    </div>
  );
}
```

What you pay: a build step. What you get: no runtime in production, deterministic output, tree-shakeable bundle that's now literally a stylesheet. For anything real, ship this. The FSCSS CDN runtime is a *prototyping* good; a compiled `.css` is a production good.

## Strategy B — runtime in the browser

The CDN runtime (as you've used all course) scans and compiles on load. Fine for:

- Landing pages without a build
- Local demos, README testers
- CMS-templated pages where a `.fscss` link is easier than deploy

```html
<link type="text/fscss" href="style.fscss">
<script src="https://cdn.jsdelivr.net/npm/fscss@1.2.0/runtime.min.js" defer></script>
```

Note `type="text/fscss"` — that's how the runtime knows the link is FSCSS *to compile*, vs a stylesheet to load. If you've only inline-`<style>`'d so far, this is the `.fscss`-as-a-file path.

## The component turns life up

The audio bridge (Advanced/07) as a React hook:

```jsx
function useWaveAnalyser(freqs, { smoothing = 0.75 } = {}) {
  useEffect(() => {
    let raf, analyser, bins;
    // getUserMedia, createMediaStreamSource, createAnalyser
    const tick = () => {
      analyser.getByteFrequencyData(bins);
      const root = document.documentElement;
      freqs.forEach((name, i) => {
        root.style.setProperty(`--blob-${name}-height`,
          `${Math.max(8, Math.round((bins[i] / 255) * 110))}px`);
      });
      raf = requestAnimationFrame(tick);
    };
    tick();
    return () => cancelAnimationFrame(raf);
  }, []);
}
```

Same CSS-variable contract, now inside a component that unmounts cleanly. **That line — "returns a cleanup" — is the whole framework-integration story**: the browser doesn't care, the framework's lifecycle does.

## State theming in the framework

`@event` themes are compile-time (Advanced/06). At runtime, reach for CSS variables + a state class, exactly like Inter/10 — frameworks are excellent at toggling classes:

```jsx
<div className={`siri-wave ${active ? 'live' : 'idle'}`}>
```

Your `.live`/`.idle` scope `--blob-*` overrides. The framework owns state; CSS owns presentation. No FSCSS involvement needed at runtime, which is the design working.

## The three-rule check for any framework

1. **Compile before you bundle.** Precompiled CSS in the framework's asset pipeline.
2. **Share tokens via CSS variables**, not via re-importing FSCSS in JS.
3. **Components own classes; data owns variables; stylesheets own look.** Violate one and tomorrow's bug hides in the other two.

## Check your understanding

1. Why is the CDN runtime acceptable in a demo but not in a production bundle?
2. What does a framework hook *return* that makes the wave safe when the component unmounts?

Answer 1: it compiles at runtime in every browser and ships FSCSS overhead; a compiled `.css` is smaller, deterministic, and already optimized. Answer 2: a cleanup that cancels `requestAnimationFrame` (and closes the audio graph) so nothing keeps feeding variables after teardown.

Next: [08 — Maintainability and audits](08-maintainability-and-audits.md).