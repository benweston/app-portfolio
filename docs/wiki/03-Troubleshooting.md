# 03 Troubleshooting

This guide provides diagnostics, root cause analyses, and verified resolutions for common operational, build, and deployment issues.

---

## 🔍 Build & Compilation Issues

### Issue 1: `npx astro check` Diagnostics Fail
- **Symptom:** Astro check reports errors and exits with non-zero status code:
  ```text
  [check] Getting diagnostics for Astro files...
  Result: 1 error, 0 warnings
  ```
- **Root Cause:** A component prop was changed, an invalid TypeScript type annotation was introduced, or an unexported variable was referenced.
- **Resolution:**
  1. Review the line and file indicated in the error output.
  2. If component props changed (e.g. `BaseHead.astro`), verify that the calling template passes all required props satisfying `interface Props`.
  3. Ensure `src/content.config.ts` matches any new frontmatter schemas.

---

## ☁️ Firebase Deployment Issues

### Issue 2: "Content hash doesn't match content" (Status 400)
- **Symptom:** During `firebase deploy`, file uploads fail repeatedly:
  ```text
  Error: Task ... failed: retries exhausted after 6 attempts, with error:
  Unexpected error while uploading file: Couldn't process request (status=400): content hash doesn't match content
  ```
- **Root Cause:** A file inside `dist/` was modified or regenerated concurrently while the Firebase CLI was computing SHA-256 hashes for the release manifest, or stale compression caches exist in `dist/`.
- **Resolution:** Perform a clean build before redeploying:
  ```bash
  rm -rf dist
  npm run build
  npx firebase deploy --only hosting:<target>
  ```

### Issue 3: "Firebase Hosting target not found"
- **Symptom:** Deployment fails with `Error: Target <name> is not configured`.
- **Root Cause:** Target mapping in `.firebaserc` is missing or out of sync with `firebase.json`.
- **Resolution:** Verify `.firebaserc` targets:
  ```bash
  npx firebase target
  ```
  If unmapped, re-apply the target:
  ```bash
  npx firebase target:apply hosting live app-portfolio-e0f7f
  npx firebase target:apply hosting stage app-portfolio-stage
  ```

---

## 🤖 GitHub Actions Pipeline Failures

### Issue 4: "google-github-actions/auth failed: Credentials are missing"
- **Symptom:** Workflow fails at the *Authenticate to Google Cloud* step.
- **Root Cause:** The repository secret `FIREBASE_SERVICE_ACCOUNT` is either missing, mistyped, or contains invalid JSON.
- **Resolution:**
  1. Open [Repository Secrets Settings](https://github.com/benweston/app-portfolio/settings/secrets/actions).
  2. Ensure the secret is named exactly **`FIREBASE_SERVICE_ACCOUNT`**.
  3. Re-generate a service account key if necessary:
     ```bash
     gcloud iam service-accounts keys create key.json \
       --iam-account=github-actions-deployer@app-portfolio-e0f7f.iam.gserviceaccount.com \
       --project=app-portfolio-e0f7f
     ```
  4. Paste the raw JSON content into the secret and delete the local file.

### Issue 5: Pull Request Blocked by CI Failure
- **Symptom:** GitHub prevents merging because the `CI Validation` workflow failed.
- **Root Cause:** The code in the PR branch failed `npx astro check` or `npm run build`.
- **Resolution:** Click **Details** on the failed check in the PR. Reproduce locally using:
  ```bash
  npx astro check && npm run build
  ```
  Fix the issue in the feature branch and push a new commit. The check will re-run automatically.

---

## 🌐 DNS, Domain, & SSL Issues

### Issue 6: SSL Certificate Error on Custom Subdomain
- **Symptom:** Visiting `https://stage.benweston.site` shows `SSL: no alternative certificate subject name matches target hostname` or browser security warning.
- **Root Cause:** Google's automated ACME SSL challenge is still in progress (typically takes 15 to 45 minutes after DNS points to Google's edge IPs).
- **Verification:**
  1. Check DNS resolution:
     ```bash
     dig stage.benweston.site +short
     ```
     Should return `app-portfolio-stage.web.app.` and Google edge IPs (`199.36.158.100`).
  2. Inspect HTTP redirect:
     ```bash
     curl -Iv http://stage.benweston.site
     ```
     Should return `301 Moved Permanently` to `https://stage.benweston.site/`.
  3. Once Google provisions the cert, HTTPS will return `HTTP/2 200`. In the interim, use the direct endpoint: `https://app-portfolio-stage.web.app`.
