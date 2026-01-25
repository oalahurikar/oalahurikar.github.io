# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Personal portfolio and blog website built with Jekyll (Beautiful Jekyll theme), hosted on GitHub Pages. Features blog posts, projects showcase, and resume.

## Development Commands

```bash
# Install dependencies
bundle

# Update dependencies
bundle update

# Start local development server (serves at http://localhost:4000)
bundle exec jekyll serve

# Docker alternative
docker build -t jekyll-site .
docker run -p 4000:4000 jekyll-site
```

## Architecture

- **Static site generator**: Jekyll with kramdown/GFM markdown
- **Theme**: Beautiful Jekyll (upstream: daattali/beautiful-jekyll)
- **Hosting**: GitHub Pages (auto-deploys on push to master)
- **Content authoring**: Obsidian vault in `_posts/` directory

### Key Directories

- `_posts/` - Blog post markdown files with YAML front matter
- `_layouts/` - Page templates (notably `mermaid-post.html` for diagram support)
- `_includes/` - Reusable HTML components
- `_data/` - Static data (social networks, UI text)
- `css/` - Bootstrap and custom stylesheets
- `js/` - jQuery, Bootstrap, custom scripts

### Blog Post Front Matter

Standard posts use `layout: post`. Posts with Mermaid diagrams use `layout: mermaid-post`.

Required front matter fields:
- `layout` - post or mermaid-post
- `title` - Post title
- `date` - Publication date

Optional: `subtitle`, `tags`, `thumbnail-img`, `cover-img`, `comments`

### Blog writing rules
- Follow first principles
- Keep it concise and to the point
- Focus on signal over noise

### Mermaid Diagrams

For posts with flowcharts/diagrams, use `layout: mermaid-post` and standard markdown code blocks:

````markdown
```mermaid
graph TD
    A --> B
```
````

The mermaid-post layout includes client-side JavaScript that converts code blocks to rendered diagrams.

### Table Syntax

Use pipe tables with header separators for Jekyll compatibility:

```markdown
| Header 1 | Header 2 |
|----------|----------|
| Cell 1   | Cell 2   |
```
