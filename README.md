<a href="https://github.com/benweston/app-portfolio">
    <img
        src="/assets/banners/main-banner.png"
        alt="Ben Weston - Cloud & Software Engineering"
        width="1100"
        height="150"
        loading="lazy"
    />
</a>

# App - Portfolio ([benweston.site](https://benweston.site/))

[![CI](https://github.com/benweston/app-portfolio/actions/workflows/ci.yml/badge.svg)](https://github.com/benweston/app-portfolio/actions/workflows/ci.yml)
[![Release](https://img.shields.io/github/v/release/benweston/app-portfolio?color=blue&label=release)](https://github.com/benweston/app-portfolio/releases)
[![Licence: MIT](https://img.shields.io/badge/Licence-MIT-green.svg)](https://github.com/benweston/app-portfolio/blob/main/LICENSE)

<div align="justify">

> Personal website engineered with [Astro 5](https://astro.build/) for zero-JS runtime performance, deployed globally across Google's Edge CDN via [Firebase Multisite](https://firebase.google.com/docs/hosting/multisites), and maintained through a state-of-the-art agentic pairing harness powered by [Google Antigravity](https://antigravity.google/).   

</div>

## Architectural Highlights

<div align="left">

- **Zero Server Runtime:** No backend daemons or containers sleeping, idling, or waking up.   
- **Zero Compute Cost:** Served entirely from Google Cloud's global Edge CDN cache.   
- **Zero Host Maintenance:** No server-side runtime, virtual environments, or container vulnerabilities.   
- **Sub-100ms Global Latency:** Delivered entirely from Google's nearest edge Point of Presence (PoP).   

</div>

## Environments

<div align="justify">

| Environment | Purpose | Target URL | Upstream Trigger |
| :--- | :--- | :--- | :--- |
| **Live (Production)** | Official public portfolio | [benweston.site](https://benweston.site/) | Release tag push (`v*`) |
| **Staging** | Continuous preview & verification | [stage.benweston.site](https://stage.benweston.site/) | Push / merge to `main` |

</div>

<div align="justify">

## Quick Start
    
```bash
npm install     # Install dependencies
npm run dev     # Launch local development server at http://localhost:4321
npm run build   # Build production static assets into dist/
```

</div>

## Documentation

<div align="justify">

Comprehensive operational guidance, architecture specifications, and release runbooks are documented in the [Project Wiki](https://github.com/benweston/app-portfolio/wiki).   

</div>

## Delivery & Roadmap

<div align="justify">

Work tracking, backlog prioritisation, and release workflows are managed on the [App - Portfolio](https://github.com/users/benweston/projects/23) Project Board.   

</div>

## Licence

<div align="justify">

The `app-portfolio` repository is open source and provided under the [MIT License](https://github.com/benweston/app-portfolio/blob/main/LICENSE).   
Banner image taken from photo by [Risto Kokkonen](https://unsplash.com/@risto_kokkonen) on [Unsplash](https://unsplash.com/photos/a-close-up-of-a-cell-phone-with-a-pattern-on-it-FT4hPjqk24s) via the [Unsplash License](https://unsplash.com/license).   

</div>
