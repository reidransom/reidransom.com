# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Project Overview

This is Reid Ransom's personal website and blog built with Jekyll using the Hitchens theme. The site showcases Reid's work as a full-stack engineer, r2ware founder, and technology leader, with a focus on his entrepreneurial journey and technical expertise.

## Architecture

### Jekyll Static Site Structure
- **Theme**: Uses the `hitchens-theme` Jekyll theme (located in `_theme/` directory)
- **Configuration**: `_config.yml` contains site settings and customizations
- **Content**:
  - Main homepage content in `index.html` with custom parallax effects
  - Blog posts in `_posts/` directory using Markdown with YAML frontmatter
  - Custom layouts in `_layouts/` (extends the Hitchens theme)
  - Navigation configuration in `_data/menu.yml`

### Key Customizations
- **Custom layouts**: Override default theme layouts in `_layouts/`
- **Custom includes**: Menu system and other partials in `_includes/`
- **Custom styling**: SCSS overrides in `_sass/` directory
- **Media integration**: Video backgrounds and image assets in `assets/` and `fonts/`

### Content Management
- **Blog posts**: Written in Markdown with YAML frontmatter in `_posts/` directory
- **Navigation**: Menu items configured via `_data/menu.yml`
- **Assets**: Images, videos, and other media stored in `assets/` directory
- **Theme customization**: Local theme files in `_theme/hitchens-theme/`

## Development Commands

### Local Development
```bash
# Serve the site locally using gojekyll
rp serve

# Serve with custom CMS for content management
rp watch
```

Note: This project uses `rp` (Reid's custom command-line tool) instead of standard Jekyll commands like `bundle exec jekyll serve`.

### Content Creation
```bash
# Create new blog post (follow naming convention)
# _posts/YYYY-MM-DD-title-with-hyphens.md

# Blog post frontmatter template:
---
layout: post
title: "Post Title"
date: YYYY-MM-DD HH:MM:SS -0400
categories: category1 category2
cover-img: /assets/img/image.jpg  # optional
---
```

### Theme Development
- Custom SCSS files go in `_sass/` directory
- Custom layouts override theme defaults in `_layouts/`
- Custom includes go in `_includes/`

## Configuration Details

### Site Settings (_config.yml)
- **Theme**: `hitchens-theme`
- **Plugins**: `jekyll-feed`
- **Custom settings**:
  - `hide_credits: true`
  - `hide_search: true`
  - `excerpt_length: 30`

### Navigation (_data/menu.yml)
Menu items are configured with:
- `title`: Display text
- `url`: Link destination (internal or external)
- `img`/`logo`: Optional image assets

### Content Types
1. **Blog posts**: Technical articles about AI, development, entrepreneurship
2. **Pages**: Static content (primarily the homepage)
3. **Assets**: Images, fonts, videos hosted locally and on DigitalOcean Spaces

## Key Files and Directories

- `index.html`: Custom homepage with parallax effects and video backgrounds
- `_config.yml`: Jekyll configuration
- `_data/menu.yml`: Navigation menu configuration
- `_posts/`: Blog post content in Markdown
- `_layouts/`: Custom layout overrides
- `_includes/`: Reusable template components
- `_sass/`: Custom SCSS styling
- `assets/`: Static assets (images, videos, etc.)
- `_theme/hitchens-theme/`: Local copy of the Jekyll theme

## Content Strategy

The site focuses on:
- **Technical content**: AI development, code generation tools, development workflows
- **Entrepreneurship**: Reid's journey from hot tub business to r2ware
- **Professional experience**: Engineering leadership at companies like Defy Ventures and Premiere Digital
- **Personal branding**: Showcasing expertise in full-stack development and business leadership

## Media and Assets

- **Video backgrounds**: Hosted on DigitalOcean Spaces (nyc3.digitaloceanspaces.com)
- **Custom graphics**: Logo variations in `assets/img/`
- **Fonts**: EB Garamond font files in `fonts/` and `_theme/hitchens-theme/assets/fonts/`
- **Cover images**: Blog post cover images in `assets/img/`

## Development Notes

- The site uses a custom `rp` command instead of standard Jekyll/bundle commands
- Custom parallax scrolling effects are implemented with vanilla JavaScript
- The theme is heavily customized with local overrides
- Menu system supports both text and image-based navigation items
- Video backgrounds are integrated into page and post layouts
