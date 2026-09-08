# Portfolio

Static site. Two files, no build step, no dependencies, no webfonts.

    index.html   all content, plus ~70 lines of vanilla JS at the bottom
    style.css    tokens, tile system, bento grid, light and dark palettes
    cv.html      the English CV; edit this, then regenerate cv.pdf (see below)
    cv.pdf       generated from cv.html — what the Download CV button serves

## The idea

A bento grid: rounded tiles of varied widths on warm cream, styled after
<https://sitesplaced.com/s/demo-bento>. Tokens (radius 28px, `#faf7f2` page,
`#6366F1` indigo, uppercase micro-labels, 0.6s scroll reveal) are taken from that
reference's own design system.

The four layers you work across — Interface, Perception, Services and data,
Infrastructure — are tiles you can click. Clicking one filters the project tiles
to the work that touches that layer. Each project tile shows four dots, lit for
the layers it spans, using the same colours as the layer tiles.

## Editing

Search `index.html` for `TODO` — every spot that needs your words is marked.

- **Add a project:** copy a whole `<article class="tile ... tile-project">`. Set
  `data-layers` to the layers it touches (`interface perception services infra`). That
  one attribute drives the filter, the dots, and the per-layer counts, all
  computed at load, so nothing goes stale.
- **Change a tile's width:** swap its `span2` / `span3` / `span4` class. The grid
  is six columns and collapses to two, then one, on narrow screens.
- **Add a role:** copy a `.tile-role` block. Newest first.

Colours are the custom properties at the top of `style.css`, with a dark set
below them.

## Light and dark

The pill in the top right cycles **System → Light → Dark**. System follows the
OS and keeps following it if the OS flips mid-visit; an explicit pick is stored
in `localStorage` under `theme` and nothing is stored while the pick is System.

Two attributes on `<html>` carry it: `data-theme-choice` is what the visitor
picked, and `data-theme` is what that resolves to right now (`light` or `dark`).
A small script in `<head>` sets both before the first paint, so a visitor who
picked dark never sees a flash of cream.

To restyle a theme, edit the palettes at the top of `style.css`. The dark one is
written twice on purpose — once under `prefers-color-scheme: dark` (which is
also what visitors without JavaScript get, since the toggle is hidden for them)
and once under `[data-theme="dark"]` for an explicit pick. **Keep the two in
sync.** Everything else in the stylesheet reads tokens only, so a tile, dot or
button added later themes itself; avoid inline colours in the JS for the same
reason.

`cv.html` has no toggle — it is a print document with no JavaScript, so it just
follows the OS, and its dark palette is scoped to `@media screen`. The generated
`cv.pdf` is unaffected.

## Regenerating the CV PDF

Edit `cv.html`, then:

    google-chrome --headless --no-pdf-header-footer \
      --print-to-pdf=cv.pdf file://$PWD/cv.html

## Preview locally

    python3 -m http.server -d . 8080    # http://localhost:8080

## Deploy (GitHub Pages)

    git init && git add -A && git commit -m "portfolio"
    gh repo create <your-username>.github.io --public --source=. --push

Then Settings → Pages → Source: `main`, folder `/`. Live at
`https://<your-username>.github.io` in about a minute.
