# Sync `main` -> `dev` Test Plan

## Test Case 1
**explain:** Verify `sync/main-to-dev` branch and sync PR are created when `main` gets a new commit.  
**prerequisite:** No `sync/main-to-dev` branch and no open sync PR exist.  
**setup:**
- Push a commit directly to `main` (e.g. a hotfix).  
**expectation:**
- `sync/main-to-dev` branch is created from `main`.
- A PR is opened from `sync/main-to-dev` → `dev`.

## Test Case 2
**explain:** Verify sync PR is not duplicated and `sync/main-to-dev` is reset on subsequent `main` pushes.  
**prerequisite:** `sync/main-to-dev` branch and open sync PR already exist with no resolution commits.  
**setup:**
- Push another commit to `main`.  
**expectation:**
- `sync/main-to-dev` is force-reset to latest `main`.
- Existing sync PR is updated, no second PR is created.

## Test Case 3
**explain:** Verify conflict is detected and the `dev` author is notified.  
**prerequisite:** Open sync PR (`sync/main-to-dev` → `dev`) exists.  
**setup:**
- Developer edits a file on their feature branch (branched from `main`) and merges it to `dev`.
- The same file was also changed on `main` (different content on the same lines).  
**expectation:**
- Sync PR enters conflict state.
- Workflow posts a comment listing conflicted files and `@mentioning` the developer whose changes are on `dev`.
- Developer resolves conflicts on `sync/main-to-dev` branch, not on `main`.

## Test Case 4
**explain:** Verify sync PR is auto-merged when there are no conflicts.  
**prerequisite:** Open sync PR exists in a clean state.  
**setup:**
- Push a commit to `main` that does not conflict with anything on `dev`.  
**expectation:**
- Workflow detects `mergeable_state=clean`.
- Sync PR is automatically merged into `dev` with commit message `chore: auto-merge main into dev (no conflicts)`.
- `dev` is immediately up to date with `main` without any manual steps.

## Test Case 5
**explain:** Verify in-progress resolution work is preserved when a new commit is pushed to `main`.  
**prerequisite:** Open sync PR exists in conflict state and developer has already pushed resolution commits to `sync/main-to-dev`.  
**setup:**
- Developer pushes conflict resolution commits to `sync/main-to-dev`.
- Before the PR is merged, another commit is pushed to `main` that does not conflict with the resolution commits.  
**expectation:**
- Workflow detects resolution commits ahead of `main` on `sync/main-to-dev`.
- New `main` commits are merged into `sync/main-to-dev` without a force reset.
- Developer's resolution work is preserved.

## Test Case 6
**explain:** Verify `sync/main-to-dev` is reset and developer is notified when new `main` commits cannot be merged into an in-progress resolution branch.  
**prerequisite:** Open sync PR exists in conflict state and developer has already pushed resolution commits to `sync/main-to-dev`.  
**setup:**
- Developer pushes conflict resolution commits to `sync/main-to-dev`.
- Another commit is pushed to `main` that conflicts with the resolution commits on `sync/main-to-dev`.  
**expectation:**
- Workflow cannot merge new `main` commits into `sync/main-to-dev`.
- `sync/main-to-dev` is force-reset to latest `main`.
- A comment is posted on the sync PR warning the developer that their resolution work was lost and they need to start fresh.
