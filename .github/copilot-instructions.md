# Project Guidelines

## Code Style
- Use Markdown for content, YAML for frontmatter, SCSS for custom styles
- Reference [_config.yml](_config.yml) for Jekyll configuration patterns
- Follow minima theme conventions in [_sass/minima/](_sass/minima/)

## Architecture
This is a Jekyll static site generator blog with standard structure:
- `_layouts/`: HTML templates (post.html, default.html, etc.)
- `_includes/`: Reusable components (head.html, header.html, footer.html)
- `_posts/`: Published posts with date-prefixed filenames
- `_drafts/`: Unpublished drafts
- `assets/`: Post-specific assets organized by slug

## Build and Test
```bash
# Setup (one-time)
rbenv local 3.2.11
bundle config set --local path vendor/bundle
bundle install

# Development server (includes drafts)
bundle exec jekyll serve --drafts

# Production build (excludes drafts)
bundle exec jekyll build
```

## Conventions
- **Posts**: Filename format `YYYY-MM-DD-slug.md`, frontmatter includes layout, title, date, categories, tags
- **Drafts**: Same format but in `_drafts/` directory, date in frontmatter
- **Excerpts**: Use `<!--more-->` comment to separate excerpt from full content
- **Assets**: Organize by post slug in `assets/[post-slug]/`
- **Tags**: Auto-generated via [tags.html](tags.html), use slugified URLs

See [README.md](README.md) for detailed setup and development environment instructions.