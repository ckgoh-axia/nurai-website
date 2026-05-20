# Staging Environment Setup

## Overview

| Branch | Environment | Vercel Target | Trigger |
|--------|-------------|---------------|---------|
| `staging` | Staging (preview) | Preview URL | Push to `staging` |
| `main` | Production | axiasols.com | Push to `main` |

---

## Step 1 — Get Your Vercel Credentials

You need 3 values from Vercel.

### VERCEL_TOKEN
1. Go to https://vercel.com/account/tokens
2. Click **Create Token**
3. Name it `github-actions-nurai`, set scope to your team
4. Copy the token — you only see it once

### VERCEL_ORG_ID and VERCEL_PROJECT_ID
Run this locally in the `nurai-website` folder:
```bash
npx vercel link
```
This creates `.vercel/project.json` (gitignored). Open it and copy:
- `orgId` → your `VERCEL_ORG_ID`
- `projectId` → your `VERCEL_PROJECT_ID`

---

## Step 2 — Create GitHub Environments

Go to your repo → **Settings → Environments** and create two environments:

### `staging`
- No required reviewers needed
- Add these secrets:
  - `VERCEL_TOKEN`
  - `VERCEL_ORG_ID`
  - `VERCEL_PROJECT_ID`

### `production`
- Optional: add a required reviewer (yourself) so production deploys need manual approval
- Add the same 3 secrets

---

## Step 3 — Push the Staging Branch

```bash
git push origin staging
```

GitHub Actions will trigger, deploy to Vercel, and post the preview URL in the Actions log.

---

## Step 4 — Set Vercel to Stop Auto-Deploying from GitHub

Since GitHub Actions now owns deploys, turn off Vercel's built-in GitHub integration to avoid double-deploys:

1. Go to Vercel → your project → **Settings → Git**
2. Under **Ignored Build Step**, add: `exit 1` (this makes Vercel ignore all direct GitHub pushes)

Now only GitHub Actions can trigger Vercel deploys.

---

## Day-to-Day Workflow

```
feature branch → PR into staging → deploy-staging.yml fires → review preview URL
staging → PR into main → deploy-production.yml fires → live on axiasols.com
```

---

## Environment Variables

Edit `env/staging.js` and `env/production.js` to add environment-specific config.
These are injected as `window.NURAI_CONFIG` on every page.

To use in your HTML:
```html
<script src="/env/config.js"></script>
<script>
  console.log(window.NURAI_CONFIG.environment); // 'staging' or 'production'
  console.log(window.NURAI_CONFIG.apiBaseUrl);
</script>
```
