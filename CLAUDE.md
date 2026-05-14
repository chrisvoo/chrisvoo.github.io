# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
# Start the local dev server with drafts visible
hugo server -D

# Build the site for production
hugo --minify --baseURL "https://chrisvoo.github.io/blog/"

# Create a new post (generates content/posts/<name>.md from archetypes/default.md)
hugo new posts/<slug>.md

# Update the Congo theme to the latest version
hugo mod get github.com/jpanther/congo/v2@latest && hugo mod tidy

# Verify resolved config (useful for debugging param nesting issues)
hugo config
```

## Architecture

This is a [Hugo](https://gohugo.io/) static site using the [Congo](https://jpanther.github.io/congo/) theme (v2.13.0) loaded as a Hugo Module (Go module), not a git submodule. The site is deployed to GitHub Pages at `https://chrisvoo.github.io/blog/` via the workflow in `.github/workflows/deploy.yml` on every push to `main`.

### Config split (`config/_default/`)

| File | Purpose |
|---|---|
| `hugo.toml` | Core Hugo settings: `baseURL`, output formats, taxonomies |
| `params.toml` | Congo theme params: appearance, feature flags, section configs |
| `languages.en.toml` | Site title and per-language params |
| `menus.en.toml` | Nav menu items (Blog, GitHub, search action) |
| `module.toml` | Hugo Module import for the Congo theme |

### Critical TOML gotcha in `params.toml`

TOML section headers (`[author]`, `[header]`, etc.) capture **all subsequent bare keys** until the next section header — blank lines don't end a section. The `[author]` block must appear **last** in `params.toml`. If it's placed first, all top-level keys like `enableSearch`, `colorScheme`, etc. fall under `params.author.*` instead of `params.*`, silently breaking Congo features (search bar won't appear, color scheme ignored, etc.). Use `hugo config` to verify where params actually land.

### Theme overrides (`layouts/`)

Congo places its templates under `layouts/_partials/` (note the underscore prefix — this is a Hugo convention for "private" partials). To override a Congo partial, mirror the path under your site's `layouts/_partials/`. The `layouts/partials/` directory (no underscore) is for standard Hugo partials and does **not** override Congo's `_partials`.

Currently overridden:
- `layouts/_partials/functions/warnings.html` — suppresses outdated Congo config-rename warnings
- `layouts/comments.html` — Giscus comment widget (repo: `chrisvoo/blog`, category: "Blog comments")

### Search

Search is powered by Fuse.js (client-side, no server). Two things must be true for it to work:
1. `enableSearch = true` in `params.toml` (at root level, not nested under a section)
2. `home = ["HTML", "RSS", "JSON"]` in `hugo.toml` `[outputs]` — this makes Hugo render `index.json`, the search index Fuse.js fetches

The search index is visible at `/blog/index.json`.

### CMS

A Decap/Sveltia CMS is configured at `/admin` (`static/admin/`). It writes posts directly to `content/posts/` via the GitHub API against the `main` branch. Media uploads go to `static/images/uploads/` with a public path prefix of `/blog/images/uploads/` to account for the site's subpath `baseURL`.

### Content frontmatter

New posts use TOML frontmatter (`+++` delimiters, from `archetypes/default.md`). Posts support: `title`, `date`, `draft`, `tags`, `series`, `summary`, `comments` (bool, defaults to true for Giscus).
