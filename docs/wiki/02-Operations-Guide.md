# 02 Operations Guide

This guide details the hosting infrastructure, delivery lifecycle, CI/CD automation pipelines, and release engineering procedures for **[benweston.site](https://benweston.site/)**.

---

## 🏛️ Core Operational Tenet

> [!IMPORTANT]
> **There must never be a deployment to Production that is not tied to an official release. The release tag IS the live site.**

Unversioned, ad-hoc, or experimental commits must never be pushed directly to production. All production updates must originate from an audited, numbered release tag.

---

## 🌐 Hosting Infrastructure & Environments

The site is hosted on **Google Cloud via Firebase Hosting Multisite** within project `app-portfolio-e0f7f`, delivered across Google's Global Edge CDN:

| Environment | Firebase Target | Site ID | Public URL | Upstream Trigger |
| :--- | :--- | :--- | :--- | :--- |
| **Local Dev** | N/A | N/A | `http://localhost:4321` | `npm run dev` |
| **Staging** | `stage` | `app-portfolio-stage` | [stage.benweston.site](https://stage.benweston.site/) | Any push or merge to `main` |
| **Production (Live)** | `live` | `app-portfolio-e0f7f` | [benweston.site](https://benweston.site/) | Tag push `v*` (or manual dispatch) |

### Multisite Configuration
Managed via [`firebase.json`](https://github.com/benweston/app-portfolio/blob/main/firebase.json) and [`.firebaserc`](https://github.com/benweston/app-portfolio/blob/main/.firebaserc):
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

---

## 🤖 CI/CD Pipeline Architecture (GitHub Actions)

Deployments are automated through three workflows under `.github/workflows/`:

```text
[Feature Branch / PR]
       │
       ▼
  ci.yml (PR Gatekeeper: typecheck + build)
       │
   (Merged)
       ▼
 [push to main]
       │
       ▼
  deploy-staging.yml (Continuous Staging: stage.benweston.site)
       │
 (Human Inspection)
       ▼
 [publish tag vX.Y.Z]
       │
       ▼
  deploy-production.yml (Production Promotion: benweston.site)
```

### 1. `ci.yml` (Pull Request Gatekeeper)
- **Trigger:** Pull Requests targeting `main`.
- **Function:** Runs `npm ci`, `npx astro check`, and `npm run build`. Requires zero cloud credentials. Prevents regressions from entering `main`.

### 2. `deploy-staging.yml` (Continuous Staging)
- **Trigger:** Push or merge to `main`.
- **Function:** Builds production static assets, authenticates to Google Cloud using service account secret `FIREBASE_SERVICE_ACCOUNT`, and executes:
  ```bash
  npx firebase deploy --only hosting:stage
  ```
- **Function:** Guarantees `stage.benweston.site` is an always-current, living replica of `main`.

### 3. `deploy-production.yml` (Production Release)
- **Trigger:** Tag push matching `v*` (e.g. `v0.2.0`), or manual `workflow_dispatch`.
- **Function:** Compiles fresh static assets and executes:
  ```bash
  npx firebase deploy --only hosting:live
  ```

---

## 🚀 Step-by-Step Release Management

When changes on `stage.benweston.site` have been visually inspected and approved for live release:

### Step 1: Bump Semantic Version
Bump version in `package.json` and `package-lock.json` without auto-tagging:
```bash
npm version <x.y.z> --no-git-tag-version
git add package.json package-lock.json
git commit -m "chore(release): bump version to <x.y.z>"
git push origin main
```

### Step 2: Publish the GitHub Release
1. Navigate to **[Releases → Draft a new release](https://github.com/benweston/app-portfolio/releases/new)**.
2. **Choose a tag:** Type `v<x.y.z>` and select `+ Create new tag: v<x.y.z> on publish`.
3. **Target:** `main`.
4. **Title:** `v<x.y.z> - <Title Summary>`.
5. **Description:** Fill in the standardised three-tier release notes:
   - `## Highlights`
   - `## Key Focus Areas`
   - `## Next Steps`
6. Click **Publish release**.

GitHub Actions will detect the tag and automatically deploy `v<x.y.z>` to **[benweston.site](https://benweston.site/)**.

---

## 🔄 Emergency Rollback Procedures

If an unexpected regression is detected on production:

### Method 1: GitHub Actions Re-dispatch (Recommended)
1. Go to **Actions → Deploy to Production**.
2. Click **Run workflow**.
3. Under **Use workflow from**, select the previous stable tag (e.g. `v0.1.0`).
4. Click **Run workflow**. The previous stable version will be compiled and promoted to live within 45 seconds.

### Method 2: Manual CLI Deployment
```bash
git checkout v<previous-stable-tag>
npm run build
npx firebase deploy --only hosting:live
```

---

## 💻 Local CLI Deployment Fallback

If GitHub Actions is ever experiencing an outage, deployments can be triggered directly from the workstation:

```bash
# Deploy to Staging
npm run build
npx firebase deploy --only hosting:stage

# Deploy to Live
npm run build
npx firebase deploy --only hosting:live
```
