# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A static, no-build personal portfolio site for Katrina Khoury (graphic designer / illustrator), deployed to Vercel. There is no `package.json`, no bundler, no framework — just two hand-authored HTML files with inline `<style>` and `<script>` blocks.

## Commands

There is no build, lint, or test tooling in this repo. To preview locally, serve the directory as static files, e.g.:

```
python3 -m http.server 8000
```

Then open `http://localhost:8000/index.html`. Deployment is Vercel, controlled entirely by `vercel.json` (`{ "outputDirectory": "." }` — deploys the repo root as-is, no build command).

## Architecture

- **`index.html`** — the home page (hero, work grid, about, contact). Self-contained: design tokens, layout CSS, and a small scroll-listener script all live inline in this one file.
- **`project.html`** — the project detail page. It has no per-project markup of its own; instead a `PROJECTS` array hardcoded in its `<script>` block is the single source of truth for every project's title, category, year, description, hero image, and gallery layout. The page reads `?id=<project-id>` from the URL, looks up the matching entry, and renders the whole page client-side via template-literal HTML builders (`buildPage`, `buildGalleryItem`, `buildNotFound`). Prev/next project navigation is derived by index adjacency within `PROJECTS`, wrapping around.
- **To add or edit a project**: edit the `PROJECTS` array in `project.html` (add/adjust an entry with `id`, `title`, `category`, `year`, `description`, `hero`/`heroAlt`, and a `gallery` array of `{ type: 'full' | 'pair', ... }` items), then add a corresponding tile/link (`project.html?id=<id>`) in the work grid section of `index.html`.
- **Design tokens are duplicated, not shared** — both `index.html` and `project.html` define their own `:root` CSS custom properties (`--canvas`, `--ink`, `--dust`, `--wire`, `--signal`, spacing, fonts). When changing a token (color, spacing, font), update it in both files.
- **`content/`** holds the original source assets, with spaces and mixed case in folder/file names (e.g. `content/ARTWORK/Sands Coffee Brandmark/SANDStshirt.jpg`). Both HTML files reference these paths directly with URL-encoding (e.g. `Sands%20Coffee%20Brandmark`) rather than a renamed/optimized copy — when adding assets, either match this encoding convention or rename the file to avoid spaces.
- `content/ARTWORK/Opalesce/` has image assets on disk that are not yet referenced by any entry in `PROJECTS` — not wired up to a live project page.
- `content/CONTACT INFO/` and `content/WEBSITE ORGINIZED/` contain `.docx` planning/source documents that are not consumed by the site at runtime (i.e. not fetched or linked from HTML) — they're reference material only, except `content/RESUME/KatrinaKhouryResume.pdf` which *is* linked directly from the contact section of `index.html`.
