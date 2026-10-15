# App Portfolio (`benweston.site`) - Agent Instructions & Project Context

## Project Overview
This repository contains the source code for Ben Weston's personal website, engineering portfolio, and technical writing hub at [benweston.site](https://benweston.site/). The site showcases enterprise Linux architecture, systems engineering, developer productivity tooling, and cloud infrastructure.

---

## Core Engineering Pillars & Workflow Chain

Content and profile cards follow a defined three-tier workflow order:
1. **Linux & Systems:** Enterprise Linux operations across Red Hat, SUSE, and Debian families (and their derivatives), covering system administration, automation scripting, security hardening, and performance monitoring.
2. **Software & DX:** Workstation/developer tooling (`dx-*`), automated CI/CD pipelines, robust Bash & shell scripting for workflow automation, and Python for systems and software development.
3. **Cloud & Architecture:** Designing resilient, cost-conscious solutions across Google Cloud, Infrastructure as Code with Terraform, containerised services (Docker), and edge delivery.

> [!NOTE]
> **Stack Scope:** TypeScript is utilised strictly as internal tooling for Astro's static type-checking and component props. It is **not** part of Ben's highlighted personal engineering stack. Do not feature TypeScript in copy, skills lists, or portfolio cards.

---

## Technology Stack & Architecture

- **Framework:** [Astro](https://astro.build/) (v5.x)
  - Content Collections for type-safe Markdown (`.md`) and MDX (`.mdx`) authoring.
  - Zero-JS by default for maximum performance, SEO, and accessibility.
  - Scoped styling with Atkinson Hyperlegible typography and dark/light theme switching.
- **Runtime & Package Manager:** Node.js (>=22.12.0) with `npm`.
- **Hosting & Infrastructure:** Google Cloud via **Firebase Hosting**.
  - Deployed to Google's Global Edge CDN with zero server runtime overhead.
  - Custom domain (`benweston.site`) managed in Squarespace, pointing to Google Edge `A` / `TXT` records.
  - Automatic Let's Encrypt SSL provisioning and renewal managed by Google.

---

## Directory Structure

```text
app-portfolio/
├── .github/
│   └── workflows/          # GitHub Actions pipelines (ci.yml, deploy-staging.yml, deploy-production.yml)
├── assets/                 # Repository-level assets (banners, diagrams, screenshots)
│   ├── banners/            # Header and promotional banners (e.g. main-banner.png)
│   ├── diagrams/           # Architecture diagrams and schematics
│   ├── icons/              # Logos and SVG iconography
│   ├── misc/               # Miscellaneous asset files
│   └── screenshots/        # Project and demo screenshots
├── docs/                   # Project documentation
│   └── wiki/               # Canonical Markdown source for GitHub Wiki
├── public/                 # Static assets served at site root (favicons, robots.txt)
├── src/
│   ├── assets/             # Bundled, optimised image and font assets
│   ├── components/         # Reusable Astro UI components (Header, Footer, BaseHead, ThemeToggle)
│   ├── content/            # Content Collections (planned for blog / articles)
│   ├── pages/              # File-based routing (index.astro)
│   ├── styles/             # Global stylesheets (global.css)
│   ├── consts.ts           # Site-wide constants (SITE_TITLE, SITE_DESCRIPTION)
│   └── content.config.ts   # Schema definitions for content collections
├── astro.config.mjs        # Astro configuration and integrations (mdx, sitemap)
├── package.json            # Project dependencies and npm scripts
├── tsconfig.json           # TypeScript configuration extending astro/tsconfigs/strict
├── .gitignore              # Git ignore rules for Node, Astro, Google Cloud, and OS files
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

1. **Authentication:**
   Ensure `firebase-tools` CLI is authenticated (it is installed as a local dev dependency):
   ```bash
   npx firebase login
   ```
2. **Configuration (`firebase.json` & `.firebaserc`):**
   Multisite targets configure separate pipelines for production and staging:
   ```json
   {
     "hosting": [
       {
         "target": "live",
         "public": "dist",
         "ignore": ["firebase.json", "**/.*", "**/node_modules/**"],
         "cleanUrls": true
       },
       {
         "target": "stage",
         "public": "dist",
         "ignore": ["firebase.json", "**/.*", "**/node_modules/**"],
         "cleanUrls": true
       }
     ]
   }
   ```
3. **Deploying to Environments:**
   - **Deploy to Staging:**
     ```bash
     npm run build
     npx firebase deploy --only hosting:stage
     ```
   - **Promote to Live (Production):**
     ```bash
     npm run build
     npx firebase deploy --only hosting:live
     ```
4. **Custom Domains:**
   - **Live (`benweston.site`):** Attached to `app-portfolio-e0f7f` site.
   - **Staging (`stage.benweston.site`):** Attached to `app-portfolio-stage` site.
   - DNS records (A/TXT/CNAME) managed in Squarespace, SSL managed automatically by Google.

---

## CI/CD Pipeline (GitHub Actions)

Trunk-Based Development with automated checks and promotion:

| Workflow | Trigger | Action | Target URL |
| :--- | :--- | :--- | :--- |
| `ci.yml` | Pull Request to `main` | Runs `npx astro check` & `npm run build` | N/A (Build verification gate) |
| `deploy-staging.yml` | Push/merge to `main` | Validates, builds & deploys to Staging | `https://stage.benweston.site` |
| `deploy-production.yml` | Tag push `v*` (or manual dispatch) | Validates, builds & deploys to Production | `https://benweston.site` |

- **Authentication:** Deployments authenticate using Google Service Account secret `FIREBASE_SERVICE_ACCOUNT` via `google-github-actions/auth@v2`.

---

## Release Workflow

When cutting a release (standardised on the [sublime-font-checks](https://github.com/benweston/sublime-font-checks) convention):

1. **Bump Version:**
   Update `package.json` and `package-lock.json` without auto-tagging:
   ```bash
   npm version <x.y.z> --no-git-tag-version
   git add package.json package-lock.json
   git commit -m "chore(release): bump version to <x.y.z>"
   git push origin main
   ```
2. **Create & Push Annotated Tag:**
   ```bash
   git tag -a v<x.y.z> -m "Release v<x.y.z> - <Title>"
   git push origin v<x.y.z>
   ```
3. **Publish GitHub Release:**
   Create a release from the tag on GitHub with structured notes containing:
   - `## Highlights`
   - `## Key Focus Areas`
   - `## Next Steps`

---

## Coding Standards & Agent Guidelines

- **Language & Locale:** Standardise on British English (en-GB) for all copy, documentation, and user-facing text (e.g. *optimised*, *containerised*, *virtualisation*, *colour*). Document root must set `<html lang="en-GB">`.
- **Google Cloud Naming:** Use "Google Cloud", never "Google Cloud Platform" or "GCP".
- **Semantic & Accessible HTML:** Adhere to WCAG 2.1 AA accessibility guidelines. Use semantic tags (`<header>`, `<main>`, `<article>`, `<nav>`, `<aside>`, `<footer>`). Ensure high contrast and appropriate `alt` attributes.
- **Content Collections Integrity:** New content collections in `src/content/` must satisfy schemas defined in `src/content.config.ts`.
- **Asset Boundaries:** Do **not** overwrite, delete, or relocate binary files in `assets/` (e.g. `main-banner.png`) without explicit user permission.
- **Privacy & Security:** Never include personal email addresses or sensitive credentials in public-facing documentation, wikis, or source code. Always use generic role titles (e.g. *Project Owner*, *Repository Administrator*).
- **Documentation Preservation:** Maintain documentation integrity. Keep existing comments, licenses, and docstrings intact.
