# 09 — Composing with other FSCSS

siri-wave is not alone in the ecosystem. You'll want heroes, badges, gradients, waves — and importing them all shouldn't turn your build into a minefield. Good news: it mostly won't, because of naming discipline.

## Multiple imports

```fscss
@import((*) from siri-wave)
@import((*) from flux-wave)
@import((*) from some-other-lib)
```

Each library brings its own directive (`siri-*`, `flux-*`, ...) and its own token namespace. FSCSS rules mean imported helpers are invoked, not globbed into a soup. You call `@siri-tokens()` for siri-wave things and `@flux-tokens()` for flux-wave things — the *names* keep them apart.

## Token namespaces are your collision shield

siri-wave owns `--siri-wave-*` and `--blob-*`. flux-wave owns `--flux-*` and its per-band tokens. Both libraries can live on the same page *without* stamping on each other's variables — different prefixes.

That's why the token-name discipline from Inter/01 matters at ecosystem scale. A library that tokens as `--bg`, `--width`, `--anim` is a hazard next to any other library. A library that namespaces everything lives nicely in a shared house.

## Composing two waves that don't fight

```fscss
@import((*) from siri-wave)
@import((*) from flux-wave)

@siri-tokens()
@flux-tokens()

@siri-wave-preset(.siri-panel)
@flux-wave-preset(.flux-panel)
```

Two independent components, two token systems, one page. The only shared stuff is `:root`, and the differing prefixes keep it collision-free.

## Watch the `--blob-*` prefix

One honest warning: `--blob-*` is a fairly generic name. If another library (or *you* in another module) also uses `--blob-*`, collisions happen — silently, because CSS variables don't error. If you build your own library, pick a private prefix. If you adopt one, grep for `--blob` first.

## Blending libraries in one component

Say flux makes the background gradient and siri makes the icon pulse:

```html
<div class="stage">
  <div class="wave wave-1"></div> <!-- flux bands -->
  <div class="wave wave-2"></div>
  <div class="wave wave-3"></div>
  <div class="wave wave-4"></div>
  <div class="siri-badge"> …blobs… </div>
</div>
```

```fscss
@flux-wave-preset(.stage)
@siri-wave-preset(.siri-badge)
```

The flux container provides the backdrop; siri-wave rides on top as a positioned element. Same page, two libraries, zero edits to either — this is the entire point of the ecosystem model.

## Rule of thumb for a component that fuses kits

1. Import both, call both token mixins once.
2. Give each library its own *scoped* anchor class.
3. Never let one library's preset output land on the same class as another's.

Do that and co-composition stays boring (which is what you want from infrastructure).

## Check your understanding

1. What stops flux-wave's tokens from wiping siri-wave's?
2. What would actually break if two libraries both shipped a `--blob-*` (same name, different meaning)?

Answer 1: separate namespaces — `--flux-*` vs `--siri-wave-*`/`--blob-*`. Answer 2: whichever sets the shared name last wins for both libraries, and one of them renders wrong — silently, no console error.

Next: [10 — Mini project: an assistant status dashboard](10-mini-project-siri-dashboard.md).