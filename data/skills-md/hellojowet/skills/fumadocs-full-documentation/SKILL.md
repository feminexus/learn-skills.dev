---
name: fumadocs-full-documentation
description: |
  Complete mirror of the upstream Fumadocs documentation from fuma-nama/fumadocs.
  Fumadocs is a documentation framework for Next.js, React Router, TanStack Start, and Vite.
  Load this skill when you need exact upstream reference material — installing, configuring,
  customizing UI, setting up search, integrations (OpenAPI, content sources, OG images),
  internationalization, headless APIs, or MDX processing.
  This is not curated guidance; it is the raw upstream docs organized by topic.
---

# Fumadocs Full Documentation

Mirrored copy of the upstream Fumadocs documentation. Use when curated guidance is insufficient and you need exact source docs.

## How to Use

1. Pick the relevant category below based on the user's question.
2. Load that category file to see all related pages with descriptions.
3. Load only the specific `.mdx` files you need from `references/docs/`.
4. Load nearby `meta.json` files when you need upstream navigation or section names.

## Categories

### Framework — `references/categories/framework.md`

Core concepts: what Fumadocs is, how it compares to alternatives, how to configure navigation and page conventions. Covers deploying (static export), guides (access control, UI customization, EPUB/PDF export, RSS), markdown features (math, Mermaid diagrams, Twoslash annotations), and all search backends (Algolia, FlexSearch, Orama, Typesense, MixedBread, custom).

### Integrations — `references/categories/framework-integrations.md`

Content sources: local markdown, MDX remote, Sanity CMS, and custom adapters. API docs: auto-generating documentation from OpenAPI and AsyncAPI specs. Also covers OG image generation, Story components, docgen from TypeScript/Python/Obsidian, LLMs.txt generation, feedback widgets, and link validation.

### i18n & Manual Install — `references/categories/internationalization.md`

Setting up multi-language documentation sites for Next.js App Router, React Router, and TanStack Start. Manual installation guides (instead of using the CLI) for Next.js, React Router, TanStack Start, and Waku.

### Headless — `references/categories/headless.md`

The framework-agnostic `fumadocs-core` package. Page tree data structures, headless component primitives (breadcrumb, link, TOC), content collections, MDX processing with all remark/rehype plugins, framework-agnostic search integrations, the source API and plugin system, and utility functions (TOC extraction, Git timestamps, Shiki highlighting).

### MDX — `references/categories/mdx.md`

The `fumadocs-mdx` package. MDX compilation and configuration, async MDX, content collections, type generation for frontmatter, performance optimization, workspace/monorepo setup, framework integrations (Next.js, Vite), loader configuration (Node, Bun), and entry point setup for browser/server/dynamic environments.

### UI — `references/categories/ui.md`

The `fumadocs-ui` component library. Layouts: docs sidebar layout, home/landing page, notebook (sequential nav), standalone page, and Flux. Components: accordion, code blocks, tabs, steps, file viewer, type tables, graph view, image zoom, banners, inline TOC, and GitHub info cards. Also covers theming (colors, dark mode), the search dialog, and string translations.

### CLI & OpenAPI — `references/categories/cli-openapi.md`

The `fumadocs` CLI tool: scaffolding new projects with `create-fumadocs-app` and the local preview server. The standalone `fumadocs-openapi` package for working with OpenAPI specs outside the framework integration.

## Other References

- `references/source.json` — Upstream repo, commit, and file count metadata
