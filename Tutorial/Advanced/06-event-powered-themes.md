# 06 — `@event`-powered themes

`@event` is FSCSS's conditional — a function that returns a value depending on its argument. It's the door to *theme systems* for siri-wave without any JS.

## The basic event

```fscss
@event wave-theme(mode) {
  if mode: dark {
    return: #0a0a12;
  }
  el {
    return: #f4f4f8;
  }
}
```

`el` is the else. Fall through: `dark` → `#0a0a12`, anything else → `#f4f4f8`.

## Wire it into tokens

```fscss
:root {
  --siri-wave-container-bg: @event.wave-theme(dark);
  --siri-wave-line-bg: @event.wave-theme(dark);
}

.light-page {
  --siri-wave-container-bg: @event.wave-theme(light);
}
```

One event, two scopes. Note the light-page override doesn't retype a color — it makes a *choice*. The theme logic stays in one place.

## Theme == one decision point

Put every wave knob behind an event and the whole theme is a single argument:

```fscss
@event wave-ui(theme){
  if theme: dark {
    return: #0a0a12;  /* container bg */
  }
  return: #f4f4f8;
}
```

Combining tokens with `@event` branches (the docs pattern):

```fscss
@event wave-dims(size){
  if size: compact  { return: 96px; }
  el-if size: cozy   { return: 160px; }
  el-if size: theater { return: 320px; }
  el { return: 160px; }
}

:root {
  --siri-wave-container-height: @event.wave-dims(cozy);
}
```

Three sizes, one knob argument. Variable-driven at runtime, event-driven at compile time.

## Comparison branch

Events can compare numbers — this is how you'd express "energy levels" purely in the stylesheet:

```fscss
@event energy(level) {
  if level >= 70 { return: 140px; }
  el-if level >= 40 { return: 90px; }
  el { return: 30px; }
}
```

If the *page* knows an energy level, the wave's blob height follows:

```fscss
.energy-low  { --blob-magenta-height: @event.energy(20); }
.energy-mid  { --blob-magenta-height: @event.energy(55); }
.energy-high { --blob-magenta-height: @event.energy(90); }
```

The cascade stays honest: `--blob-magenta-height` on a class, event resolving the value. Swap the class in HTML/JS, the wave obeys. That's a compile-time theme system with zero JS — and it composes exactly with the runtime class-toggling you used in Inter/10.

## Beware the trap

`@event` evaluates at *compile time*. It cannot read a live value (user's theme preference, a fetched number). At runtime, FSCSS re-evaluates events per render in browser mode, but the *source of the argument* is still literal — you can't pass `window.innerWidth`. For live data, use the CSS-variable bridge from the next lesson. This road map: `@event` = author-time theming, `--tokens` + JS = live data.

## Check your understanding

1. Where does the "dark/light" decision effectively live — at author time or runtime?
2. When should you *not* use `@event` for a wave number?

Answer 1: author time — it compiles to plain values keyed by the literal you passed. Answer 2: when the number won't be known until something happens at runtime (live audio, fetched metrics) — that's JS territory.

Next: [07 — The JS bridge: live audio](07-js-bridge-live-audio.md).