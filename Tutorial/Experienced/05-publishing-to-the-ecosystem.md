# 05 — Publishing a library

siri-wave gets imported by name — `@import((*) from siri-wave)` — with no URL. That convenience is a delivery system, and now you control it. Here's how a `.fscss` library becomes a *registered* one.

## Where names come from

The FSCSS registry — the library directory that by-name imports resolve against — is the `fscss-ttr/FSCSS` repository, in `xf/styles/`. When your library file lands there:

- `@import((*) from your-lib)` resolves to `xf/styles/your-lib.fscss`.
- The devtem site (`fscss.devtem.org`) references it, READMEs link it, and the runtime can pull it by name anywhere.

That's the whole journey: **one file, one directory, one by-name name.** The expensive part is the repo ownership + a maintainer review, not the code.

## Cheap-but-real publishing path

1. **Make the repo.** `<your-lib>.fscss` as a repo name tells everyone what it is.
2. **Headline files:** the `.fscss` itself, a README that opens with the 3-line install + usage, a sample HTML, `package.json` with metadata.
3. **Ship the file to the registry.** Open the PR that adds your `.fscss` into `fscss-ttr/FSCSS/xf/styles/`. Actually going through with it is the difference between "I have a repo" and "people can `from your-lib`".

## Retailetail your README for the compiler, not just browsers

```
- Usage must be copy-paste: import line, token call, preset call, and the HTML.
- Show the "override after tokens" pattern early — it's the #1 thing users try.
- Name the FSCSS version floor (`>= 1.2.0`).
- Screenshot or animated preview. Nobody adopts invisible.
```

## The remote URL escape hatch

Even unregistered, a library is importable straight from its raw URL (READMEs do this):

```fscss
@import((*) from "https://cdn.jsdelivr.net/gh/you/your-lib.fscss@main/your-lib.fscss")
```

So "not in the registry" never means "unusable" — it means an uglier import line for adopters. Registry is polish; URL is substance.

## Don't publish junk

Before you offer a library:

- Compile with the CLI and open the CSS — no stray `exec(_warn)` firing on every page as-is.
- Named token precedence sett the ones a user *will* try to override; put them at the top of the token define.
- Ship the keyframes with the tokens that time them (the one-call rule from the start).
- Version floor declared. Test the floor, not just the latest.

## The ecosystem flywheel

Registry entry → 3-line install → README adoption → issues/PRs → contributions → your name on the founder hall. Small files, small asks, big compounding. siri-wave is live proof; flux-wave beside it. Now you and your wave libs.

## Check your understanding

1. What, concretely, changes for an adopter once your file is in `xf/styles`?
2. What's the no-approval-needed fallback that always works?

Answer 1: the import shortens to `@import((*) from your-lib)` and appears in the FSCSS library list. Answer 2: the raw/jsDelivr URL form — registered or not, it always imports.

Next: [06 — Semver and package meta](06-semver-and-package-meta.md).