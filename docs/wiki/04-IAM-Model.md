<a href="https://github.com/benweston/app-portfolio">
    <img
        src="https://raw.githubusercontent.com/benweston/app-portfolio/main/assets/banners/main-banner.png"
        alt="Ben Weston - Enterprise Linux & Cloud Architecture"
        width="1100"
        height="150"
    />
</a>

<div align="justify">

> Specifies the identity and access management (IAM) model, service account architecture, and least-privilege security controls governing **[benweston.site](https://benweston.site/)**.   

</div>

## Security Architecture & Principles

<div align="justify">

The security model is designed around three core tenets:   

1. **Principle of Least Privilege (PoLP):** Automated pipelines receive strictly the minimal permissions required to deploy static assets to Firebase Hosting, with zero administrative or cross-service privileges.   
2. **Zero Hardcoded Secrets:** No API keys, tokens, or service account credentials are stored in git repositories or static builds.   
3. **Traceable Audit Logging:** All deployment actions and releases are logged within Google Cloud Cloud Audit Logs and GitHub Actions audit trails.   

</div>

## User Access & Identity

<div align="justify">

| Identity | Platform | Role / Privilege | Scope | Purpose |
| :--- | :--- | :--- | :--- | :--- |
| **Project Owner** | Google Cloud | `roles/owner` | Project `app-portfolio-e0f7f` | Complete project administration and billing management. |
| **benweston** | GitHub | Repository Administrator | Repository `app-portfolio` | Code merges, release publishing, repository secret management. |

</div>

## Service Account Architecture

<div align="justify">

A dedicated Google Cloud service account is provisioned specifically for GitHub Actions:   

- **Service Account Name:** `github-actions-deployer`   
- **Email:** `github-actions-deployer@app-portfolio-e0f7f.iam.gserviceaccount.com`   
- **Assigned IAM Role:** **Firebase Hosting Admin** (`roles/firebasehosting.admin`)   

```bash
# Role verification via gcloud CLI
gcloud projects get-iam-policy app-portfolio-e0f7f \
  --flatten="bindings[].members" \
  --filter="bindings.members:github-actions-deployer@app-portfolio-e0f7f.iam.gserviceaccount.com"
```

</div>

### Why `roles/firebasehosting.admin`?

<div align="justify">

- **Permitted Operations:** Create new releases, upload static files to Hosting buckets, finalize release manifests, and release to hosting targets (`live` and `stage`).   
- **Denied Operations:** Cannot modify Google Cloud IAM policies, create or delete GCP projects, access Cloud Storage buckets outside hosting, or modify billing.   

</div>

## Secret Management

<div align="justify">

Authentication credentials are encrypted using GitHub Repository Secrets:   

| Secret Name | Location | Format | Permitted Workflows |
| :--- | :--- | :--- | :--- |
| **`FIREBASE_SERVICE_ACCOUNT`** | GitHub Actions Encrypted Secrets | JSON Service Account Key | `deploy-staging.yml`, `deploy-production.yml` |

</div>

> [!NOTE]   
> The PR validation workflow (`ci.yml`) does **not** have access to `FIREBASE_SERVICE_ACCOUNT`, ensuring untrusted pull requests cannot exfiltrate credentials or trigger unauthorized deployments.   

## End-to-End Authentication Flow

```mermaid
sequenceDiagram
    autonumber
    participant Dev as Engineer / Tag Push
    participant GHA as GitHub Actions Runner
    participant Sec as GitHub Secrets Store
    participant GCP as Google Cloud IAM (STS / Auth)
    participant FB as Firebase Hosting Edge

    Dev->>GHA: Push tag v* (or push to main)
    GHA->>Sec: Retrieve FIREBASE_SERVICE_ACCOUNT
    Sec-->>GHA: Return encrypted JSON Key
    GHA->>GCP: Authenticate via google-github-actions/auth@v2
    GCP-->>GHA: Mint temporary OAuth2 access token
    GHA->>FB: npx firebase deploy --only hosting:<target>
    FB->>GCP: Validate roles/firebasehosting.admin on token
    GCP-->>FB: Authorization Granted
    FB-->>GHA: Release finalized and deployed live
```
