---
title: 'Part 1: building a blog with GitHub - Configuring Hugo'
date: 2026-05-18T20:34:00
draft: false
tags:
  - blog
  - cms
  - github
series: ''
summary: First part of a series of post for building a blog with GitHub
comments: true
---

![A woman writing a blog post about how to create a blog](/images/uploads/Gemini_Generated_Image_sy58jjsy58jjsy58.png)

It's been passed a long time since I had a blog. In an attempt to start again to write and keep interesting things as my memories, something that could be useful for both myself and others bumping into it, I looked for a cheap but effective solution. Here is the one I'm currently using.

## Stack

| Topic | Choice | Why |
| --- | --- | --- |
| Static site generator | [Hugo](https://gohugo.io) | Fastest builds, 400+ themes, Go binary |
| Theme | [Congo](https://github.com/jpanther/congo) | Feature-rich, Tailwind-based, active maintenance, live demos at [jpanther.github.io/congo](jpanther.github.io/congo) |
| CMS | [Sveltia CMS](https://sveltiacms.app/en) | Modern Decap CMS drop-in; GitHub Access Token auth; works on GitHub Pages with zero backend |
| Auth | [GitHub PAT](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens) | Personal Access Token stored in browser — no proxy, no Cloudflare, no OAuth app needed |
| Comments | [Giscus](https://giscus.app) | GitHub Discussions-backed, no DB, GitHub OAuth for visitors |

## Set up

### 
Prerequisites

Start installing [Go](https://go.dev/doc/install), gh and Hugo. The following command will use Brew since I'm using a Mac:

```plain
brew install hugo go gh

hugo version   # must show hugo v0.x.x+extended
go version     # needed for Hugo Modules (Congo install method)
```

[gh](https://cli.github.com) (the GitHbB CLI) is certainly optional, but it's pretty good and fast operate with it instead of searching for the things you need in GitHub settings.

### 1. Repository & Hugo Scaffold

Let's create and clone a new GitHub repo and then create the basic blog structure in the directory:

```plain
gh repo create chrisvoo/blog --public --clone
cd blog

# Scaffold Hugo site (--force because dir has .git)
hugo new site . --force
```

### 
2. Congo Theme via Hugo Modules

Hugo Modules is the preferred Congo install method (easier upgrades than submodules). The following snippets perfectly reflects this website's configuration, so it already acts as a demo.

- Initialize the module:

```sh
hugo mod init github.com/chrisvoo/blog
```

- Create `config/_default/module.toml`

```toml
[[imports]]
path = "github.com/jpanther/congo/v2"
```

Then pull it:

```toml
hugo mod get -u
```

Congo uses a split config layout under `config/_default/`:

```plain
config/
└── _default/
    ├── hugo.toml           ← site identity + baseURL
    ├── params.toml         ← theme features
    ├── languages.en.toml   ← title, tagline, menu labels
    ├── menus.en.toml       ← nav items
    ├── markup.toml         ← code highlight settings
    └── taxonomies.toml     ← tags, categories, series
```

Let's modify the configuration (`config/_default/hugo.toml`) for base URL, locale, search bar and tags:

```toml
baseURL = "https://chrisvoo.github.io/blog/"
locale = "en"
defaultContentLanguage = "en"
enableRobotsTXT = true

[outputs]
  home = ["HTML", "RSS", "JSON"]   # JSON required for Fuse.js search index

[taxonomies]
tag = "tags"
category = "categories"
series = "series"
```

Now the params (`config/_default/params.toml`) for defining the peculiarities of header, footer and articles:

```toml
colorScheme = "congo"         # built-in palette; others: avocado, cherry, fire, ocean, slate
defaultAppearance = "dark"    # auto | light | dark
autoSwitchAppearance = true

enableSearch = true
enableCodeCopy = true
enableImageLazyLoading = true
enableImageWebp = true

showReadingTime = true
showBreadcrumbs = true
showTableOfContents = true
showAuthor = true
showDate = true
showWordCount = false

[header]
  showTitle = true
  layout = "hybrid"

[footer]
  showAppearanceSwitcher = true
  showScrollToTop = true

[article]
  showDate = true
  showTaxonomies = true
  showComments = true
  showRelatedContent = true
  sharingLinks = ["linkedin", "email", "telegram", "reddit", "facebook", "mastodon"]

[list]
  showSummary = true
  showBreadcrumbs = true
  showTaxonomies = true
  groupByYear = true

[homepage]
  layout = "page"
  showRecent = true
  recentLimit = 5

[sitemap]
  excludedKinds = []

[author]
  name = "Christian Castelli"
```

Something about the language and the website's title (`config/_default/languages.en.toml`):

```toml
title = "The Castles"
label = "English"

[params]
  description = "Thoughts on software, tech, and whatever."
```

Finally the menu items. The search icon in the nav requires a menu entry with `action = "search"`. Without this, the search bar never renders even if `enableSearch = true`.

```toml
[[main]]
  name = "Blog"
  pageRef = "posts"
  weight = 10

[[main]]
  name = "GitHub"
  url = "https://github.com/chrisvoo"
  weight = 30
  [main.params]
    icon = "github"
    showName = false
    target = "_blank"

[[main]]
  identifier = "search"
  weight = 99
  [main.params]
    action = "search"
    icon = "search"
```

Putting the code below in `content/_index.md`,  prevent Congo's `page` homepage layout from rendering a duplicate `<h1>` with the site title (which also appears in the header logo). An empty frontmatter file suppresses the title in the page body while keeping the recent-posts list.

```markdown
---
draft: false
---
```

The following blog posts will explain the remaining parts to be describe. 🤘
