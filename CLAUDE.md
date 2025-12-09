# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a Zenn content repository for publishing technical articles. Zenn is a Japanese technical blogging platform. Content is written in Markdown and managed using the zenn-cli tool.

## Repository Structure

- `articles/` - Technical articles in Markdown format
  - Each article has frontmatter with metadata (title, emoji, type, topics, published status)
  - Article filenames use random alphanumeric IDs (e.g., `d8445713c0a3ea.md`)
- `images/` - Images organized by article ID in subdirectories
  - Images are typically stored externally on Zenn's CDN, but local images use the pattern `images/{article-id}/{image-name}.png`
- `books/` - Book content (currently empty, but supported by zenn-cli)

## Common Commands

### Preview content locally
```bash
npx zenn preview
```
Opens a local preview server to view articles and books in the browser.

### Create new article
```bash
npx zenn new:article
```
Generates a new article file with a random ID and default frontmatter.

### Create new book
```bash
npx zenn new:book
```
Generates a new book directory structure.

### List articles
```bash
npx zenn list:articles
```

### List books
```bash
npx zenn list:books
```

## Article Frontmatter Format

All articles must include frontmatter at the top:

```yaml
---
title: "Article Title"
emoji: "📱"
type: "tech"  # or "idea"
topics:
  - "topic1"
  - "topic2"
published: true  # or false
published_at: "2022-02-07 00:39"  # optional, for scheduled publishing
---
```

## Content Guidelines

- Articles are primarily written in Japanese
- Technical articles use `type: "tech"` in frontmatter
- Topics should be lowercase and relevant tags
- Images can be embedded using Markdown syntax with Zenn CDN URLs or relative paths
- Unpublished articles have `published: false` in frontmatter

## Git Workflow

- Main branch: `main`
- New articles appear as untracked files and should be committed when ready
- Images should be committed along with their corresponding articles
