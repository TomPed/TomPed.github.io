# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Tom Ped's personal portfolio — a **Jekyll** static site on **GitHub Pages** at the custom domain
`www.tomped.com` (`CNAME`). No JavaScript build, no tests, no runtime JS at all. It's a
**two-sided "folder" site**: a terminal-style landing page branches into two audiences.

## Commands

```bash
bundle install              # install gems (first time)
bundle exec jekyll serve    # local dev server with live rebuild at http://localhost:4000
bundle exec jekyll build    # build static site into _site/
```

`_site/`, `Gemfile.lock`, and `.jekyll-cache/` are generated and gitignored — never edit them by
hand. Classic GitHub Pages builds and deploys from the **`master`** branch (no Actions workflow),
so changes only go live when merged there.

To visually verify a change (no browser extension needed), render a page with headless Chrome and
open the resulting PNG with the Read tool:

```bash
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" \
  --headless=new --disable-gpu --hide-scrollbars --window-size=1200,1600 \
  --screenshot=/tmp/page.png "http://127.0.0.1:4000/resume/"
```

Note: headless captures at scroll-top (fragment-scroll screenshots come back blank), and macOS
Chrome has a ~500px min window width — test "mobile" at 500px+, not 390.

## Architecture

Three sections, each with its own layout, header, and SCSS partial, sharing one theme:

- **Landing** — `index.html` (`layout: landing`): a terminal `tree` chooser linking to `/studio/`
  and `/resume/`. No section header. `404.html` reuses this same terminal look.
- **`/resume/`** — `resume/index.html` (`layout: resume`): a mini-résumé. A `const me = { … }`
  block, then sections introduced by **pseudo-code method-call headings** (`me.summary()`,
  `me.experience()`, …). Experience is a flat tagged-highlight list; Related work uses two-column
  `.exp-entry` cards; Skills are grouped tag pills.
- **`/studio/`** — `studio/index.html` (`layout: studio`): a client-facing freelance landing —
  hero, "how it works" process cards, a portfolio gallery (the `_sites` collection), and a mailto
  CTA.

Supporting pieces:
- **Layouts** (`_layouts/`): `landing.html`, `resume.html`, `studio.html`. All include
  `head.html` + `footer.html`; resume/studio also include `resume-header.html` / `studio-header.html`
  (a `cd /tomped` breadcrumb back to the landing + a brand bar).
- **Collections** (`_config.yml`): `sites` (studio gallery — `_sites/N-*.md`, front matter
  `title`/`link`/`image`/`tag`, iterated on the studio page) and `work` (legacy personal projects
  in `_work/`, currently `output: false` and not rendered — kept for possible reuse). Numeric
  filename prefixes control order.
- `work/index.html` is a redirect stub preserving the old `/work/` URL → `/resume/`.

## Styling (`_sass/` + `css/main.scss`) — Sass module system

- `_sass/_shared.scss` is the hub: all variables, the `media-query` mixin, and the shared
  placeholders (`%card`, `%card-lift`, `%spine`, `%clearfix`). Every partial starts with
  `@use "shared" as *;`, and `css/main.scss` pulls the partials together with `@use` (modern
  module system — **no `@import`**, so the build is deprecation-warning free). Only `main.scss`
  carries front matter.
- Palette is **Dracula** (`$background-color: #282a36`, `$brand-color: #bd93f9`,
  `$text-color: #f8f8f2`); base font is monospace. Cards are raised via `%card` + soft shadows;
  sections get a quiet vertical accent via `%spine`. The section header is `position: sticky`
  (defined once in `_base.scss` — don't set `position` on `.site-header` elsewhere or it overrides).
- Avoid Sass `/` division (deprecated) — use `* 0.5` etc. Use `color.adjust(...)`, not
  `lighten()`/`darken()`.

## Content convention

Copy is framed as **pseudo-code**. The landing is a terminal `tree`; the resume uses a `const me`
object and `me.method()` section headings. Code blocks are **hand-colored with spans** —
`<pre class="code"><code>…<span class="k">const</span>… <span class="s">'string'</span>… <span class="n">30</span></code></pre>`
(`.k` keyword, `.s` string, `.n` number), styled in `_base.scss`. There is intentionally **no
highlight.js / runtime JS** — keep new code blocks hand-colored the same way.

## Config notes

- `_config.yml`: `url: https://www.tomped.com` (matches `CNAME`), `title`, `linkedin_id`,
  `github_username` (the last two drive the footer icons). Changing `_config.yml` requires
  restarting `jekyll serve`.
- The `github-pages` gem is intentionally commented out in `Gemfile`; the site builds with plain
  `jekyll` locally while GitHub Pages supplies its own build environment. The site uses no plugins.
