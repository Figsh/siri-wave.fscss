# The Final Project — three real-world builds

You've finished 40 tutorials. Now the contract: three projects, each a thing people actually build, each solved **strictly with siri-wave** for every wave. The rules are the walls; the walls are the point.

## The rules for all three

1. **Every wave is siri-wave.** No other wave library, no canvas, no SVG-animated waveform. The visuals are the token-driven siri-wave or you're doing it wrong.
2. **JS may only feed data, never style.** All visual behavior goes through `--siri-wave-*` / `--blob-*` tokens and state classes, per the Advanced/07 rule. No inline `style=` from JS anywhere.
3. **`prefers-reduced-motion` visibly works.** Static, calm wave for those who asked.
4. **No `!important`.** If you need it, your scoping is wrong (Experienced/10).
5. **Ships precompiled.** Run the CLI, serve the `.css`, and it must still work — no runtime dependency in the final build.
6. **Files are modular** — tokens, states, preset, and page split per Advanced/09.
7. **A README.** Problem, approach, file map, how to run. Assume a stranger inherits it Monday morning.

Pick one, build it properly. Two is bragging. All three is unreasonable for a weekend — and a legend by Wednesday.

---

## Project 1 — Now-Playing: a wave that reacts to a real track

**The problem:** A music web app needs a "now playing" screen. The wave must flow not from the mic but from *the actual track being played*. Functions on mute? The wave goes idle, not dead.

**Constraints that test you:**

- Audio source is a `<audio>` element / MediaElement, not the microphone. Use `createMediaElementSource` — not `getUserMedia`. If you reach for the mic here, the test fails; fix the "wrong input device" smell.
- Track changes must rewire the analyser cleanly (no double graphs, no leaks).
- Play/pause/seek must not desync the wave.
- Muted or near-silent track → visible "quiet" state, and it must recover the moment sound returns (silence detection, Advanced/10).

**What a good solution shows:**

- A track UI (art, title, scrubber) built with plain HTML/CSS that wraps one siri-wave.
- The wave moves with spectrum data from the real track.
- Token-driven states for play / paused / quiet / loud.
- The `.css` in dist, component in `src`, all modular.

**Hints:** `createMediaElementSource` + `AnalyserNode` + a `requestAnimationFrame` loop is the mirror image of Advanced/07 — same bridge, different input. Watch `fftSize` cost on long tracks. Silence detection is just a running average threshold.

---

## Project 2 — Voice Assistant status panel

**The problem:** A smart-speaker/assistant interface lives in four states: **idle**, **listening**, **thinking**, **speaking**. Users must instantly read which one the device is in — without reading. The wave is the status.

**Constraints that test you:**

- State machine is **not** timers-in-CSS. JS toggles a state class on the widget; the *tokens* under that class change the wave. The signal must be observable from a labeled component (Experienced/07).
- **Thinking ≠ trying to talk.** Thinking should read as "processing": smaller motion without full voice energy. Speaking should be the most alive. If both look identical you've failed.
- Thinking state may interleave with a real mic feed (Advanced/07) — the two must compose, not fight.
- Keyboard accessible: a console/API consumer raises states without touching markup.

**What a good solution shows:**

- One wave, four clearly-different states, each decodable in a screenshot.
- A transition *between* states that doesn't jump (CSS transition on tokens).
- Real mic data on "listening" (worst case: simulated data — but the data pipeline must be a swap, not a rewrite).

**Hints:** State tokens per class (Inter/10 had the skeleton): idle = dim + slow, listening = live spectrum, thinking = medium pulse, speaking = full energy + faster duration. `@event` can't read live state (Advanced/06) — reach for scoped `--` overrides, the cascade is your condition.

---

## Project 3 — Live ops monitor

**The problem:** An ops console watches services (API, DB, queue, etc.). Each service is a wave; its height is its current load (requests/min, queue depth, error rate). A wall of green and no alerts is worth more than a pager — productively.

**Constraints that test you:**

- Data comes from a **fetch/poll** (mock endpoint is fine — but the poll contract must look real: JSON, status codes, retry).
- Six services ⇒ six distinct siri-waves sharing ONE token system (`@siri-tokens()` once, presets per service, overrides per card).
- Load drives **blob heights + a state class** (ok / warn / critical). Warning must be obvious without color-blind exclusion — the *motion* changes, not just the hue.
- Demo must survive a service going black: node is down ≠ flat line. It's a red, fast, *alarm* — not a zero.
- **Autoscale tests you:** connections spike from `500 → 5000`. The wave shouldn't melt the frame rate (Experienced/04 rules apply).

**What a good solution shows:**

- A grid of cards, each with service name + a siri-wave + a numeric readout.
- Poll loop with clean failure handling; states on the whole widget, not per-pixel hacks.
- The compiled css-only version in `dist`, warm and honest README.

**Hints:** Map each metric to `--blob-<color>-height` linearly; map error/critical to state classes that re-token opacity + duration. For many waves, global tokens + scoped overrides is exactly the multi-wave architecture from Beginners/09 + Inter/06. Silence isn't a metric — design the "down" state deliberately.

---

## Judgment

Grade against the five "rules for all three" wheel, then the project-specific constraints. Honest scoring:

- All 5 rules + the project's constraints → **ship-grade**. Put it on GitHub, link it.
- 4/5 rules, clean project → **strong pass**, fix the one rule.
- 1–2 rule slips → **rework now**, the rule you skipped is likely the one your next job demands.

If you build all three, they should *look like three different products* — because three different problems get three different expressions of one wave library. That contrast is the whole course in miniature.

Congratulations — that's the course. Go ship one.