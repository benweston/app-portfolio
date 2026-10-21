# 06 Frequently Asked Questions (FAQ)

---

### Q1: Why is Staging on a subdomain (`stage.benweston.site`) instead of a git branch?
**A:** In modern **Trunk-Based Development**, environments are deployment targets, not git branches. Long-lived environment branches (`develop`, `stage`) cause "merge hell", branch drift, and build discrepancies. By maintaining a single trunk (`main`), every commit is automatically compiled and validated on `stage.benweston.site`, giving you a live replica on Google's Edge CDN without git friction.

---

### Q2: Why doesn't pushing to `main` update the live site?
**A:** Because **the release tag IS the live site**. A core rule of enterprise release engineering is: *there should never be an unversioned deployment to production*.
If every commit pushed to `main` went straight to live, staging would be rendered useless, and half-finished work or bugs could hit the public domain. Pushing to `main` updates **staging** for verification. Production updates exclusively when an official release tag (`v*`) is published.

---

### Q3: What is the difference between a Git Tag and a GitHub Release?
**A:**
- A **Git Tag** (`git tag v0.2.0`) is a native Git pointer marking a specific commit in history. Pushing this tag triggers the GitHub Action to deploy to `benweston.site`.
- A **GitHub Release** is a GitHub platform feature built on top of the git tag that attaches rich, structured Markdown release notes (`## Highlights`, `## Key Focus Areas`, `## Next Steps`), changelogs, and source code archives.
- When you draft and publish a release on GitHub, it automatically creates the tag and fires the deployment action simultaneously.

---

### Q4: Why is TypeScript in `devDependencies` if it's not featured on Ben's profile?
**A:** TypeScript is used strictly as internal tooling for Astro’s static analysis engine, configuration typing (`src/content.config.ts`), and component prop validation (`interface Props` in `BaseHead.astro`). It is not featured in copy, skills cards, or portfolio highlights because Ben's highlighted software stack centers on **Bash/Shell scripting** for workflow automation and **Python** for systems and software development.

---

### Q5: Why is the primary site ID `app-portfolio-e0f7f` instead of `app-portfolio-prod`?
**A:** In Google Cloud and Firebase, Project IDs and primary default hosting site IDs are globally unique and immutable once created. Google Cloud appended the random hash `-e0f7f` during initial creation.
To maintain naming elegance and consistency, Firebase uses **Deploy Targets** (`live` and `stage`). In your CLI and workflows, you only ever interact with `hosting:live` and `hosting:stage`, and public visitors only ever see `benweston.site` and `stage.benweston.site`.

---

### Q6: Why are British English (en-GB) and "Google Cloud" naming strictly enforced?
**A:**
- **British English:** Standardises on proper UK spelling across all documentation, metadata, and copy (e.g. *optimised*, *containerised*, *virtualisation*, *licence*). The HTML root sets `<html lang="en-GB">`.
- **Google Cloud:** "Google Cloud Platform" and the acronym "GCP" were formally retired by Google in 2019. The official enterprise brand name is simply **Google Cloud**.

---

### Q7: Can I push a change to live immediately without waiting days?
**A:** Yes! You do not need to accumulate changes. If you have an urgent fix or immediate update:
1. Commit the change on `main`.
2. Bump the version (`npm version <x.y.z> --no-git-tag-version`).
3. Commit and tag it (`git tag -a v<x.y.z> -m "Release v<x.y.z>"`).
4. Push with tags (`git push origin main --tags`).
GitHub Actions will deploy it to **both** staging and production in parallel immediately.
