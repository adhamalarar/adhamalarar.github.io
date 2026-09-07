# Portfolio

Static site. Two files, no build step, no dependencies, no webfonts.

    index.html   all content, plus ~70 lines of vanilla JS at the bottom
    style.css    tokens, tile system, bento grid (light and dark follow the OS)
    cv.pdf       drop your CV here — the Download CV button links to it

## The idea

A bento grid: rounded tiles of varied widths on warm cream, styled after
<https://sitesplaced.com/s/demo-bento>. Tokens (radius 28px, `#faf7f2` page,
`#6366F1` indigo, uppercase micro-labels, 0.6s scroll reveal) are taken from that
reference's own design system.

The four layers you work across — Interface, Services, Data and AI,
Infrastructure — are tiles you can click. Clicking one filters the project tiles
to the work that touches that layer. Each project tile shows four dots, lit for
the layers it spans, using the same colours as the layer tiles.

## Editing

Search `index.html` for `TODO` — every spot that needs your words is marked.

- **Add a project:** copy a whole `<article class="tile ... tile-project">`. Set
  `data-layers` to the layers it touches (`interface services data infra`). That
  one attribute drives the filter, the dots, and the per-layer counts, all
  computed at load, so nothing goes stale.
- **Change a tile's width:** swap its `span2` / `span3` / `span4` class. The grid
  is six columns and collapses to two, then one, on narrow screens.
- **Add a role:** copy a `.tile-role` block. Newest first.

Colours are the custom properties at the top of `style.css`, with a dark set in
the `prefers-color-scheme` block below them.

## Preview locally

    python3 -m http.server -d . 8080    # http://localhost:8080

## Deploy (GitHub Pages)

    git init && git add -A && git commit -m "portfolio"
    gh repo create <your-username>.github.io --public --source=. --push

Then Settings → Pages → Source: `main`, folder `/`. Live at
`https://<your-username>.github.io` in about a minute.
