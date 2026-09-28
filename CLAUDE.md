# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

Read `README.md` first — it covers the design concept, how to add a project or role,
and how to regenerate `cv.pdf`. This file only adds what the README leaves implicit.

## Commands

    python3 -m http.server -d . 8080     # preview at http://localhost:8080

    google-chrome --headless --no-pdf-header-footer \
      --print-to-pdf=cv.pdf file://$PWD/cv.html    # after editing cv.html

No build, no package manager, no tests, no linter. Deploy = push to `main` (GitHub Pages, `CNAME` → alarar.de).

## Architecture

Three self-contained files. `index.html` holds all markup plus the script (one IIFE at
the bottom, and a one-line `<head>` script that sets `html.js`); `style.css` holds every
style; `cv.html` is a separate English-only page with its own inline `<style>` and no shared
CSS — it does not track `index.html`, so a CV change must be made in both by hand.

Keep it dependency-free and request-free: the font is local, there are no trackers, no
CDN links, no frameworks. Do not introduce a build step or a library.

### The two invariants

**1. `data-layers` is the single source of truth for projects.** An `<article class="project">`
declares which of the four layers (`interface perception services infra`) it touches. `render()`
derives from it: the filter, the per-layer counts, the disabled state of a layer button, the
status line, and the landmark dots in `.side`. Nothing is written twice — never hardcode a count
or a dot. `.side` and `.layer-count` are overwritten on every render, so anything hand-written
there is lost. A layer with zero projects is disabled and labelled `words.internship`; the layer
names shown in dots and the status line are read from the button's `.layer-name`.

**2. Bilingual text lives entirely in the markup.** English is the element's own text, German is
its `data-de` attribute; the script snapshots `innerHTML` into `data-en` at load and swaps on
language change. Consequences worth knowing:

- Any user-visible string added to the markup needs a `data-de` sibling or it will not translate.
- Strings the *script* generates ("3 projects", "in") live in the `words` object — both languages there.
- The swap uses `innerHTML`, so `data-de` may contain markup but must never contain untrusted input.
- The page `<title>` swaps via `body[data-title-de]`.

### The figure

The SVG `.fig` has one `<g data-part="...">` per layer (arms = interface, head = perception,
torso = services, legs = infrastructure). Filtering sets `fig.dataset.active`; CSS alone dims
the other parts (the `.fig[data-active]` rules in `style.css`; the dim rule must stay before the lit ones). The draw-in animation is gated on `html.js` and disabled
under `prefers-reduced-motion`, so the site renders fully without JavaScript.

### Colour

The four layer colours are custom properties (`--interface`, `--perception`, `--services`,
`--infra`) with a dark-scheme override in the `prefers-color-scheme` block. JS references them
only as `var(--<layer>)`, keyed off the same layer names — renaming a layer means changing the
token, the `data-layer`, and every `data-layers` value together.
