# atomvm.org

The public website for [AtomVM](https://github.com/atomvm/AtomVM), published at <https://atomvm.org> via GitHub Pages.

# Prerequisites

To build the site locally you will need:

* [Ruby 2.7+](https://www.ruby-lang.org/en/)
* [Bundler](https://bundler.io)

# Instructions

Change your working directory to the directory containing this README:

    cd <path-to-this-directory>

Create a `Gemfile` (intentionally not committed) with the following content:

```ruby
source "https://rubygems.org"

gem "github-pages", group: :jekyll_plugins
gem "webrick", "~> 1.7"  # Required for Ruby 3.0+
```

Create a `_config_dev.yml` file for local development:

```yaml
host: "127.0.0.1"
port: 4000
url: "http://localhost:4000"
```

[Optional] Install gems locally under `vendor/bundle`:

    bundle config set --local path 'vendor/bundle'

Install all needed gems (may take some time):

    bundle install

Serve the site locally on <http://127.0.0.1:4000>:

    bundle exec jekyll serve --host 127.0.0.1 --config _config.yml,_config_dev.yml

Or build to `_site/`:

    bundle exec jekyll build [-d <path-to-output>] [--watch]

# Site structure

The site uses a small custom Jekyll theme that lives directly in this repository (no `remote_theme:`, no separate theme repo). The relevant directories are:

```
_config.yml       Jekyll config (sass + defaults).
_data/home.yml    Navbar, hero, feature cards, news config, footer columns.
_includes/        Reusable HTML fragments (head, navbar, drawer, footer, cards).
_layouts/         Page templates: default, home, news, post, page.
_sass/            SCSS partials (tokens, themes, layout, components).
assets/css/       Single SCSS entry (main.scss) compiled by Jekyll.
assets/js/        Single ~25 LOC theme.js (light/dark toggle + drawer).
_posts/           News posts (YYYY-MM-DD-Title.md).
```

Design tokens (colors, typography, radius, stroke) live as CSS custom properties in `_sass/_tokens.scss`. Light and dark themes are defined as variable overrides in `_sass/_themes.scss`; the active theme is controlled by a `data-theme` attribute on `<html>` that respects `prefers-color-scheme` and persists user choice in `localStorage`. The footer band is locked to light colours regardless of theme.

Fonts are loaded once from Google Fonts (Manrope variable + IBM Plex Mono — both OFL-licensed).

The Extensions, Sample Code, Tutorials and 404 pages use the same `page` layout as a thin variant of the news-post layout — they were not designed separately; treat them as derived templates.

# Add a news post

Create a file in `_posts/` named `YYYY-MM-DD-Your-Title.md` with this front matter:

```markdown
---
layout: post
title: Your Headline
author: Optional Author Name
excerpt_separator: <!--more-->
---

Short intro shown on the /news/ list and in the home page news teaser.

<!--more-->

Full article body shown only on the post's own page.
```

The `<!--more-->` marker separates the news-index excerpt from the full article. If you omit it, the whole body is used as the excerpt.

# Notable conventions

- `_data/home.yml` is the single source of truth for navbar entries, hero text/buttons, feature cards, news teaser config, and footer content. Editing copy on the home page does not require touching `index.md`.
- Adding a new top-level page is two steps: create `mypage.md` with `title:` + `permalink:` in front matter (no `layout:` needed — the `defaults` rule applies `layout: page`), and link to it from `_data/home.yml` if it should appear in the navbar or footer.
- `bundle exec jekyll build` is the only build step. No Node.js, no npm, no Tailwind.
