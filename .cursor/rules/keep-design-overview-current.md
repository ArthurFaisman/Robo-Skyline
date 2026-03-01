# Rule: Keep System Design Overview Current

After making structural changes to this repository, update `.cursor/rules/system-design-overview.md` to reflect the new state. Structural changes include:

- Adding, removing, or renaming files or directories
- Changing `_config.yml` settings (url, baseurl, plugins, permalink)
- Adding or modifying layouts, includes, or content pages
- Changing CSS custom properties (design tokens) in `assets/css/style.css`
- Modifying the GitHub Actions deployment workflow
- Changing the custom domain (CNAME)
- Adding JavaScript, SCSS, data files, collections, or blog posts (these currently don't exist and their addition would require updating the "Key constraints" and "File Map" sections)

Sections most likely to need updates:

- **File Map** -- if files are added, removed, or moved
- **Key Implementation Caveats** -- if architectural constraints change
- **Design Tokens table** -- if CSS custom properties are added, removed, or renamed
- **CSS Class Reference** -- if major new components or class names are introduced
- **Architecture diagram** -- if the build/deploy pipeline or rendering chain changes
