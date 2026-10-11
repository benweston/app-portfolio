# App Portfolio (`benweston.site`) - Agent Instructions & Project Context

## Project Overview
This repository contains the source code for Ben Weston's personal website, engineering portfolio, and technical writing hub at [benweston.site](https://benweston.site/). The site showcases cloud & software engineering projects, system architecture diagrams, and technical articles.

---

## Technology Stack & Architecture

- **Framework:** [Astro](https://astro.build/) (v5.x)
  - Content Collections for type-safe Markdown (`.md`) and MDX (`.mdx`) authoring.
  - Zero-JS by default for maximum performance, SEO, and accessibility.
  - Scoped styling with Atkinson Hyperlegible typography.
- **Runtime & Package Manager:** Node.js (>=22.12.0) with `npm`.
- **Hosting & Infrastructure:** Google Cloud Platform (GCP) via **Firebase Hosting**.
  - Deployed to Google's Global Edge CDN with zero server runtime overhead.
  - Custom domain (`benweston.site`) managed in Squarespace, pointing to Google Edge `A` / `TXT` records.
  - Automatic Let's Encrypt SSL provisioning and renewal managed by Google.

---

## Directory Structure

```text
app-portfolio/
├── assets/                 # Repository-level assets (banners, diagrams, screenshots)
│   ├── banners/            # Header and promotional banners (e.g. main-banner.png)
│   ├── diagrams/           # Cloud architecture diagrams and schematics
│   ├── icons/              # Logos and SVG iconography
│   └── screenshots/        # Project and demo screenshots
├── public/                 # Static assets served at site root (favicons, robots.txt)
├── src/
│   ├── assets/             # Bundled, optimized image and font assets
│   ├── components/         # Reusable Astro UI components (Header, Footer, BaseHead)
│   ├── content/
│   │   └── blog/           # Technical articles and blog posts (.md and .mdx)
│   ├── layouts/            # Page template layouts (e.g. BlogPost.astro)
│   ├── pages/              # File-based routing (index, about, blog, rss.xml)
│   ├── consts.ts           # Site-wide constants (SITE_TITLE, SITE_DESCRIPTION)
│   └── content.config.ts   # Zod schema definitions for content collections
├── astro.config.mjs        # Astro configuration and integrations (mdx, sitemap)
├── package.json            # Project dependencies and npm scripts
├── tsconfig.json           # TypeScript configuration extending astro/tsconfigs/strict
├── .gitignore              # Git ignore rules for Node, Astro, GCP, and OS files
└── GEMINI.md               # Persistent agent guidelines and project context
```

---

## Development Workflow & Commands

All development commands are executed from the repository root:

| Command | Action |
| :--- | :--- |
| `npm install` | Install all dependencies |
| `npm run dev` | Start local Astro dev server (default: `http://localhost:4321`) |
| `npm run build` | Build static production site into `dist/` |
| `npm run preview` | Locally serve and preview the compiled `dist/` build |
| `npx astro check` | Run type checking and diagnostics across Astro components |

---

## Google Cloud / Firebase Hosting Deployment

1. **Prerequisites:** Ensure `gcloud` and `firebase-tools` CLI are authenticated:
   ```bash
   npm install -g firebase-tools
   firebase login
   ```
2. **Configuration (`firebase.json`):**
   Ensure `dist` is configured as the public hosting directory:
   ```json
   {
     "hosting": {
       "public": "dist",
       "ignore": ["firebase.json", "**/.*", "**/node_modules/**"],
       "cleanUrls": true
     }
   }
   ```
3. **Deploying:**
   ```bash
   npm run build
   firebase deploy --only hosting
   ```
4. **Custom Domain (`benweston.site`):**
   - Add domain in Firebase Console: **Hosting → Add Custom Domain (`benweston.site`)**.
   - Copy the provided `TXT` (verification) and two `A` records into Squarespace DNS Settings.
   - SSL certificates are automatically provisioned by Google once DNS propagates.

---

## Coding Standards & Agent Guidelines

- **Semantic & Accessible HTML:** Adhere to WCAG 2.1 AA accessibility guidelines. Use semantic tags (`<header>`, `<main>`, `<article>`, `<nav>`, `<aside>`, `<footer>`). Ensure high contrast and appropriate `alt` attributes.
- **Content Collections Integrity:** New blog posts in `src/content/blog/` must satisfy the schema defined in `src/content.config.ts`:
  - Required frontmatter: `title` (string), `description` (string), `pubDate` (Date/string).
  - Optional frontmatter: `updatedDate` (Date), `heroImage` (image path).
- **Asset Boundaries:** Do **not** overwrite, delete, or relocate binary files in `assets/` (e.g. `main-banner.png`) without explicit user permission.
- **Documentation Preservation:** Maintain documentation integrity. Keep existing comments, licenses, and docstrings intact.
