# 06 — Semver and package meta

Version numbers are the quiet contract users rely on. The repo's `package.json` is where that contract gets written down — and where things go quietly twitchy if you get it wrong.

## Reading siri-wave's meta

```json
{
  "name": "siri-wave",
  "version": "1.0.0",
  "description": "Siri-style animated wave and pulsing blob loader, built entirely from FSCSS mixins with pure css.",
  "extension": "fscss",
  "fscss_version": ">=1.2.0",
  "blocked_methods": [],
  "repository": "https://github.com/Koolkidkonrad/siri-wave.fscss",
  "file": {
    "usage": {
      "directive": "siri-*",
      "helpers": [
        "siri-tokens(root:root)",
        "siri-base(st:.siri-wave)",
        "siri-wave(st:.siri-wave)",
        "siri-line(st:.siri-line)",
        "siri-blob(st:.blob)",
        "siri-blob-variant(st, color)",
        "siri-blob-colors(st:.blob)",
        "siri-wave-preset(st:.siri-wave)"
      ]
    },
    "modules": ["siri-wave.fscss"],
    "remote": "siri-wave.fscss"
  },
  "author": "Koolkidkonrad",
  "license": "MIT",
  "keywords": ["fscss", "siri", "wave", "blob", "loader", "animation", "css", "no-js"],
  "bugs": "https://github.com/Koolkidkonrad/siri-wave.fscss/issues"
}
```

Inspect the interesting corners — not the boring JSON.

- **`"extension": "fscss"`** — tooling knows this is an FSCSS package.
- **`"fscss_version": ">=1.2.0"`** — the floor. Consumers' tooling can gate on it.
- **`"blocked_methods": []`** — declared disallowed helpers. An empty list = "everything declared is allowed." If you ever delete a helper, *this* is where you block the old name so typeahead/conventions stop suggesting it.
- **`"file.usage"`** — the machine-readable API: directive `siri-*`, every helper signature with its defaults. This is what makes "`from siri-wave`" resolvable and self-describing.
- **`"file.modules"` + `"file.remote"`** — the exact filename to pull, so `from` imports hit the right single file.

## The versioning rules 90% of people need

Two axioms, then a table:

1. **Ships to the registry → breaking-change discipline.** Anyone importing by name gets your next file automatically if they don't pin. Be honest about breaks.
2. **Small common-sense > perfect federation.** Don't split hairs between patch and minor; don't bikeshed.

| Bump | When |
|---|---|
| `major` | Breaking: helper renamed/moved/removed, token names re-keyed, default behavior changes, FSCSS floor rises |
| `minor` | New helper/token that leaves old ones working; additive theming surface |
| `patch` | Fallback value fix, keyframes value fix, docs typo, no API change |

## Deprecating a helper well

Rename `siri-pulse-soft` to `siri-pulse-smooth`:

- Keep the old name too, as an alias: `@define siri-pulse-soft(...){ @siri-pulse-smooth(...) }`.
- `blocked_methods: ["siri-pulse-soft"]` if you truly purge it.
- Changelog line, then bump.

Aligning `"version"` and the README's "Requirements" line (this repo states `FSCSS >= 1.2.0` — the floor in the README must match the JSON, or adopters fight phantom mismatches).

## Check your understanding

1. What would "1.0.0 → 1.1.0" plausibly ship for siri-wave?
2. Where does a new helper appear in the JSON, and why does that matter beyond decoration?

Answer 1: an additive surface — a new token or a new helper — that leaves the existing eight untouched. Answer 2: under `file.usage.helpers` — it's the resolvable API definition the ecosystem reads; import-name resolution and tooling suggestions use it, so an undeclared helper may be invisible to consumers checking the manifest.

Next: [07 — Framework integrations](07-framework-integrations.md).