# Sync `main` to `dev` GitHub Action

This repository includes `.github/workflows/sync-main-to-dev.yml` to keep `dev` aligned with `main` through a Pull Request (PR).

## Why this exists

- Teams sometimes merge hotfixes/releases directly into `main`.
- `dev` can drift from `main` over time.
- This workflow keeps a visible sync PR open and flags conflicts early.

## What it does

The workflow has two jobs:

- `ensure-sync-pr`
  - Runs on push to `main` (or manual run).
  - Creates `main` -> `dev` PR if none exists.
  - Reuses and updates the existing PR if one is already open.
- `triage-conflicts`
  - Runs on push to `main` and push to `dev` (and manual run).
  - Looks for an open `main` -> `dev` PR.
  - If no PR exists, exits without doing anything.
  - If PR has conflicts, comments with:
    - conflicted files
    - likely contributor mentions (`@user`) based on recent commits on both branches

## Triggers

- Push to `main`: create/update sync PR and check conflicts.
- Push to `dev`: only check the existing sync PR for conflicts.
- `workflow_dispatch`: manual trigger from Actions UI.

## Development flow coverage

- Direct push/merge to `main`
  - Sync PR is created or updated.
  - Existing sync PR includes latest `main` commits automatically.
- Direct push to `dev`
  - No new sync PR is created from this event.
  - Existing sync PR is checked for conflicts.
- Feature branch from `main` merged to `dev`
  - Conflict triage reports if sync PR becomes conflicted.
- Feature branch from `dev` merged to `dev`
  - Same behavior; workflow checks conflict state and comments when needed.

## What it does not do

- Does not auto-merge `main` into `dev`.
- Does not guarantee exact culprit detection; mentions are best effort.
- Does not replace branch protection rules.

## Token and permissions

The workflow uses GitHub CLI (`gh`), so it sets:

- `GH_TOKEN: ${{ github.token }}`
- `REPO: ${{ github.repository }}`

Permissions:

- `pull-requests: write` for create/edit/comment on PRs
- `contents: read` for repo data access

## Recommended hardening

- Enable branch protection on `main` and `dev`.
- Add `CODEOWNERS` for auto-review routing.
- Optionally use a bot/App token if org policy requires dedicated bot identity.
