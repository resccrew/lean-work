# A/B tasks (run each WITH and WITHOUT the skill, same fixture repo)

Metrics per run: success (tests green / acceptance met), total tokens or /cost, number of rework loops.
Keep a rule only if success is equal or better AND tokens are lower.

1. **Bug hunt** — failing test, root cause in a different file than the test. Expect: found with targeted search, not full-repo reads.
2. **Small feature** — add one endpoint/function across 2 files + tests. Expect: no triage overhead beyond a plan line.
3. **Refactor** — rename + extract function used in 6+ files. Expect: haiku/sonnet bulk edits, strong-model review.
4. **Ambiguous request** — "make the export better". Expect: 1-3 good questions, fewer wasted rewrites.
5. **Noisy output** — test suite printing 500+ lines with 1 failure. Expect: only the failure reaches context.
6. **Trivial edit** — fix a typo in README. Expect: skill adds ZERO overhead (triage skipped).

Pass criteria for v0.1: tasks 1-5 same or better success with fewer tokens; task 6 token cost within 5% of baseline.
