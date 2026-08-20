# blogs

My personal blog, built with [Hugo](https://gohugo.io/) and a customized fork of the [PaperMod](https://github.com/adityatelange/hugo-PaperMod) theme (vendored in `themes/PaperMod`).

Deployed to GitHub Pages at <https://yuan-xinyi.github.io/blogs/> on every push to `master`.

## Local development

```bash
hugo server -D
```

## Writing a post

Add a Markdown file under `content/posts/` with front matter:

```yaml
---
title: "Post Title"
date: 2026-08-20
tags: ["tag"]
ShowToc: true
---
```
