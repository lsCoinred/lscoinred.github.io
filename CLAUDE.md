# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Academic portfolio website built on the [Academic Pages](https://github.com/academicpages/academicpages.github.io) Jekyll template (forked from Minimal Mistakes theme). Deployed via GitHub Pages at `lscoinred.github.io`.

## Build & Serve Commands

### Local Ruby (primary)
```bash
bundle install                              # install dependencies
jekyll serve -l -H localhost                # serve with live reload at localhost:4000
bundle exec jekyll serve -l -H localhost    # alternative using bundler
```
If permission errors: `bundle config set --local path 'vendor/bundle'` then `bundle install`.
Changes to `_config.yml` require restarting the server; Markdown/HTML changes reload automatically.

### Docker
```bash
docker compose up    # serves at localhost:4000
```

### VS Code Dev Container
F1 → "Dev Container: Reopen in Container" — auto-serves at localhost:4000.

### JavaScript build
```bash
npm run build:js     # minify JS (jQuery + plugins + custom) into assets/js/main.min.js
npm run watch:js     # watch and rebuild JS on changes
```

## Architecture

### Content Collections (in `_config.yml`)
Four Jekyll collections, each with its own directory and permalink pattern `/:collection/:path/`:

| Collection | Directory | Layout | Key metadata |
|---|---|---|---|
| Publications | `_publications/` | single | `category` (books/manuscripts/conferences), `venue`, `paperurl`, `citation` |
| Talks | `_talks/` | talk | `type`, `venue`, `location` (for talkmap), `date` |
| Teaching | `_teaching/` | single | `type`, `venue`, `location` |
| Portfolio | `_portfolio/` | single | `excerpt` (supports HTML/images) |

Blog posts go in `_posts/` with standard Jekyll `YYYY-MM-DD-title.md` naming.

Content filenames follow `YYYY-MM-DD-slug.md` format. All content uses YAML front matter with `title`, `collection`, `permalink`, and `date`.

### Key directories
- `_pages/` — archive/listing pages and standalone pages (about, cv, 404, talkmap)
- `_layouts/` — page templates (default → single → talk/archive/cv-layout/splash)
- `_includes/` — reusable partials (author-profile, archive-single, masthead, footer, analytics, comments)
- `_sass/` — SCSS organized into `theme/` (6 color schemes with light/dark variants), `layout/` (component styles), `include/` (mixins)
- `_data/` — `navigation.yml` (header menu), `authors.yml`, `cv.json` (JSON Resume format), `ui-text.yml`
- `images/` — site images and author avatar
- `files/` — downloadable PDFs and documents

### Configuration
- `_config.yml` — site settings, author profile, social links, publication categories, collection definitions, plugin list, theme selection (`site_theme`: default/air/sunrise/mint/dirt/contrast)
- `_data/navigation.yml` — header navigation menu items and order
- `_config_docker.yml` — Docker-specific overrides

### Markdown Generators (`markdown_generator/`)
Python scripts and Jupyter notebooks that convert CSV/TSV data into collection Markdown files:
- `publications.py` — reads `publications.csv`/`.tsv`, outputs to `_publications/`
- `talks.py` — reads `talks.tsv`, outputs to `_talks/`
- `pubsFromBib.py` / `PubsFromBib.ipynb` — converts BibTeX to publication markdown
- `OrcidToBib.ipynb` — fetches BibTeX from ORCID

### CI/CD
`.github/workflows/scrape_talks.yml` — triggered on pushes to `_talks/` or `talkmap.ipynb`, runs the talkmap notebook to regenerate talk location data and auto-commits results.

### Talkmap
`talkmap.py` / `talkmap.ipynb` parse talk locations from `_talks/` front matter, geocode them, and generate an interactive map in `talkmap/`. Enable the map link with `talkmap_link: true` in `_config.yml`.
