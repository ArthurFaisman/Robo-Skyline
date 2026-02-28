# Robo Skyline Website

A minimalist corporate website built with Jekyll and deployed via GitHub Pages.

## Project Structure

```
_config.yml              Jekyll configuration (title, URL, plugins)
Gemfile                  Ruby dependencies
.gitignore               Excludes _site/, .jekyll-cache/, vendor/, Gemfile.lock
_layouts/
  default.html           Base HTML5 layout (head, header, content, footer)
_includes/
  header.html            Sticky navigation bar with responsive mobile menu
  footer.html            Contact email, privacy link, copyright
assets/
  css/
    style.css            All custom styles (CSS variables, responsive breakpoints)
index.html               Homepage — hero, services, contact CTA
privacy-policy.md        Privacy policy page
.github/
  workflows/
    pages.yml            GitHub Actions workflow for building and deploying
```

## Dependencies

- **Ruby** >= 3.2 (CI uses 3.3; locally any 3.2+ works)
- **Bundler** (ships with Ruby)
- **Jekyll** ~> 4.3 (installed via Bundler)
- **jekyll-seo-tag** — generates `<meta>` and Open Graph tags automatically

No JavaScript frameworks, build tools, or external CSS libraries are used.

## Local Development

```bash
bundle install
bundle exec jekyll serve
```

The site will be available at `http://127.0.0.1:4000/`. Pass `--baseurl ""` when serving locally to strip the `/Robo-Skyline` path prefix.

## Deployment

The site deploys automatically via the GitHub Actions workflow at `.github/workflows/pages.yml` on every push to `main`.

**First-time setup:** In the GitHub repository, go to **Settings > Pages** and set the source to **GitHub Actions**.

Once deployed, the site is available at:

```
https://arthurfaisman.github.io/Robo-Skyline/
```

