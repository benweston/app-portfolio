# 01 User Guide

This guide covers the technical architecture, workstation setup, local development workflow, and content management for **[benweston.site](https://benweston.site/)**.

---

## 🎯 System Overview

`app-portfolio` is the repository powering Ben Weston's technical portfolio, engineering writing hub, and systems architecture sandbox. It is engineered with a strict focus on:
- **Zero-JS by Default:** Maximising performance, SEO indexing, and accessibility with [Astro](https://astro.build/) (v5.x).
- **Enterprise Engineering Pillars:** Highlighting enterprise Linux systems (Red Hat, SUSE, Debian), developer productivity tooling (`dx-*`), and Google Cloud infrastructure.
- **Fast, Accessible Typography:** Self-hosted Atkinson Hyperlegible typeface with zero layout shifts and instant dark/light mode toggling.

> [!NOTE]
> **Stack Scope:** TypeScript is utilised strictly as internal tooling for Astro's static type-checking and component props. It is **not** part of Ben's highlighted personal engineering stack. Do not feature TypeScript in copy, skills lists, or portfolio cards.

---

## 💻 Workstation Prerequisites

Ensure your local development workstation satisfies the following requirements:

| Tool | Minimum Version | Installation / Verification |
| :--- | :--- | :--- |
| **Node.js** | `>=22.12.0` | `node -v` |
| **npm** | `>=10.x` | `npm -v` |
| **Git** | `>=2.40` | `git --version` |
| **Firebase CLI** *(optional local)* | `^15.33.0` | Available locally via `npx firebase` |
| **Google Cloud CLI** *(optional)* | `>=490.0` | `gcloud --version` |

---

## 🚀 Quick Start & Local Commands

All development commands are executed from the repository root:

### 1. Install Dependencies
```bash
npm install
```

### 2. Start the Local Development Server
```bash
npm run dev
```
Launches Astro's local dev server at **`http://localhost:4321`** with hot module replacement (HMR).

### 3. Run Static Diagnostics & Type Checking
```bash
npx astro check
```
Runs Astro's diagnostic suite across all `.astro`, `.ts`, and content collection files. Ensures prop integrity and import validity.

### 4. Build Production Static Assets
```bash
npm run build
```
Compiles and optimises static HTML, CSS, fonts, and WebP images into the `dist/` directory.

### 5. Preview Production Build Locally
```bash
npm run preview
```
Spins up a local HTTP server serving the compiled `dist/` folder to verify production behavior prior to staging deployment.

---

## 📁 Repository & Source Structure

```text
app-portfolio/
├── .github/workflows/      # Automated CI/CD pipelines
├── assets/                 # Repository assets (banners, diagrams, screenshots)
├── public/                 # Static assets copied untouched to site root (favicons, robots.txt)
├── src/
│   ├── assets/             # Bundled, optimised fonts (Atkinson) and profile images
│   ├── components/         # Reusable Astro UI components (Header, Footer, BaseHead, ThemeToggle)
│   ├── content/            # Content Collections (blog posts and technical writing)
│   ├── pages/              # File-based routing (index.astro)
│   ├── styles/             # Global CSS and CSS variable definitions (global.css)
│   ├── consts.ts           # Site-wide constants (SITE_TITLE, SITE_DESCRIPTION)
│   └── content.config.ts   # Schema definitions for content collections
├── astro.config.mjs        # Astro configuration and integrations (mdx, sitemap)
├── package.json            # Node dependencies, scripts, and engine constraints
├── tsconfig.json           # Astro strict type-checking configuration
└── GEMINI.md               # Persistent AI agent instructions and project context
```

---

## ✍️ Content & Layout Management

### Modifying Global Constants
Site title and default descriptions are managed in [`src/consts.ts`](https://github.com/benweston/app-portfolio/blob/main/src/consts.ts):
```typescript
export const SITE_TITLE = 'Ben Weston';
export const SITE_DESCRIPTION = 'Personal portfolio showcasing enterprise Linux architecture, systems engineering, developer productivity tooling, and cloud infrastructure.';
```

### Core Engineering Focus Cards
The landing page displays three core workflow cards in strict order within [`src/pages/index.astro`](https://github.com/benweston/app-portfolio/blob/main/src/pages/index.astro):
1. **Linux & Systems:** Distro operations (RHEL, SUSE, Debian), administration, security hardening, monitoring.
2. **Software & DX:** Workstation automation (`dx-*`), CI/CD pipelines, Bash scripting, Python development.
3. **Cloud & Architecture:** Google Cloud infrastructure, Terraform (IaC), containerised services (Docker), edge delivery.

### Theme & Styling Architecture
- **Global Styles:** Controlled in [`src/styles/global.css`](https://github.com/benweston/app-portfolio/blob/main/src/styles/global.css) using semantic CSS custom properties (`--bg-body`, `--bg-surface`, `--accent`, `--border-color`).
- **FOUC Prevention:** [`src/components/BaseHead.astro`](https://github.com/benweston/app-portfolio/blob/main/src/components/BaseHead.astro) executes an inline, blocking script in `<head>` that inspects `localStorage.getItem('theme')` or system `prefers-color-scheme` before DOM rendering, completely eliminating flash of unstyled content.
