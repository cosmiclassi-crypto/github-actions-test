# Sync `main` -> `dev` Test Plan

## Test Case 1
**explain:** Verify a new sync PR is created when there is a push to `main` and no open `main` -> `dev` PR exists.  
**prerequisise:** `main` and `dev` branches exist, and there is no open PR with base `dev` and head `main`.  
**setup:**  
- Developer 1: make a small commit on `main` and push.  
- Developer 2: do nothing for this test.  
**expectation:**  
- Workflow runs.  
- `ensure-sync-pr` creates one PR from `main` to `dev`.  
- No auto-merge happens.

## Test Case 2
**explain:** Verify existing sync PR is reused, not duplicated, after another push to `main`.  
**prerequisise:** One open `main` -> `dev` sync PR already exists.  
**setup:**  
- Developer 1: push a second commit to `main`.  
- Developer 2: do nothing for this test.  
**expectation:**  
- Workflow runs.  
- Existing sync PR is updated with latest `main` commits.  
- No additional duplicate sync PR is created.

## Test Case 3
**explain:** Verify push to `dev` does not create a sync PR and only performs triage logic.  
**prerequisise:** `main` and `dev` exist; optional open sync PR may exist.  
**setup:**  
- Developer 1: push a commit to `dev`.  
- Developer 2: do nothing for this test.  
**expectation:**  
- `ensure-sync-pr` is skipped for `dev` push.  
- `triage-conflicts` runs.  
- If sync PR exists: it checks conflict status.  
- If sync PR does not exist: it exits cleanly.

## Test Case 4
**explain:** Verify conflict detection and PR comment when `main` and `dev` diverge on the same lines.  
**prerequisise:** Open `main` -> `dev` sync PR exists.  
**setup:**  
- Developer 1: edit same line in file `X` on `main`, commit and push.  
- Developer 2: edit same line in file `X` differently on `dev`, commit and push.  
**expectation:**  
- Sync PR is in conflict state (`dirty`).  
- Workflow posts conflict comment in sync PR.  
- Comment includes conflicted files and actionable instructions.

## Test Case 5
**explain:** Verify mention behavior for likely contributors in conflict comment.  
**prerequisise:** Two different GitHub users have recent commits on conflicted files across `main` and `dev`.  
**setup:**  
- Developer 1 account commits file change to `main`.  
- Developer 2 account commits conflicting file change to `dev`.  
- Trigger workflow by push or manual dispatch.  
**expectation:**  
- Conflict comment includes best-effort `@mentions` for detected users.  
- If no author login is resolvable, fallback text appears.

## Test Case 6
**explain:** Verify workflow never auto-merges sync PR.  
**prerequisise:** Open sync PR exists (clean or conflicted).  
**setup:**  
- Developer 1: trigger workflow via push to `main` or `dev`.  
- Developer 2: optionally review PR status.  
**expectation:**  
- Workflow only creates/edits/comments.  
- No merge action is performed automatically.

## Test Case 7
**explain:** Verify manual run (`workflow_dispatch`) works as expected.  
**prerequisise:** Workflow file exists in default branch; Actions enabled.  
**setup:**  
- Developer 1: open Actions tab and run workflow manually.  
- Developer 2: optional reviewer.  
**expectation:**  
- Workflow executes successfully without new push.  
- It creates/updates sync PR when needed and triages conflicts.

## Test Case 8
**explain:** Verify concurrency handling for near-simultaneous pushes to `main` and `dev`.  
**prerequisise:** Concurrency is enabled in workflow; both branches active.  
**setup:**  
- Developer 1: push to `main`.  
- Developer 2: push to `dev` at nearly same time.  
**expectation:**  
- Older in-progress run is canceled if overlap occurs.  
- Latest run completes and reflects latest repo state.  
- Reduced duplicate/noisy behavior in comments.
