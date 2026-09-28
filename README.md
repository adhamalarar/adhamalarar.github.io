# Portfolio

Static site. No build step, no dependencies, no third-party requests.

    index.html            all content in both languages, plus ~110 lines of vanilla JS
    style.css             tokens, grid, the landmark figure, light and dark palettes
    font/archivo-latin.woff2   the one webfont, served from this domain
    cv.html               the English CV; edit this, then regenerate cv.pdf (see below)
    cv.pdf                generated from cv.html — what the CV button serves

## The idea

Otl Aicher drew the 1972 Munich pictograms by reducing a person to points joined
by segments on a 90/45 degree grid. MediaPipe pose landmarks do the same thing
fifty years later. The site is built in that system: flat colour fields, a strict
grid, no shadows or gradients, and a landmark figure as the only ornament.

The four layers — Interface, Perception, Services and data, Infrastructure — each
own a colour, and each owns a part of the figure: arms are the interface, the head
is perception, the torso is services, the legs are infrastructure. Clicking a layer
filters the projects and dims the rest of the figure to the part you selected.

Type is Archivo, one variable family carrying both weight and width. It is served
from this domain rather than from Google Fonts, so no visitor data leaves the site.

## Editing

**Both languages live in the markup.** English is the element's own text; German is
its `data-de` attribute. A German browser gets German first, the choice is
remembered, and there is nothing else to keep in sync:

    <p data-de="Neben dem Studium.">Alongside my studies.</p>

Search `index.html` for `TODO` — the availability band has two spots worth your
attention (start date, and whether to state your work authorisation).

- **Add a project:** copy an `<article class="project">`. Set `data-layers` to the
  layers it touches (`interface perception services infra`). That one attribute
  drives the filter, the landmark dots and the per-layer counts, all computed at
  load, so nothing goes stale.
- **Add a role:** copy an `.entry` block. Newest first.

Colours are the custom properties at the top of `style.css`, with a dark set below them.

## Light and dark

The button beside the language switch cycles **System → Light → Dark**. System follows
the OS, live; an explicit pick is stored in `localStorage` under `theme`.

`<html>` carries two attributes: `data-theme-choice` (what the visitor picked) and
`data-theme` (what that resolves to now, `light` or `dark`). A small script in `<head>`
sets both before the first paint, so there is no flash of the wrong theme.

The dark palette is written twice on purpose — under `prefers-color-scheme: dark` (also
what visitors without JavaScript get; the toggle ships `hidden`) and under
`[data-theme="dark"]` for an explicit pick. **Keep the two in sync.**

**When you edit `style.css`, bump the `?v=` on its `<link>` in `index.html`.** GitHub
Pages serves both under fixed URLs with `max-age=600`, so a returning visitor can
otherwise get new HTML with a stale stylesheet.

`cv.html` has no toggle: it follows the OS, and its dark palette is scoped to
`@media screen`, so `cv.pdf` is unaffected.

## Regenerating the CV PDF

Edit `cv.html`, then:

    google-chrome --headless --no-pdf-header-footer \
      --print-to-pdf=cv.pdf file://$PWD/cv.html

## Preview locally

    python3 -m http.server -d . 8080    # http://localhost:8080

## Deploy (GitHub Pages)

Push to `main`. Settings → Pages → Source: `main`, folder `/`. `CNAME` points the
site at alarar.de.
