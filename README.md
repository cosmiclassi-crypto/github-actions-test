This is a [Next.js](https://nextjs.org) project bootstrapped with [`create-next-app`](https://nextjs.org/docs/app/api-reference/cli/create-next-app).

## Getting Started

First, run the development server:

```bash
npm run dev
# or
yarn dev
# or
pnpm dev
# or
bun dev
```

Open [http://localhost:3000](http://localhost:3000) with your browser to see the result.

You can start editing the page by modifying `app/page.tsx`. The page auto-updates as you edit the file.

This project uses [`next/font`](https://nextjs.org/docs/app/building-your-application/optimizing/fonts) to automatically optimize and load [Geist](https://vercel.com/font), a new font family for Vercel.

## Learn More

To learn more about Next.js, take a look at the following resources:

- [Next.js Documentation](https://nextjs.org/docs) - learn about Next.js features and API.
- [Learn Next.js](https://nextjs.org/learn) - an interactive Next.js tutorial.

You can check out [the Next.js GitHub repository](https://github.com/vercel/next.js) - your feedback and contributions are welcome!

## Deploy on Vercel

The easiest way to deploy your Next.js app is to use the [Vercel Platform](https://vercel.com/new?utm_medium=default-template&filter=next.js&utm_source=create-next-app&utm_campaign=create-next-app-readme) from the creators of Next.js.

Check out our [Next.js deployment documentation](https://nextjs.org/docs/app/building-your-application/deploying) for more details.

## GitHub Action: Sync `main` to `dev`

This repository includes `.github/workflows/sync-main-to-dev.yml` to keep `dev` aligned with `main` through a Pull Request (PR).

### Why this exists

- Teams often merge hotfixes/releases directly into `main`.
- `dev` can drift away from `main`.
- This workflow keeps a visible sync PR open from `main` to `dev` and warns when conflicts appear.

### What it does

The workflow has two jobs:

- `ensure-sync-pr`
  - Runs on push to `main` (or manual run).
  - Creates `main` -> `dev` PR if none exists.
  - Reuses and updates the existing PR if one is already open.
- `triage-conflicts`
  - Runs on push to `main` and push to `dev` (and manual run).
  - Looks for the open `main` -> `dev` PR.
  - If no PR exists, exits without doing anything.
  - If PR has conflicts, comments on that PR with:
    - conflicted files
    - likely contributors (`@mentions`) based on recent commits on both branches

### Triggers

- `push` to `main`: create/update sync PR and check conflicts.
- `push` to `dev`: only check the existing sync PR for conflicts.
- `workflow_dispatch`: manual trigger from GitHub Actions UI.

### Development flow scenarios

- Direct push/merge to `main`
  - Sync PR is created (or updated if already open).
  - PR reflects latest `main` automatically.
- Direct push to `dev`
  - No new sync PR is created from this event.
  - Existing sync PR is checked for conflicts.
- Feature branch created from `main`, then merged to `dev`
  - Can cause divergence; conflict triage will report if sync PR becomes conflicted.
- Feature branch created from `dev`, then merged to `dev`
  - Same behavior; sync PR is checked and conflict warning is posted when needed.

### What it does not do

- It does not auto-merge `main` into `dev`.
- It does not guarantee perfect "culprit detection" for conflicts; mentions are best effort.
- It does not enforce branch policy by itself (use branch protection rules for that).

### Required permissions and token

The workflow uses `gh` CLI, so it passes:

- `GH_TOKEN: ${{ github.token }}` (built-in Actions token)
- `REPO: ${{ github.repository }}` (current `owner/repo`)

Permissions in workflow:

- `pull-requests: write` to create/edit/comment on PRs
- `contents: read` to fetch repository data

### Suggested best practices

- Protect `main` and `dev` branches (reviews + required checks).
- Add `CODEOWNERS` so the right team is requested for review.
- Optionally replace `github.token` with a bot/App token if org policy requires dedicated bot identity.
