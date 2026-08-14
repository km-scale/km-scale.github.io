# AGENTS.md

Static Hugo site for km-scale.github.io, deployed via GitHub Actions to
GitHub Pages. No package manager, no build step beyond Hugo itself.

## Stack

- Generator: [Hugo](https://gohugo.io/) (extended), config in `hugo.toml`.
- Theme: [`hugo-scroll`](https://github.com/zjedi/hugo-scroll), vendored as
  a git submodule at `themes/hugo-scroll`. Do not edit files under
  `themes/hugo-scroll/` — override via `layouts/` or `assets/` at the repo
  root instead (Hugo's filesystem overlay: site-root files of the same
  path win over the theme's).
- `hugo.toml` sets `disableKinds = ["section", ...]` — this is a one-page
  site; regular Hugo sections/list pages are intentionally not built.

## Content model (hugo-scroll specifics)

- `content/_index.md` — front matter only (`header_headline`,
  `header_subheadline`, `header_image`, ...). Its markdown **body is never
  rendered** by this theme; don't put real content there.
- `content/homepage/` is a **headless leaf bundle**: it must contain its
  own `index.md` with `headless: true`. Every sibling `.md` file in that
  directory becomes a page-resource the theme's `layouts/_default/index.html`
  iterates over — that's how scrolling sections, top-menu buttons, and
  footer links are all built. Without `content/homepage/index.md`, sibling
  files are silently invisible (they're treated as an ordinary section,
  not bundle resources).
- Per-file front matter on `content/homepage/*.md`:
  - `weight` — ordering (lower first).
  - `header_menu: true` — adds a button to the top bar.
  - `footer_menu: true` / `footer_menu_title` — adds a footer link.
  - `external: "<url>"` — top-bar button links directly to `<url>` instead
    of scrolling to the section, **and removes the section from the
    scrolling page + sticky side-nav** (mutually exclusive with being a
    visible section).
  - `detailed_page_path: "<path-or-url>"` — used by `header_menu`/
    `footer_menu` links as the target instead of an in-page anchor.
    Combine with `detailed_page_homepage_content: false` to make the file
    a nav-only stub (not shown as a scrolling section). Hugo's
    `relLangURL`/passthrough treats an absolute `https://` value here as
    an external link, unchanged — see `legal.md`/`privacy.md` for the
    pattern.
  - A top-level `content/<slug>.md` (outside `homepage/`) renders as a
    normal single page at `/<slug>/` via
    `themes/hugo-scroll/layouts/_default/single.html` — used for stub
    pages that a `detailed_page_path` in `content/homepage/` points to.
- Color/style overrides live in `layouts/partials/custom_head.html`
  (`:root { --var: ...; }`, matching the CSS custom properties defined in
  `themes/hugo-scroll/assets/css/variables.scss`), not by copying theme
  templates.

## Commands

```sh
git submodule update --init --recursive   # required after clone/pull
hugo server                                # local preview; prints the URL to open
hugo --minify                              # production build -> ./public
```

No `hugo` install? Run `uvx hugo server` / `uvx hugo --minify` instead —
`uv` fetches Hugo on the fly and it behaves identically.

## CI/CD

`.github/workflows/hugo.yml`:
- `push` to `main` → build and deploy to GitHub Pages.
- `pull_request` targeting `main` → build only (validates the site still
  builds; no deploy — gated by `if: github.event_name == 'push'` on the
  deploy job).
- Repo must have **Settings → Pages → Source: GitHub Actions** (not
  "Deploy from a branch" — that path runs Jekyll instead and will mangle
  `_index.md`-style paths).

## Conventions

- Keep this file updated when the content model, theme, or CI workflow
  changes.
