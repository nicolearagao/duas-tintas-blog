# duas tintas

Personal and professional blog by [Nicole Aragão](https://nicolearagao.com.br). Built with [Astro](https://astro.build), hosted on [Vercel](https://vercel.com).

The name means "two inks" — the blog splits into two tones: the intimate and the technical, written in both Portuguese and English.

- [/escrita](https://nicolearagao.com.br/escrita) — personal essays
- [/dev](https://nicolearagao.com.br/dev) — software engineering
- [/leituras](https://nicolearagao.com.br/leituras) — reading list
- [/agora](https://nicolearagao.com.br/agora) — now page

## Stack

- **Astro** — static site generator
- **Markdown** — content via [content collections](https://docs.astro.build/en/guides/content-collections/) with Zod schema validation
- **Vercel** — deployment (static output)

No database, no JS framework, no CMS. Content lives in Git.

## Project structure

```
src/
├── components/        # Header, Footer, Sidebar, PostCard
├── content/
│   └── blog/          # Markdown posts (21 posts, pt/en)
├── data/
│   └── books.json     # Reading list data
├── layouts/
│   └── BaseLayout.astro
├── pages/
│   ├── index.astro
│   ├── blog/[slug].astro
│   ├── escrita/index.astro
│   ├── dev/index.astro
│   ├── leituras.astro
│   ├── agora.astro
│   ├── sobre.astro
│   └── rss.xml.js
└── styles/
    └── global.css
```

## Content schema

Posts use the following frontmatter:

```yaml
title: "Post title"
description: "Short description"
pubDate: 2026-06-03
category: "personal" | "professional"
lang: "pt" | "en"           # defaults to pt
tags: ["debugging", "python"]
series: "início de jornada"  # optional
draft: false                 # optional, defaults to false
```

## Running locally

```bash
npm install
npm run dev       # http://localhost:4321
npm run build     # static output to dist/
npm run preview   # preview the build
```

## License

Content (posts, text, images) is copyright Nicole Aragão. Code is MIT.
