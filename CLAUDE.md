# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Tom Ped's personal portfolio website — a **Jekyll** static site deployed via **GitHub Pages**
to the custom domain `tomped.com` (set in `CNAME`). No JavaScript build, no tests.

## Commands

```bash
bundle install              # install gems (first time)
bundle exec jekyll serve    # local dev server with live rebuild at http://localhost:4000
bundle exec jekyll build    # build static site into _site/
```

`_site/`, `Gemfile.lock`, and `.jekyll-cache/` are generated and gitignored — never edit them
by hand. GitHub Pages builds and deploys automatically on push to `master`.

To visually verify a change without a browser extension, render a page with headless Chrome
and open the resulting PNG:

```bash
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" \
  --headless --disable-gpu --hide-scrollbars --window-size=1200,1400 \
  --screenshot=/tmp/page.png "http://127.0.0.1:4000/work/"
```

## Architecture

- **Pages**: `index.html` (bio/landing) and `work/index.html` (portfolio list) are the two
  top-level pages. Each declares a `layout` in its front matter.
- **Layouts** (`_layouts/`): `default.html` is the base shell; `work.html` extends the same
  shell but appends the project list loop. Both pull in `head.html`, `header.html`, and
  `footer.html` from `_includes/`.
- **`_work` collection**: each project is one markdown file (`_work/1-Intuit.md`,
  `_work/2-Viibe.md`, …). Front matter has `title` and `link`; the body is the description.
  `work/index.html` iterates `site.work` and renders each project's `title` + `excerpt`.
  The numeric filename prefix controls display order. The collection is registered under
  `collections:` in `_config.yml`. **To add a project, add a numbered markdown file to
  `_work/` — no other file needs to change.**
- **Styling** (`_sass/` + `css/main.scss`): `main.scss` defines Sass variables then
  `@import`s the partials (`_base`, `_footer`, `_layout`, `_code-style`). Only `main.scss`
  carries front matter (required for Jekyll to process it). The palette is **Dracula**
  (`$background-color: #282a36`, `$brand-color: #bd93f9`, `$text-color: #f8f8f2`); base font
  is monospace to match the code-editor aesthetic.

## Content convention

Site copy is framed as **JavaScript console output**. Prose sections are introduced with a
`<pre><code class="javascript console-log">console.log(me.xxx);</code></pre>` block, and body
text follows. Syntax highlighting comes from `js/highlight.pack.js` (highlight.js), initialized
in `head.html` via `hljs.initHighlightingOnLoad()`. Preserve this motif when adding sections.

## Config notes

- `_config.yml` sets `title`, `url`, `linkedin_id`, and `github_username` (the last two drive
  the footer social icons). Changing `_config.yml` requires restarting `jekyll serve`.
- The `github-pages` gem is intentionally commented out in `Gemfile`; the site builds with
  plain `jekyll` locally while GitHub Pages supplies its own build environment.
