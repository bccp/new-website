# BCCP Website — Maintenance Notes

Last updated: October 7, 2026

These notes are for anyone maintaining the Berkeley Center for Cosmological Physics (BCCP) website, including Claude Code. Read this file, then inspect the actual repository before changing anything.

## Current status

- The site is live at **https://bccp.berkeley.edu/**, served by GitHub Pages from this repository.
- Content is complete for now. Future work is regular content updates and possibly new pages.
- Simone handles all communication with Berkeley IT. Do not contact IT, and do not change domain, DNS, or GitHub Pages settings unless Simone asks.
- Simone strongly prefers the least complicated solution.

## Repository and deployment

- Repository: https://github.com/bccp/new-website (public; production branch `main`)
- Every push to `main` triggers `.github/workflows/deploy.yml`, which runs `npm ci` and `npm run build` on Node 22 and publishes `dist/` to GitHub Pages.
- Actions: https://github.com/bccp/new-website/actions
- The workflow builds for the root domain. These values are correct and must stay as they are:

  ```yaml
  env:
    SITE_URL: https://bccp.berkeley.edu
    SITE_BASE: /
  ```

- The custom domain is set in the repository's Settings -> Pages, not by a `public/CNAME` file. Do not add a `CNAME` file.
- `https://bccp.github.io/new-website/` now redirects to the Berkeley domain.

## Domain setup (do not change)

- Berkeley DNS has `bccp.berkeley.edu CNAME bccp.github.io`.
- In October 2026 the domain was verified for the `bccp` GitHub organization with a `_github-pages-challenge-bccp` TXT record. This resolved an earlier outage in which GitHub removed the custom domain because it was "verified by another owner".
- Both the CNAME and the TXT record must stay in DNS permanently. GitHub requires the TXT record to keep the domain verified.
- Earlier plans for a Berkeley-hosted redirect or a move to Cloudflare are no longer needed.

## Site structure

Astro static site (no backend or database).

```text
src/pages/index.astro       Home page (Astro, custom layout)
src/pages/research.md       Research
src/pages/people.md         People
src/pages/events.md         Events
src/pages/jobs.md           Jobs
src/pages/visiting.md       Visiting
src/pages/donate.md         Donate
src/layouts/BaseLayout.astro   Header, navigation, footer, favicon link
src/layouts/PageLayout.astro   Layout for Markdown pages
src/styles/global.css
public/images/              All site images (stored in the repo)
public/favicon.ico
```

Markdown pages begin with a frontmatter block that must stay intact:

```md
---
layout: ../layouts/PageLayout.astro
title: "People"
description: "..."
---
```

## Content conventions

- People lists (faculty, postdocs, students, former postdocs, former students) are alphabetized.
- Visitor and donation contact is Maria Feng.
- Old conference microsites from the previous BCCP site were deliberately not recreated; only workshop links and descriptions were kept.
- Accessibility basics are in place (skip link, focus states, alt text, contrast, active-page indication, Berkeley accessibility-report link in the footer). Keep them, and always give images meaningful alt text. Berkeley requires ongoing accessibility review.

## Common tasks

### Workflow for any change

1. `git fetch origin` and `git pull --ff-only` on `main` (Simone sometimes edits directly on GitHub).
2. Make the change. Preserve unrelated work; never use `git reset --hard` or `git push --force`.
3. Run `npm run build` and confirm it finishes without errors.
4. Commit only the intended files with a clear message and push to `origin main`.
5. Confirm the "Deploy to GitHub Pages" Actions run succeeds and check the live page at https://bccp.berkeley.edu/.

### Adding images

Put images under `public/images/` (a descriptive subfolder is fine) and reference them as `![Description](/images/example.jpg)`.

### Adding a new page

Create `src/pages/<name>.md` with the frontmatter above, add a navigation link in `src/layouts/BaseLayout.astro`, build, and check the page and the navigation on both desktop and mobile widths.

## Troubleshooting

- **Site looks like unstyled text:** the build base path is wrong. Check that `SITE_URL` and `SITE_BASE` match the values above, then rerun the deployment.
- **An edit doesn't appear:** check that it was committed to `main`, that the Actions run succeeded, then wait a few minutes and force-refresh.
- **Favicon doesn't update in Chrome:** bump the `?v=2` query on the favicon link in `BaseLayout.astro` (for example to `?v=3`).
- **Build warning about `markdown.remarkPlugins`:** a known Astro deprecation notice from the small image-prefix plugin in `astro.config.mjs`. Harmless now; a future Astro upgrade may require rewriting that plugin.

## Known cleanup

`README.md` contains outdated instructions (an old personal preview site, transferring the repository, restoring `public/CNAME`). Ignore them; this file is the source of truth.
