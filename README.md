# Christian Castelli's Blog

A [Hugo](https://gohugo.io/) site with the [Congo](https://jpanther.github.io/congo/) theme.
GitHub Actions builds the site and publishes it to GitHub Pages at <https://chrisvoo.github.io/>.

## Run locally

### Requirements

- [Hugo **extended**](https://gohugo.io/installation/) 0.158.0 or later (Congo needs SCSS)
- [Go](https://go.dev/dl/) (Hugo uses it to download the theme)
- Git

### Start the server

```bash
git clone git@github.com:chrisvoo/chrisvoo.github.io.git
cd chrisvoo.github.io
hugo server -D
```

Open <http://localhost:1313/>. The `-D` flag shows drafts. The page reloads when you save a file.

The first run downloads Congo as a Hugo Module. No `git submodule` step is needed.

## Write and publish a post

1. Create the post:

   ```bash
   hugo new posts/my-post-slug.md
   ```

2. Edit `content/posts/my-post-slug.md`. Set the front matter:

   ```yaml
   title: 'My Post Title'
   date: 2026-01-31T10:00:00
   draft: false          # draft = true hides the post in production
   tags: [hugo, blog]
   summary: One sentence for the post list.
   comments: true        # Giscus comments
   see_also:             # optional related posts
     - /posts/another-post-slug/
   ```

3. Put images in `static/images/uploads/`. Link them as `/images/uploads/<file>`.
4. Check the result with `hugo server -D`.
5. Publish:

   ```bash
   git add content/posts/my-post-slug.md static/images/uploads
   git commit -m "Add post: my post title"
   git push origin main
   ```

Each push to `main` starts the workflow `.github/workflows/deploy.yml`.
The site is live after about one minute. Watch progress in the repository's **Actions** tab.

To test the production build locally:

```bash
hugo --minify --baseURL "https://chrisvoo.github.io/" --buildFuture
```

## Write without a local setup (browser only)

The site includes [Sveltia CMS](https://github.com/sveltia/sveltia-cms) at
<https://chrisvoo.github.io/admin/>.
It reads and writes Markdown files in `content/posts/` through the GitHub API.
You need no server and no OAuth proxy. You need a Personal Access Token (PAT).

### Create the PAT

1. Open GitHub → **Settings → Developer settings → Personal access tokens → Fine-grained tokens**.
2. Click **Generate new token**.
3. Under **Repository access**, select only `chrisvoo/chrisvoo.github.io`.
4. Under **Repository permissions**, set **Contents** to **Read and write**.
5. Copy the token. GitHub shows it only once.

A classic token with the `repo` scope also works, but it has wider access.

### Publish a post

1. Open <https://chrisvoo.github.io/admin/>.
2. Choose **Sign In Using Token** and paste the PAT.
3. Open **Blog Posts** and click **New Blog Post**.
4. Fill in the fields and set **Draft** to off.
5. Click **Publish**.

Sveltia commits the file straight to `main`. The deploy workflow then publishes the site.
Do not share the token. Revoke it in GitHub if you lose it.

## Project layout

| Path | Purpose |
|---|---|
| `config/_default/` | Hugo and Congo settings (`hugo.toml`, `params.toml`, `menus.en.toml`, ...) |
| `content/` | Home page text and posts |
| `layouts/` | Template overrides, including the custom home page and Giscus comments |
| `assets/css/custom.css` | Custom styles |
| `static/` | Files copied as-is: images, `resume.pdf`, `admin/` (the CMS) |
| `.github/workflows/deploy.yml` | Build and deploy pipeline |

## Gotchas

- In `params.toml`, keep the `[author]` block **last**. TOML puts every key after a section header inside that section.
  Run `hugo config` to check where settings land.
- To override a Congo partial, copy it to `layouts/_partials/` (with the underscore).
- A local `_vendor/` folder makes Hugo ignore module updates. Delete it before you run `hugo mod get`.

## Update the theme

```bash
hugo mod get github.com/jpanther/congo/v2@latest && hugo mod tidy
```
