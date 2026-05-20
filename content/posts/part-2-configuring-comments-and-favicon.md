---
title: 'Part 2: configuring comments and favicon'
date: 2026-05-20T20:49:00
draft: false
tags:
  - blog
  - github
series: ''
summary: Enabling comments under your posts and create a custom favicon
comments: true
---

![LIKE - Photo by https://unsplash.com/@matscha](https://images.unsplash.com/photo-1564416437164-e2d131e7ec07?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w5NTY3NTF8MHwxfHNlYXJjaHwxMTh8fGNvbW1lbnR8ZW58MHx8fHwxNzc5MzA2MTU0fDA&ixlib=rb-4.1.0&q=80&w=1080)

In this part we'll enable the possibility for the user with a Github account to comment our posts. We'll use [Giscus](https://giscus.app/) for this.

💡 Read [Part 1: building a blog with GitHub - Configuring Hugo](https://chrisvoo.github.io/posts/part-1-building-blog-github-configuring-hugo/) if you've missed it.

Since Congo, the Hugo template used in the first part of this tutorial, does **not** have built-in Giscus config keys, we'll wire it via a custom partial.

### Enable Discussions on the repo

1. `github.com/chrisvoo/chrisvoo.github.io` → Settings → General → check **Discussions**
2. Discussions tab → Categories
3. Click the pencil/edit icon next to Categories → New category and add **Blog Comments** (type: Open-ended discussion)
4. Install Giscus app: [https://github.com/apps/giscus](https://github.com/apps/giscus) → select `chrisvoo/chrisvoo.github.io`
5. Visit [https://giscus.app](https://giscus.app/), enter `chrisvoo/chrisvoo.github.io` → copy `data-repo-id` and `data-category-id`

As you can see, Giscus offer a dynamic way to populate the script parts necessary to enable this feature.

#### Create `layouts/_partials/comments.html`

💬 Note: Congo uses `_partials/` (with underscore prefix) for all its override hooks, checked via `templates.Exists "_partials/comments.html"`. A file at `layouts/partials/comments.html` (no underscore) is invisible to this check and the warning `[CONGO] Comments are enabled but no comments partial exists` will keep firing.

```html
{{ if .Params.comments | default true }}
<script src="https://giscus.app/client.js"
        data-repo="chrisvoo/chrisvoo.github.io"
        data-repo-id="<REPO_ID>"
        data-category="Blog comments"
        data-category-id="<CAT_ID>"
        data-mapping="pathname"
        data-strict="0"
        data-reactions-enabled="1"
        data-emit-metadata="0"
        data-input-position="bottom"
        data-theme="preferred_color_scheme"
        data-lang="en"
        crossorigin="anonymous"
        async>
</script>
{{ end }}
```

Replace the placeholder above with the values generated on Giscus website. To disable comments on a specific post, add `comments: false` to its frontmatter. If everything works, you'll see the comment form below the post

### Favicons

Create the necessary files by yourself or denerate a full favicon set at [favicon.io](https://favicon.io/) or [realfavicongenerator.net](https://realfavicongenerator.net/) and then place all files under `assets/favicon/` (not `static/`) so Hugo Pipes manages them. Expected files:

```plain
assets/favicon/
  favicon.ico
  favicon.svg
  favicon-96x96.png
  apple-touch-icon.png
  web-app-manifest-192x192.png
  web-app-manifest-512x512.png
  site.webmanifest
```

#### assets/favicon/site.webmanifest

This is my manifest file. The manifest's `src` values are resolved from the domain root, not the Hugo baseURL

```json
{
  "name": "The Castles",
  "short_name": "The Castles",
  "icons": [
    {
      "src": "/favicon/android-chrome-192x192.png",
      "sizes": "192x192",
      "type": "image/png"
    },
    {
      "src": "/favicon/android-chrome-512x512.png",
      "sizes": "512x512",
      "type": "image/png"
    }
  ],
  "theme_color": "#ffffff",
  "background_color": "#ffffff",
  "display": "standalone"
}
```

#### layouts/_partials/favicons.html

Congo's `head.html` checks `templates.Exists "_partials/favicons.html"` — if it exists, Congo delegates all favicon `<link>` tags to it entirely. Files in `assets/` are only emitted to `public/` when referenced via `resources.Get`.

```html
{{- $ico := resources.Get "favicon/favicon.ico" -}}
{{- $svg := resources.Get "favicon/favicon.svg" -}}
{{- $png96 := resources.Get "favicon/favicon-96x96.png" -}}
{{- $apple := resources.Get "favicon/apple-touch-icon.png" -}}
{{- $manifest := resources.Get "favicon/site.webmanifest" -}}
<link rel="icon" href="{{ $ico.RelPermalink }}" sizes="32x32" />
<link rel="icon" href="{{ $svg.RelPermalink }}" type="image/svg+xml" />
<link rel="icon" type="image/png" sizes="96x96" href="{{ $png96.RelPermalink }}" />
<link rel="apple-touch-icon" href="{{ $apple.RelPermalink }}" />
<link rel="manifest" href="{{ $manifest.RelPermalink }}" />
```

Verify after build: `grep favicon public/index.html` should show five `<link>` tags
