# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is the Jekyll site for the Climate Analytics Lab at UC San Diego, served at https://climate-analytics-lab.github.io. It uses the **Minimal Mistakes** theme (as a gem, not remote theme) and the **jekyll-scholar** plugin for BibTeX-driven publications. Deployment is automated via GitHub Actions on pushes to `master` (`.github/workflows/jekyll.yml`); the `github-pages` gem is intentionally **not** used so we can run modern Jekyll 4.3+.

## Common commands

```bash
bundle install              # install gems (first run only)
bundle exec jekyll serve    # local dev server with live reload on http://localhost:4000
bundle exec jekyll build    # one-off build into _site/
```

Ruby 3.1 is what CI uses. There are no tests or linters.

## Content architecture

The site is composed almost entirely from data sources — pages themselves are mostly empty shells that render content from collections, data files, or the bibliography. Knowing where each piece of content lives matters more than reading the page templates.

- **`_people/*.md`** — one file per lab member. Frontmatter only (no body). `_people/README.md` documents required fields (`lastname`, `firstname`, `pub_id`, `role`, `status: active|alumn`, `sort_display`, `image_path`). The People page (`_layouts/people.html`) renders `active` and `alumn` lists separately, sorted by `sort_display` descending.
- **`_news/YYYY-MM-DD.md`** — one file per news item; only date frontmatter + markdown body. Surfaced on the home page (latest 5, with show-more for the full list). See `_news/README.md`.
- **`projects/_posts/0000-01-01-<slug>.md`** — projects use the `posts` collection with the `projects` layout (configured in `_config.yml` defaults). The `0000-01-01-` prefix keeps them date-neutral.
- **`_data/publications.bib`** — single BibTeX file driving `/publications/`. The publications page hard-codes one `{% bibliography --query @*[year=YYYY] %}` block per year, so **adding a new year requires editing `_pages/publications.md`** to add a new section. `_layouts/bibtemplate.html` post-processes each rendered reference: it bolds any author whose `pub_id` (or auto-generated `Lastname, F.` form) matches an entry in `_people/`, strips bare DOI URLs, and appends a `[doi]`/`[link]` button. A small inline script on the page sets the CSS counter so numbering runs continuously (newest = highest) across all years.
- **`_pages/*.md`** — top-level pages. Navigation order lives in `_data/navigation.yml`, not in the page files.

## Conventions and gotchas

- The publication numbering script in `_pages/publications.md` assumes a flat `ol.bibliography` structure — if you change the layout to nested lists, update the counter logic.
- `pub_id` in a person's frontmatter must match exactly how their name appears in the BibTeX (per the citation style — currently `copernicus-publications`). Without a `pub_id`, the template falls back to `Lastname, F.` which often won't match.
- `_pages/publications.md.backup` is the pre-jekyll-scholar manual list; leave it alone unless explicitly cleaning up.
- `Gemfile.lock` is committed — CI uses it for reproducible builds. When bumping gems, run `bundle update <gem>` locally and commit the new lock. Don't `bundle update` without a reason. If you add a new dev platform, run `bundle lock --add-platform <plat>`; `x86_64-linux` is required for the GitHub Actions runner.
- Don't add the `github-pages` gem; it pins Jekyll to an old version and would break jekyll-scholar.
