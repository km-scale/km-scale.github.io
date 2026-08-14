# km-scale.github.io

Homepage for km-scale modeling and the km-scale hackathons, published at
[km-scale.github.io](https://km-scale.github.io/).

Built with [Hugo](https://gohugo.io/) using the
[hugo-scroll](https://github.com/zjedi/hugo-scroll) theme (a single
scrolling page). The look is based on the
[2025 Digital Earths Global Hackathon page](https://digital-earths-global-hackathon.github.io/hk25/).

## Running locally

The theme is a git submodule, so clone with `--recurse-submodules`, or run
after the fact:

```sh
git submodule update --init --recursive
```

Then, with Hugo installed (or via [uv](https://docs.astral.sh/uv/)):

```sh
hugo server # use uvx hugo server to run via uv
```

Open the url displayed by hugo

## Editing content

- **Header banner** (title/subtitle at the top): `content/_index.md` front
  matter (`header_headline`, `header_subheadline`).
- **Page sections** (the scrolling cards, and any menu buttons pointing to
  them): add a `.md` file under `content/homepage/`. See existing files
  there for examples.
- **Footer/legal links**: also `content/homepage/*.md` files — see
  `legal.md` and `privacy.md`.
- **Colors**: overridden in `layouts/partials/custom_head.html`.

## Deployment

Publishing is automatic: pushing to `main` runs
[`.github/workflows/hugo.yml`](.github/workflows/hugo.yml), which builds
the site with Hugo and deploys it via GitHub Pages. Pull requests trigger
the same build for validation, without deploying.

Repo setting required (already set): **Settings → Pages → Source: GitHub
Actions**.
