<a href="https://github.com/benweston/app-portfolio">
    <img
        src="https://raw.githubusercontent.com/benweston/app-portfolio/main/assets/banners/main-banner.png"
        alt="Ben Weston - Enterprise Linux & Cloud Architecture"
        width="1100"
        height="150"
    />
</a>

### Q1: Why is Staging on a subdomain (`stage.benweston.site`) instead of a git branch?

<div align="justify">

**A:** In modern **Trunk-Based Development**, environments are deployment targets, not git branches. Long-lived environment branches (`develop`, `stage`) cause "merge hell", branch drift, and build discrepancies. By maintaining a single trunk (`main`), every commit is automatically compiled and validated on `stage.benweston.site`, giving you a live replica on Google's Edge CDN without git friction.   

</div>

---

### Q2: Why doesn't pushing to `main` update the live site?

<div align="justify">

**A:** Because **the release tag IS the live site**. A core rule of enterprise release engineering is: *there should never be an unversioned deployment to production*.   
If every commit pushed to `main` went straight to live, staging would be rendered useless, and half-finished work or bugs could hit the public domain. Pushing to `main` updates **staging** for verification. Production updates exclusively when an official release tag (`v*`) is published.   

</div>

---

### Q3: What is the difference between a Git Tag and a GitHub Release?

<div align="justify">

**A:** 
- A **Git Tag** (`git tag v0.2.0`) is a native Git pointer marking a specific commit in history. Pushing this tag triggers the GitHub Action to deploy to `benweston.site`.   
- A **GitHub Release** is a GitHub platform feature built on top of the git tag that attaches rich, structured Markdown release notes (`## Highlights`, `## Key Focus Areas`, `## Next Steps`), changelogs, and source code archives.   
- When you draft and publish a release on GitHub, it automatically creates the tag and fires the deployment action simultaneously.   

</div>

---

### Q4: Why is TypeScript in `devDependencies` if it's not featured on the profile?

<div align="justify">

**A:** TypeScript is used strictly as internal tooling for Astro’s static analysis engine, configuration typing (`src/content.config.ts`), and component prop validation (`interface Props` in `BaseHead.astro`). It is not featured in copy, skills cards, or portfolio highlights because the highlighted software stack centres on **Bash/Shell scripting** for workflow automation and **Python** for systems and software development.   

</div>

---

### Q5: Why is the primary site ID `app-portfolio-e0f7f` instead of `app-portfolio-prod`?

<div align="justify">

**A:** In Google Cloud and Firebase, Project IDs and primary default hosting site IDs are globally unique and immutable once created. Google Cloud appended the random hash `-e0f7f` during initial creation.   
To maintain naming elegance and consistency, Firebase uses **Deploy Targets** (`live` and `stage`). In your CLI and workflows, you only ever interact with `hosting:live` and `hosting:stage`, and public visitors only ever see `benweston.site` and `stage.benweston.site`.   

</div>

---

### Q6: Why are British English (en-GB) and "Google Cloud" naming strictly enforced?

<div align="justify">

**A:**
- **British English:** Standardises on proper UK spelling across all documentation, metadata, and copy (e.g. *optimised*, *containerised*, *virtualisation*, *licence*). The HTML root sets `<html lang="en-GB">`.   
- **Google Cloud:** "Google Cloud Platform" and the acronym "GCP" were formally retired by Google in 2019. The official enterprise brand name is simply **Google Cloud**.   

</div>

---

### Q7: Can I push a change to live immediately without waiting days?

<div align="justify">

**A:** Yes! You do not need to accumulate changes. If you have an urgent fix or immediate update:   
1. Commit the change on `main`.   
2. Bump the version (`npm version <x.y.z> --no-git-tag-version`).   
3. Commit and tag it (`git tag -a v<x.y.z> -m "Release v<x.y.z>"`).   
4. Push with tags (`git push origin main --tags`).   
GitHub Actions will deploy it to **both** staging and production in parallel immediately.   

</div>

---

### Q8: Why is the blog decoupled at `blog.benweston.site` instead of hosted inside `src/content/`?

<div align="justify">

**A:** Separating the technical writing hub onto `blog.benweston.site` (backed by a two-way GitHub pipeline between `benweston/blog-source`, Hashnode's Edge network, and `benweston/blog-backup`) decouples article publishing from portfolio application releases:   
- Articles can be authored in Markdown and published independently via Git without cutting a new `app-portfolio` version tag.   
- Visitors navigating to `benweston.site/blog` (or clicking **Blog ↗** in the site header) are seamlessly routed via an HTTP `301` edge redirect to `https://blog.benweston.site`.   
- Because all articles remain stored as canonical Markdown with YAML frontmatter in Git, migrating to a self-hosted Astro blog (`app-blog`) in the future requires zero content rewriting.   

</div>
