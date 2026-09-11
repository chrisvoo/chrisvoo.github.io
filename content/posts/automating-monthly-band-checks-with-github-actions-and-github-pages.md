---
title: Automating Monthly Band Checks with GitHub Actions and GitHub Pages
date: 2026-09-11T22:28:00
draft: true
tags:
  - musicbrainz
  - api
  - github
summary: |-
  This post walks through setting up a Node.js script to run automatically on a monthly schedule via GitHub Actions, commit its updated state back to the repo, and publish a formatted results page to GitHub Pages.

  The example uses a script that polls the MusicBrainz API to detect new album releases for a tracked list of bands — but the pattern applies to any Node.js script that produces structured output you want to share.
comments: true
see_also: []
---

![Photo by https://unsplash.com/@fandrejevic](https://images.unsplash.com/photo-1604177052603-c2b4cff228db?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w5NTY3NTF8MHwxfHNlYXJjaHw4fHxoZWF2eSUyMG1ldGFsfGVufDB8fHx8MTc4OTE2MzA3Nnww&ixlib=rb-4.1.0&q=80&w=1080)

So...sometimes you'd like to know if one of your favourite bands has rolled out a new album. A long time ago I just crafted a [small JSON file](https://github.com/chrisvoo/bands_update/blob/main/checks.json) as a small database, a list of objects where each one represents a band and contains its latest album and [MusicBrainz](https://musicbrainz.org/) ID (plus other info as underlined by [its schema](https://github.com/chrisvoo/bands_update/blob/main/checks.schema.json)).
Then, once in a while, I run the script to see if there's something new.  
Today I took the chance of some free time to unleash Claude a bit and pump up the original script:

- better terminal interface
- backoff retry strategy when interacting with MusicBrainz 
- partial runs save their results in the JSON file

One of the last things missing was automatise this process on a scheduled basis. So our friend GitHub came to the rescue even this time.

**What we're building**

- A GitHub Actions workflow that runs on the 1st of every month
- The workflow commits updated state (checks.json) back to main branch
- It generates [a static HTML results page and deploys it to GitHub Pages](https://chrisvoo.github.io/bands_update/)

**Prerequisites**

- A Node.js project in a GitHub repo
- Node 22+ locally and in CI 
- The repo can be public or private (GitHub Actions free tier gives 2,000 minutes/month for private repos — one monthly 10-minute run costs nothing)

### Step 1 — Emit structured results from your script

Basically, this simple block of code:

```javascript
// before this, looping objects and querying MusicBrainz...
try {
   const payload = {
      date: TODAY,
      elapsed_seconds: (Date.now() - startTime) / 1000,
      bands_total: entries.length,
      findings: results,
   };
   await writeFile(RESULTS_FILE, JSON.stringify(payload, null, 2), 'utf8');
} catch (err) {
   console.error(red('Warning: failed to write results.json:'), err.message);
}
```

where `results.txt` contains the records that will be displayed on GitHub Pages.

### Step 2 — Create the HTML page generator

This is basically [this file](https://github.com/chrisvoo/bands_update/blob/main/generate-page.mjs) reading our results, and composing an HTML file inside a `site` folder. This file will be run by our scheduled action.  

Keeping HTML generation out of the main script separates concerns: `index.mjs` fetches and persists data, `generate-page.mjs` renders it. You can regenerate the page locally from any saved `results.json` without re-running the API calls.

### Step 3 — Create the workflow file and push it

Create `.github/workflows/monthly-check.yml`:

```yaml
name: Monthly bands check

on:
  schedule:
    - cron: '0 8 1 * *'   # 1st of every month at 08:00 UTC
  workflow_dispatch:        # manual trigger for testing

permissions:
  contents: write

jobs:
  check:
    runs-on: ubuntu-latest
    timeout-minutes: 20

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: '22'
          cache: 'npm'

      - run: npm ci

      - name: Run bands check
        run: node index.mjs

      - name: Commit updated state
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "github-actions[bot]@users.noreply.github.com"
          git add checks.json
          git diff --staged --quiet || git commit -m "chore: monthly bands check $(date -u +%Y-%m-%d)"
          git push

      - name: Generate results page
        run: node generate-page.mjs

      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v4
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./site
          publish_branch: gh-pages
          commit_message: "Update results page ${{ github.run_id }}"
          force_orphan: false
```

A few things worth noting:

- `permissions: contents: write` — the default `GITHUB_TOKEN` is read-only. This line grants it write access so it can commit `checks.json` back and push to the `gh-pages` branch.
- `workflow_dispatch:` adds a "Run workflow" button in the Actions UI. Essential for testing without waiting for the 1st of the month.
- `git diff --staged --quiet || git commit:` skips creating an empty commit if nothing changed (e.g. all bands were already checked today).
- `timeout-minutes: 20:` caps runaway runs. The default is 6 hours; a 20-minute ceiling is safer for a script that should finish in \~10 minutes.
- `peaceiris/actions-gh-pages@v4:` the de facto standard action for `gh-pages` branch deployments. It handles orphan branch creation on first run, so you don't need to create the branch manually.

💬 Note: pushes to main do not trigger this workflow. The only triggers are the monthly cron and the manual button. No `push: trigger` is defined.

### Step 4 — Trigger the first run manually

The `gh-pages` branch doesn't exist yet, the action creates it. You need to run the workflow once before you can configure Pages.

1. Go to your repo → Actions tab
2. Click "Monthly bands check" in the left sidebar
3. Click _"Run workflow" →_ leave _"Use workflow from"_ on `main` → click the green _"Run workflow"_ button

Wait for the job to complete (green checkmark). This first run creates the `gh-pages` branch and pushes `index.html `to it.  "_Use workflow from: main"_ means _"run the copy of the workflow file that lives on main."_ It has nothing to do with where the results are deployed.

### Step 5 — Enable GitHub Pages

Now that gh-pages exists, configure Pages to serve from it:

1. Go to _Settings → Pages_
2. Under _Build and deployment → Source_, select _"Deploy from a branch"_
3. Under _Branch_, select `gh-pages` and `/ (root)`
4. Click _Save_

**If you already have a site at username.github.io, there's no conflict**. GitHub serves that from your username/username.github.io repo. This project gets its own path: `username.github.io/bands_update/`. Each project repo gets its own sub-path automatically.

**⚠️ One gotcha**: **GitHub silently disables scheduled workflows on repos with no activity for 60 days**. If you don't push anything for two months, the schedule stops firing and you get an email warning. Re-enabling it requires going to _Actions → the workflow → "Enable workflow"_. Since you're running it monthly and committing `checks.json` back, this won't be an issue — each run creates a commit, which counts as activity.

### Conclusions

Next step could be an integration with Whatsapp or Telegram to notify me there are new albums :-)
