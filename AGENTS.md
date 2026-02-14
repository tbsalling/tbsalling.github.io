# AGENTS.md

This repository is a **Jekyll** site hosted on **GitHub Pages** (remote theme: Cayman).

## What to change (most common)
- Primary marketing pages:
  - `index.md` (homepage / positioning)
  - `clients.md` (featured projects)
  - `_config.yml` (`title`, `description`)
  - `404.md`
- Avoid changing theme/layout/includes unless asked.
- Blog posts (`blog_*.md`) are usually out of scope unless explicitly requested.

## Copy guidelines
- Tone: **selling, coherent, authoritative**.
- Emphasize: **senior freelance**, **Java backend/architecture**, **AI‑accelerated delivery**.
- Keep claims credible and consistent with the projects list.

## Local development
### Prereqs
- Ruby **3.x** and Bundler (Ruby 4.x may fail due to gem compatibility).

### Install
```bash
bundle install
```

### Build
```bash
bundle exec jekyll build
```

### Serve
```bash
bundle exec jekyll serve
# or (faster dev config)
bundle exec jekyll serve --config _config.yml,_config_dev.yml
```

## Validation before finishing
- Run `bundle exec jekyll build` to ensure the site still renders.
- Keep changes minimal and focused on the requested outcome.
