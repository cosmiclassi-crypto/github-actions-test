Scenarios to test
Scenario 1: First-time PR creation

Close/delete any open main -> dev sync PR.
Push commit to main.
Expect: new sync PR created, no auto-merge.
Scenario 2: Existing PR update

Keep sync PR open.
Push another commit to main.
Expect: no new PR; same PR reflects latest commits.
Scenario 3: dev push behavior

Push commit to dev.
Expect: workflow runs conflict triage only; does not create new PR on dev push.
Scenario 4: Clean state (no conflict)

Make non-overlapping commits on both branches.
Expect: triage says no conflicts, no conflict instructions comment.
Scenario 5: Real conflict

Edit same line in same file differently on main and dev.
Trigger via push.
Expect: sync PR marked conflicted + actionable bot comment + mentions.
Scenario 6: Mention quality

A edits conflict file on main, B edits same file on dev.
Expect: comment likely mentions @A and/or @B.
Scenario 7: No open PR on dev push

Manually close sync PR.
Push to dev.
Expect: job exits cleanly with “No open main -> dev PR found.”
Scenario 8: Manual run

Run workflow_dispatch from Actions tab.
Expect: behaves same as automated trigger.
Scenario 9: Near-simultaneous pushes

Push to main and dev quickly.
Expect: concurrency keeps latest run; older in-progress run canceled.