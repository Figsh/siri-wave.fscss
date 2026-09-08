# 10 — Mini project: an assistant status dashboard

Now you use real composition: a "voice assistant" status panel with several siri-waves, each meaning something, sharing one token call. This is the intermediate checker — every mixin and trick from Inter/01-09 gets exercised.

## The brief

Three cards, one page:

1. **Assistant card** — a pulsing wave that means "listening"; its line should glow when the assistant can hear you.
2. **Microphone card** — shows the current "voice energy" via blob height (static heights, not real audio — that's Advanced).
3. **State wave** — switches between two looks when an "awaiting" class is toggled: near-idle and active.

Rules: three waves, one `siri-tokens()`, no module edits, plain CSS allowed everywhere. And `prefers-reduced-motion` must kill the pulse if the user asks.

## The HTML

```html
<main class="dash">

  <section class="card assistant">
    <h2>Assistant</h2>
    <div class="wave-status">
      <div class="siri-line"></div>
      <div class="blob magenta"></div>
      <div class="blob cyan"></div>
      <div class="blob green"></div>
      <div class="blob purple"></div>
      <div class="blob orange"></div>
      <div class="blob blue"></div>
    </div>
  </section>

  <section class="card mic">
    <h2>Mic energy</h2>
    <div class="wave-mic">
      <div class="siri-line"></div>
      <div class="blob magenta"></div>
      <div class="blob cyan"></div>
      <div class="blob green"></div>
      <div class="blob purple"></div>
      <div class="blob orange"></div>
      <div class="blob blue"></div>
    </div>
  </section>

  <section class="card await" id="stateCard">
    <h2>State</h2>
    <div class="wave-await">
      <div class="siri-line"></div>
      <div class="blob magenta"></div>
      <div class="blob cyan"></div>
      <div class="blob green"></div>
      <div class="blob purple"></div>
      <div class="blob orange"></div>
      <div class="blob blue"></div>
    </div>
    <button id="toggle">toggle state</button>
  </section>

</main>
```

Three separate anchors: `.wave-status`, `.wave-mic`, `.wave-await`.

## The FSCSS

```fscss
@import((*) from siri-wave)

@siri-tokens()

@use(.dash){
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
  gap: 20px;
  max-width: 900px;
  margin: 40px auto;
  font-family: system-ui, sans-serif;
}

.card {
  background: #12121c;
  border-radius: 16px;
  padding: 16px;
  color: #e8e8f0;
}
.card h2 { font-size: 0.85rem; text-transform: uppercase; letter-spacing: 0.08em; }

/* 1. Assistant: medium, calm */
.wave-status {
  --siri-wave-container-width: 100%;
  --siri-wave-container-height: 120px;
  --siri-wave-container-bg: #181826;
  --siri-wave-container-radius: 12px;
}

/* 2. Mic energy: tall blobs = "loud", driven by class not audio yet */
.wave-mic {
  --siri-wave-container-height: 140px;
  --siri-wave-container-bg: #181826;
  --siri-wave-container-radius: 12px;
}

/* 3. Await: two looks via a toggled class on the card */
#stateCard.await-active .wave-await {
  --siri-wave-line-bg: rgba(0, 194, 255, 0.9);
  --siri-wave-line-shadow: 0 0 12px rgba(0, 194, 255, 0.6);
  --blob-green-height: 95px;
}
#stateCard:not(.await-active) .wave-await {
  --blob-green-height: 24px;
}
```

## The tiny toggle script

```html
<script>
  const card = document.getElementById('stateCard');
  document.getElementById('toggle').addEventListener('click', () => {
    card.classList.toggle('await-active');
  });
</script>
```

Three waves. One token call. Two of them purely token-driven states, the third reacting to a class. No siri mixin edited. That's the intermediate skill: **you orchestrate states through tokens and scoped CSS, and the library stays untouched.**

## Stretch goal

Set `--blob-magenta-height` on `.wave-mic` from an interval to fake "voice energy":

```html
<script>
  let i = 0;
  setInterval(() => {
    i = (i + 1) % 3;
    const e = [40, 90, 140][i];
    document.documentElement.style.setProperty('--blob-magenta-height', e + 'px');
  }, 600);
</script>
```

Height reacts live. It's a fake — but it's the *exact* mechanism Advanced uses with a real `AnalyserNode`. You're standing on the gangway.

## Check your understanding

1. Why does toggling `await-active` on the card work when the selectors target `.wave-await` deeper inside it?
2. The state tweaks are scoped to the card. What would `:root { --blob-green-height: 95px }` have done instead?

Answer 1: ancestor class + descendant compound selector — the state re-scopes only that wave's tokens. Answer 2: every wave's green blob would jump to 95px — global, which we didn't want.

## Onward

You've composed multi-wave pages, thought about accessibility, and met other libraries. Next level: rebuild the library yourself and make it talk to real audio.

Go to [Advanced/01 — Build your own tokens](../Advanced/01-build-your-own-tokens.md).