# Rubric: is this pull request ready to submit?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Diff matches the plan's scope | The diff's changed files and hunks, read against the plan's in-scope/not-in-scope statement and any `## Deviations` note | Every changed file and change falls inside the plan's stated scope, or is explicitly recorded as a deviation with a reason. Fails when the diff touches files or behavior outside the plan's stated scope with no deviation note (does more than planned), or fails to implement something the plan committed to with no deviation note (does less than planned). An honestly disclosed deviation passes this check — the disclosure is what's graded, not the shortfall itself. | required |
| Description matches the diff | The PR description's stated summary and changes list, read against the actual diff content | Every change the description claims is actually present in the diff, and the diff contains no change the description omits or misrepresents. Fails when the description claims more fidelity than the diff delivers (e.g. claims a case is handled that the diff doesn't touch) or silently omits a real change the diff makes. | required |
| Test evidence is decisive | `test_evidence.md`'s before/after output, read against the plan's test plan and the issue's original repro steps | The plan's own named observable check is backed by real evidence: either directly pasted before/after output, OR — when the diff adds an automated test that implements that exact check — the suite's quantified run result that includes the new test (e.g. "312 tests passed") counts as sufficient proof without a separately captured standalone run. The repo's own broader required checks (test suite, lint, typecheck) are each run with a specific result: a quantified count when the invoked tool's standard, default output naturally includes one (e.g. pytest or cargo test's "1462 passed, 12 skipped"), or a clear pass/clean/fail statement when it doesn't (lint, formatters, typecheckers, and test runners like vanilla `go test` that print only `ok`/`PASS` by default without a counting flag). Fails when the plan's own check has neither kind of evidence, when a broader check is silently skipped with no disclosure, or when a broader check's result is bare and unverifiable — no tool named, no count, no specific outcome at all. | required |
| Diff is reviewable | The diff itself and the commit list | The diff contains only the changes needed for this fix: no debug prints, no commented-out code, no unrelated drive-by edits, no formatting-only churn riding along with the real change. Fails when debris or unrelated changes are present, making the actual fix harder to isolate and review. | required |
| Meets the repo's stated standards | The repo's PR template sections, `docs/CONTRIBUTING.md`/stated policy, and the issue thread's explicit maintainer direction, read against the PR description's sections and AI-use disclosure | Every section the repo's PR template asks for is filled with real content, not boilerplate or a placeholder; AI-use disclosure is present if the repo's policy asks for it; and the description engages with any explicit maintainer direction from the thread rather than ignoring it. Fails when a required template section is empty or boilerplate, a required disclosure is missing, or explicit maintainer direction is ignored. | required |

## Verdict rule

Accept (ready to submit) only if every required check grades pass. Any
required check that grades fail, or grades unclear (evidence genuinely
absent from the package, not merely hard to find), makes the verdict
reject. There are no preferred checks in this rubric. A PR that
honestly discloses a shortfall — via a deviation note in `plan.md`, or
a disclosed failing repo check in the description — is not penalized
for the shortfall itself, only for failing to disclose it: the checks
above are written to pass an honest "less than everything" PR and fail
only a silent one.