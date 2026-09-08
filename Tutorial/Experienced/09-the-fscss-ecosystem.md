# 09 — The FSCSS ecosystem

After forty lessons you've been living *inside* FSCSS. Step outside and see the neighborhood. Knowing the map is how you contribute and steer, not just consume.

## The pieces that matter

| Piece | What it is |
|---|---|
| **FSCSS** | The project itself — `fscss-ttr/FSCSS` on GitHub, published as `fscss` on npm |
| **Compiler / runtime** | Same engine, two modes: `fscss in.fscss out.css` (CLI) and `runtime.min.js` (browser, auto-scan). Since 1.2.0 the two entry points are `runtime.js` and `esm.js` |
| **Libraries directory** | `fscss-ttr/FSCSS/xf/styles/` — where by-name imports like `from siri-wave` resolve (Experienced/05) |
| **devtem** | The docs + tooling front door: `fscss.devtem.org` (docs, import guide, compiler API) |
| **npm** | Distribution for the `fscss` engine and ecosystem tooling |

## The version story

FSCSS built back to 2022 (shorthand `%2-%6`, variables), went public in 2025, hit v1.1.25 in 2026 with arrays, `@event`, `pattern()`, then **v1.2.0** consolidated the browser surface into `runtime.js`/`esm.js`. That's the version you've been pinning all course (`>=1.2.0`). Ecosystem moves; pinning floors keeps a wave honest.

## Where siri-wave fits

Walk the map from siri-wave's point of view:

1. Repo: `Koolkidkonrad/siri-wave.fscss` — the canonical source.
2. Package: `package.json` declares name, FSCSS floor, helpers, remote file.
3. Registry: sits in `xf/styles/` → `@import((*) from siri-wave)` resolves.
4. Docs: this Tutorial course + the README.
5. Neighbors: flux-wave (flowing gradient bands), the rest of `xf/styles/`.

That's the same five slots your own library (Experienced/03, /05) slots into.

## How to actually contribute

House rules are small and humane:

- **Issues over tweets.** FSCSS lives on GitHub issues — report a compiler bug with a *minimal* `.fscss`, "works on my machine" is not a repro.
- **The library dir makes using easy.** If your `.fscss` pattern is good (Experienced/03 discipline), the file itself is the proposal — PR it into `xf/styles`.
- **Discussions + dev.to.** The project runs a GitHub Discussions and publishes tutorials to dev.to; teaching others the `-*-`-free, token-first way is a real contribution.

## Reading the tea leaves

As a library author, watch:

- `fscss_version` floors in others' `package.json` (tells you what they consider safe).
- `blocked_methods` (tells you what's being retired — an early warning of breaking changes).
- The docs changelog (or GitHub release notes) for the next `>=` floor.

You'll find you're no longer a user — real authors read the compiler's release notes the way sailors read the weather.

## Check your understanding

1. Where does "by name" resolution actually live in the ecosystem?
2. Given the 1.2.0 browser-file consolidation, what should your README pin and why?

Answer 1: the `xf/styles/` directory in the FSCSS repo — that's where imports like `from siri-wave` point. Answer 2: `fscss_version: >=1.2.0` (and CDN URLs pinned to 1.2.0) — consistent floors avoid phantom version drift on adopters.

Next: [10 — Capstone: production feature](10-capstone-production-feature.md).