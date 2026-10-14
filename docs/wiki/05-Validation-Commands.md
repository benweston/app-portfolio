# 05 Validation Commands

This runbook provides executable CLI commands for validating local builds, DNS resolution, edge CDN responses, IAM policies, and automated deployments.

---

## 💻 1. Local Workstation & Build Validation

### Verify Node & npm Environments
```bash
node -v   # Expect: >=v22.12.0
npm -v    # Expect: >=v10.x
```

### Run Static Type & Astro Diagnostics
```bash
npx astro check
# Expect: 0 errors, 0 warnings, 0 hints
```

### Clean Rebuild Verification
```bash
rm -rf dist
npm run build
# Expect: static build output in dist/ with sitemap-index.xml
```

### Preview Compiled Output Locally
```bash
npm run preview
# Serves dist/ at http://localhost:4321
```

---

## 🌐 2. DNS & Edge CDN Validation

### Validate Staging DNS Resolution
```bash
dig stage.benweston.site +short
# Expect:
# app-portfolio-stage.web.app.
# 199.36.158.100 (Google Cloud Edge IP)
```

### Validate Production DNS Resolution
```bash
dig benweston.site +short
# Expect:
# 199.36.158.100 (Google Cloud Edge IP)
```

### Test HTTP-to-HTTPS Automatic Redirection
```bash
curl -Iv http://stage.benweston.site
# Expect: HTTP/1.1 301 Moved Permanently
# Expect: Location: https://stage.benweston.site/
```

### Test Staging HTTPS Edge Response
```bash
curl -I https://stage.benweston.site
# Expect: HTTP/2 200
# Expect: strict-transport-security header
# Expect: x-served-by: cache-... (Google CDN Edge node)
```

### Test Live Production HTTPS Edge Response
```bash
curl -I https://benweston.site
# Expect: HTTP/2 200
# Expect: strict-transport-security header
```

---

## 🔥 3. Firebase Multisite CLI Validation

### List Hosting Sites in Project
```bash
npx firebase hosting:sites:list
# Expect:
# - app-portfolio-e0f7f (Default / Live)
# - app-portfolio-stage (Staging)
```

### Inspect Applied Deploy Targets
```bash
npx firebase target
# Expect:
# live -> app-portfolio-e0f7f
# stage -> app-portfolio-stage
```

---

## 🛡️ 4. Google Cloud IAM Validation

### Verify Service Account Existence & Details
```bash
gcloud iam service-accounts describe \
  github-actions-deployer@app-portfolio-e0f7f.iam.gserviceaccount.com \
  --project=app-portfolio-e0f7f
```

### Verify IAM Role Binding
```bash
gcloud projects get-iam-policy app-portfolio-e0f7f \
  --flatten="bindings[].members" \
  --filter="bindings.members:github-actions-deployer@app-portfolio-e0f7f.iam.gserviceaccount.com"
# Expect: roles/firebasehosting.admin
```

---

## 🤖 5. GitHub Actions Pipeline Validation

### Inspect Recent Workflow Runs (via GitHub CLI or cURL)
```bash
curl -s "https://api.github.com/repos/benweston/app-portfolio/actions/runs?per_page=5" | \
  grep -E '("(name|display_title|status|conclusion)":)'
```

### Validate Local Git Tags Against Remote
```bash
git tag -l
git ls-remote --tags origin
```
