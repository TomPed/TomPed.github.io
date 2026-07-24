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
- **Collection** (`_config.yml`): `sites` — the studio gallery. Each `_sites/N-*.md` has front
  matter `title`/`link`/`image`/`tag`, plus optional `soon: true` to render a dimmed "Coming soon"
  card (no link). Iterated on the studio page; numeric filename prefixes control order.
- `work/index.html` is a redirect stub preserving the old `/work/` URL → `/resume/`.

## Styling (`_sass/` + `css/main.scss`)

- `css/main.scss` defines all variables + the `media-query` mixin, then `@import`s the partials
  (`base`, `footer`, `layout`, `landing`, `resume`, `studio`). Shared placeholders (`%card`,
  `%card-lift`, `%spine`, `%clearfix`) live in `_base.scss` and are `@extend`ed by the others.
  Only `main.scss` carries front matter.
- **⚠️ GitHub Pages builds with libsass (Jekyll 3.9), which does NOT support the Dart Sass module
  system.** Never use `@use` / `@forward` (or `color.adjust`, `math.div`, `sass:*` modules): they
  compile locally on Jekyll 4 / Dart Sass but ship **uncompiled** on Pages — `main.css` becomes
  literal `@use "base";…` and the live site loses all CSS (this happened once — see git history).
  Stick to `@import` + global variables + `lighten()`/`darken()`; use `* 0.5` instead of `/`
  division. The Dart Sass **deprecation warnings** shown locally (`@import`, `lighten`, `/`) are
  cosmetic — ignore them; they don't affect the libsass prod build. (To use modern Sass safely
  you'd switch Pages to a GitHub Actions build with Jekyll 4 + Dart Sass — a possible future
  change, not set up.)
- Palette is **Dracula** (`$background-color: #282a36`, `$brand-color: #bd93f9`,
  `$text-color: #f8f8f2`); base font is monospace. Cards are raised via `%card` + soft shadows;
  sections get a quiet vertical accent via `%spine`. The section header is `position: sticky`
  (defined once in `_base.scss` — don't set `position` on `.site-header` elsewhere), and
  `html { scroll-padding-top }` offsets `#anchor` jumps so they clear the sticky header.

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
