# Robo Skyline -- System Design Overview

## Project Summary

Robo Skyline is a static **Jekyll 4.x** brochure website for a software solutions company. It is deployed to **GitHub Pages** via a custom GitHub Actions workflow and served at the custom domain **roboskyline.ca**.

Key constraints:
- No JavaScript anywhere in the site.
- No pre-built Jekyll theme gem -- all layouts and styling are custom.
- No SCSS -- plain CSS only, using CSS custom properties for theming.
- Only two content pages: homepage and privacy policy.
- No `_data/`, collections, or blog posts.

## Architecture

```mermaid
flowchart TD
    Push["git push to main"] --> GHA[".github/workflows/pages.yml"]
    GHA --> Checkout["Checkout repo"]
    Checkout --> Ruby["Setup Ruby 3.3"]
    Ruby --> Build["bundle exec jekyll build"]
    Build --> Upload["Upload _site/ artifact"]
    Upload --> Deploy["Deploy to GitHub Pages"]
    Deploy --> Domain["roboskyline.ca via CNAME"]

    subgraph jekyllBuild ["Jekyll Build Process"]
        ConfigYml["_config.yml"] --> JekyllEngine["Jekyll Engine"]
        ContentPages["index.html, privacy-policy.md"] --> JekyllEngine
        JekyllEngine --> DefaultLayout["_layouts/default.html"]
        DefaultLayout --> HeaderInclude["_includes/header.html"]
        DefaultLayout --> FooterInclude["_includes/footer.html"]
        DefaultLayout --> SeoTag["jekyll-seo-tag plugin"]
        StyleCSS["assets/css/style.css"] --> CopiedToSite["Copied to _site/"]
    end

    Build --> jekyllBuild
```

## File Map

```
Robo-Skyline/
├── .github/
│   └── workflows/
│       └── pages.yml              # GitHub Actions: build + deploy to Pages
├── _includes/
│   ├── header.html                # Sticky nav bar, CSS-only mobile hamburger
│   └── footer.html                # Contact email, copyright, privacy link
├── _layouts/
│   └── default.html               # Single HTML5 layout shell (head, seo, header, main, footer)
├── assets/
│   └── css/
│       └── style.css              # All styling (~370 lines, CSS custom properties, responsive)
├── _config.yml                    # Jekyll config (url, baseurl, plugins)
├── Gemfile                        # Jekyll ~> 4.3, jekyll-seo-tag
├── Gemfile.lock                   # Locked dependency versions
├── CNAME                          # Custom domain: roboskyline.ca
├── index.html                     # Homepage (hero, services cards, contact CTA)
├── privacy-policy.md              # Privacy policy content page
├── .gitignore                     # Ignores _site/, .jekyll-cache, vendor/, etc.
└── README.md
```

## Rendering Chain

Each page follows this rendering path:

1. Jekyll reads the page's front matter (`layout: default`, `title`, `permalink`).
2. The page content is rendered (Markdown via kramdown, or raw HTML).
3. Content is injected into `_layouts/default.html` at `{{ content }}`.
4. The layout includes `_includes/header.html` and `_includes/footer.html`.
5. `jekyll-seo-tag` generates `<meta>`, Open Graph, and JSON-LD tags in `<head>`.
6. `assets/css/style.css` is linked in the `<head>` and copied as-is to `_site/`.

## Key Implementation Caveats

### No theme gem
All styling lives in `assets/css/style.css`. There is no `theme:` key in `_config.yml`. Do not add one or attempt to install a theme gem.

### CSS-only mobile navigation
The hamburger menu uses a hidden `<input type="checkbox">` with CSS sibling selectors (`.nav-toggle:checked ~ .site-nav`). The breakpoint is `max-width: 640px`. There is no JavaScript toggle logic.

### Jekyll 4.x with custom GitHub Actions (not the github-pages gem)
The `Gemfile` pins `jekyll ~> 4.3` directly rather than using the `github-pages` gem. Deployment is handled by `.github/workflows/pages.yml`, not the legacy GitHub Pages Jekyll integration. This gives access to Jekyll 4.x features and newer plugin versions.

### Custom domain and CNAME
The `CNAME` file contains `roboskyline.ca`. The `url` field in `_config.yml` must stay in sync with this value (`https://roboskyline.ca`). If the domain changes, update both files.

### SEO via jekyll-seo-tag
The `jekyll-seo-tag` plugin auto-generates meta tags from `_config.yml` fields (`title`, `description`, `url`) and per-page front matter (`title`). Every content page should include a `title` in its front matter.

### Permalink style
`_config.yml` sets `permalink: pretty`, which produces clean URLs (e.g., `/privacy-policy/` instead of `/privacy-policy.html`).

### Baseurl is empty
`baseurl: ""` in `_config.yml` because the site is served at the domain root, not a subdirectory. All `relative_url` filter outputs resolve from `/`.

## Deployment Flow

1. Push (or merge) to `main` triggers `.github/workflows/pages.yml`.
2. The workflow checks out the repo, sets up Ruby 3.3 with bundler cache.
3. `bundle exec jekyll build` runs with `JEKYLL_ENV: production`.
4. The `_site/` directory is uploaded as a Pages artifact.
5. A second job deploys the artifact to GitHub Pages.
6. The `CNAME` file tells GitHub Pages to serve from `roboskyline.ca`.

No manual CI/CD setup is needed beyond the workflow file already in the repo.

## Design Tokens (CSS Custom Properties)

All themeable values are defined as CSS custom properties on `:root` in `assets/css/style.css`. Modify these to change the site's visual design:

| Token | Value | Purpose |
|---|---|---|
| `--color-bg` | `#ffffff` | Page background |
| `--color-bg-alt` | `#f7f8fa` | Alternate section background (hero, cards, contact) |
| `--color-text` | `#1a1a2e` | Primary text color |
| `--color-text-muted` | `#5a5a7a` | Secondary/body text |
| `--color-primary` | `#2563eb` | Accent color (links, buttons) |
| `--color-primary-hover` | `#1d4ed8` | Accent hover state |
| `--color-dark` | `#0f1b2d` | Header and footer background |
| `--color-dark-text` | `#e2e8f0` | Text on dark backgrounds |
| `--color-border` | `#e2e5eb` | Border/divider color |
| `--font-sans` | System font stack | `-apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, ...` |
| `--max-width` | `960px` | Content container max width |
| `--space-xs` | `0.5rem` | Extra-small spacing |
| `--space-sm` | `1rem` | Small spacing |
| `--space-md` | `2rem` | Medium spacing |
| `--space-lg` | `4rem` | Large spacing |
| `--space-xl` | `6rem` | Extra-large spacing |

## CSS Class Reference

Key classes used across pages (defined in `assets/css/style.css`):

- `.container` -- max-width wrapper with horizontal padding
- `.site-header` / `.header-inner` -- sticky top navigation bar
- `.wordmark` -- company name link in the header
- `.site-nav` -- horizontal nav link list (collapses on mobile)
- `.nav-toggle` / `.nav-toggle-label` -- CSS-only hamburger menu
- `.hero` -- full-width hero section with centered text
- `.services` / `.services-grid` / `.service-card` -- services section with CSS Grid cards
- `.contact` / `.btn` -- contact CTA section with styled button
- `.site-footer` / `.footer-inner` -- dark footer with flex layout
- `.page-content` -- wrapper for non-homepage content (privacy policy)
