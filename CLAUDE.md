# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A Jekyll-based slideshow site that wraps [reveal.js](https://github.com/hakimel/reveal.js) (vendored via `node_modules/reveal.js`, version pinned in `package-lock.json`). It is designed to be served via GitHub Pages and is used as the sample repo for the GitHub Learning Lab "Introduction to GitHub" course. There is no application code — authoring a slideshow means adding Markdown posts and tweaking Jekyll config.

## Commands

All scripts live in `script/` and follow the "scripts to rule them all" pattern.

- `script/setup` — install Ruby gems via Bundler and initialize git submodules. Run once after cloning.
- `script/server` — `bundle exec jekyll serve`; extra args are passed through (e.g. `script/server --drafts`).
- `script/cibuild` — builds the site with `bundle exec jekyll build --baseurl "."` and runs `htmlproofer _site/index.html --empty-alt-ignore`. Use this to validate before pushing.
- `script/stage [repo-name]` — internal staging deploy; pushes the built `_site/` as a `gh-pages` branch to `ghe.io/training-staging/<repo>`. Not for general use.

There is no test suite; `htmlproofer` inside `script/cibuild` is the only automated check.

## Architecture

The site is assembled at build time by Jekyll from a small set of pieces:

- `index.html` is the single page. It iterates `site.posts` in reverse and renders each via `_includes/slide.html`, producing one `<section class="step">` per post inside the reveal.js `.slides` container (`_layouts/presentation.html`). Each post becomes one slide.
- `_posts/` holds slide content. Filenames follow Jekyll's `YYYY-MM-DD-title.md` convention; the existing `0000-01-01-intro.md` uses a sentinel date so it sorts first when posts are reversed. Front matter `layout: slide` is required; optional `slide-id`, `classes`, and `data` keys control the `<section>`'s id, classes, and `data-*` attributes (see `_includes/slide.html`).
- `_layouts/presentation.html` is the layout used by `index.html` (full reveal.js deck). `_layouts/slide.html` renders a single post standalone (permalinks are set to `/:title` in `_config.yml`). `_layouts/print.html` exists for PDF export.
- `_includes/head.html` pulls reveal.js CSS from `node_modules/reveal.js/...` at runtime (paths are prefixed with `site.baseurl`). `_includes/script.html` calls `Reveal.initialize` with the markdown/highlight/notes plugins. Reveal.js options in `_config.yml` under the `reveal:` key are **not** wired through to `Reveal.initialize` — they are documentation; actual runtime config is hardcoded in `_includes/script.html`.
- `_config.yml` sets `permalink: /:title`, `markdown: kramdown`, `highlighter: rouge`, and excludes `reveal.js/*` paths (a leftover from when reveal.js was a submodule; today it is vendored under `node_modules/`).

### Adding a slide

Create `_posts/<date>-<slug>.md` with front matter `layout: slide` and a `title:`. Markdown body becomes the slide body. Posts with future dates will not appear (`future: false` in `_config.yml`, `timezone: Europe/Berlin`).

## Conventions

- `.editorconfig` enforces: tabs (size 4) by default; 2-space indentation for `json/js/css/scss/yml/htm/html`; 4-space indentation and trailing newline for Markdown. Trailing whitespace is trimmed except in Markdown.
- `.gitmodules` exists but is empty; `reveal.js` is no longer a submodule despite `script/setup` still running `git submodule update --init`.
- `_site/`, `.sass-cache/`, `.jekyll-metadata`, and `.bundle` are gitignored build artifacts — don't commit them.

## Branch for this work

Per task instructions, develop on `claude/add-claude-documentation-UpPua` and push there.
