# Sync `main` -> `dev` Test Plan

## Test Case 1
**explain:** Verify sync PR is created when `main` gets a new commit (hotfix or any direct push).  
**prerequisite:** No open `main` -> `dev` sync PR exists.  
**setup:**
- Push a commit directly to `main` (e.g. a hotfix).  
**expectation:**
- Workflow runs and `ensure-sync-pr` creates a PR from `main` to `dev`.
- `dev` now has a pending update with the latest `main` code.

## Test Case 2
**explain:** Verify sync PR is not duplicated when `main` gets multiple pushes.  
**prerequisite:** One open `main` -> `dev` sync PR already exists.  
**setup:**
- Push another commit to `main`.  
**expectation:**
- Workflow runs and updates the existing sync PR.
- No second sync PR is created.

## Test Case 3
**explain:** Verify conflict is detected and developers are notified when a feature branch diverges from `main`.  
**prerequisite:** Open `main` -> `dev` sync PR exists.  
**setup:**
- Developer edits a file on their feature branch (branched from `main`) and merges it to `dev`.
- The same file was also changed directly on `main` (different content on the same lines).  
**expectation:**
- Sync PR enters conflict state.
- Workflow posts a comment on the sync PR listing conflicted files and `@mentioning` the likely authors.
- Developer resolves the conflict on their feature branch before merging to `main`.

## Test Case 4
**explain:** Verify workflow never auto-merges the sync PR.  
**prerequisite:** Open sync PR exists with no conflicts (clean state).  
**setup:**
- Push a commit to `main` to trigger the workflow.  
**expectation:**
- Workflow creates/updates the PR only.
- No automatic merge is performed.
- PR remains open for manual review and merge.
