# tbsalling.dk

Static personal website for Thomas Borg Salling, hosted with GitHub Pages.

## Overview

This repository is intentionally simple:

- `index.html` is the home page.
- `clients.html` is the portfolio page.
- `images/` contains portraits and client logos.
- `favicon.svg` is the site icon.
- `CNAME` points GitHub Pages at `tbsalling.dk`.

There is no build step, package manager, framework, or generated asset pipeline. The pages are plain HTML with inline CSS and JavaScript.

## Local Preview

Run a local static server from the repository root:

```bash
python3 -m http.server 4173 --bind 127.0.0.1
```

Then open:

```text
http://127.0.0.1:4173/index.html
http://127.0.0.1:4173/clients.html
```

Opening the HTML files directly also works for most edits, but using a local server is closer to how GitHub Pages serves the site.

## Editing Notes

- Keep internal links relative, such as `clients.html` and `index.html#contact`.
- Keep image paths relative to the repo root, such as `images/tbs.webp`.
- Keep the site dependency-free unless there is a clear reason to add tooling.
- The home page and portfolio page use CSS scroll snapping; test desktop and mobile after layout changes.
- `CLAUDE.md` documents the visual system and common editing patterns.

## Publishing

GitHub Pages serves the site from the repository root on the main branch. To publish changes:

```bash
git add .
git commit -m "Update site"
git push origin main
```
