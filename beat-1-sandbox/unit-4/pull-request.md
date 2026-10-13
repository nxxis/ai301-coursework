# Unit 4 — Test and Submit

GitHub username: nxxis

## Pull request

https://github.com/codepath/pathreview-ai301-fa26-s1/pull/175

## Branch

fix/68-empty-index-zerodivision

## Reflection

### Run history

1. Built `pr-precheck` from scratch against `CONTRACT.md` and the assignment's four named failure categories (silent-drift, not-tested, unreviewable, standards-wall): five required checks, no preferred checks. `SKILL.md` itself was new this week too, written following Unit 3's `plan-check` `SKILL.md` as a style pattern.
2. Smoke test (`--limit 3`): 3/3 agreement, including the trickiest category (`standards-wall`) on a first try.
3. First full run: 13/20, below the bar. Every single `clear-accept` package (0/7) failed on exactly one check, "Test evidence is decisive" — a pattern specific enough to point at one systemic wording problem rather than seven separate judgment calls.
4. Debugged four packages at once. All four reported real, specific, quantified suite results (e.g. "1462 passed, 12 skipped") but had been failed for not pasting the full raw terminal output of the entire suite — unrealistic for any PR with a real test count. Reworded the check: the plan's own specific targeted test still needs full pasted output, but the repo's broader suite/lint/typecheck checks only need a quantified one-line result.
5. Re-test: the simplest case fixed cleanly, but two packages (`pkg-05`, `pkg-11`) still failed — for a different, more specific reason this time. Both had added genuine automated tests implementing the plan's exact observable check, and the overall suite count already included those new tests passing — but my check still demanded a *separately captured* run of just that one test. Reworded again: an automated test added in the diff, backed by the suite's quantified count that includes it, counts as sufficient evidence without a standalone capture.
6. Canary-checked against two `not-tested` packages (genuinely bad submissions) before trusting the loosening — both held correctly rejected, each for their own independent reasons, confirming the fix didn't open a general hole.
7. Re-test: those two packages passed cleanly. One (`pkg-16`) remained, failing on a bare `go test ... passes` result with no count. Recognized that vanilla `go test`, unlike `pytest`/`cargo test`, genuinely doesn't produce a count by default — the same situation as lint tools not having counts. Extended the no-count exception to any tool whose standard invocation doesn't naturally include one.
8. Re-test: `pkg-16` passed cleanly, with every earlier canary still holding. Confirming full run: 18/20, PASS, every category clean (`not-tested 4/4`, `silent-drift 4/4`, `standards-wall 2/2`, `unreviewable 3/3`, `clear-accept 5/7`).
9. The two remaining misses (`pkg-11` on a different check, and a new package `pkg-13`) were each individually re-tested in isolation and came back fully clean `accept` — confirmed as model-judgment wobbles on borderline calls, not rubric defects, the same pattern seen on close calls throughout this course.

### Package analysis

**`pkg-11`** — gold verdict `accept`. In the confirming full run, my rubric rejected it on "Description matches the diff": the description claims tests were added "for the escaped, unescaped, and double-backslash cases," but the diff's one new test (`escaped_trailing_space_is_kept`) explicitly exercises only the escaped and double-backslash cases — the "unescaped trailing space" behavior is only implicit in the test's input data, not a separately named case. When I re-tested this package in isolation (twice, across different rounds), it passed this same check cleanly both times, with the evidence line explicitly calling the gap "a noted tension that doesn't fail the check." That inconsistency is itself informative: this is a genuinely borderline wording question — is "tests added for X, Y, and Z" an accurate description when Z is covered implicitly by the same test case rather than a dedicated one? — and the model landed on both sides of that line across different runs. I didn't chase a rubric fix for this, because the ambiguity is in the PR's own description wording, not a flaw in how my check reads it; tightening the check further to always forgive this phrasing risked quietly also forgiving a genuine case of claimed-but-missing test coverage elsewhere in the set.

### Check rationale

The check as it currently reads in my rubric:

> **Test evidence is decisive** | `test_evidence.md`'s before/after output, read against the plan's test plan and the issue's original repro steps | The plan's own named observable check is backed by real evidence: either directly pasted before/after output, OR — when the diff adds an automated test that implements that exact check — the suite's quantified run result that includes the new test (e.g. "312 tests passed") counts as sufficient proof without a separately captured standalone run. The repo's own broader required checks (test suite, lint, typecheck) are each run with a specific result: a quantified count when the invoked tool's standard, default output naturally includes one (e.g. pytest or cargo test's "1462 passed, 12 skipped"), or a clear pass/clean/fail statement when it doesn't (lint, formatters, typecheckers, and test runners like vanilla `go test` that print only `ok`/`PASS` by default without a counting flag). Fails when the plan's own check has neither kind of evidence, when a broader check is silently skipped with no disclosure, or when a broader check's result is bare and unverifiable — no tool named, no count, no specific outcome at all. | required

This check took three real rounds to get right, and each round corrected a different flavor of the same underlying mistake: treating a Python-repro-script's evidence shape (manually run commands, fully pasted output) as the only legitimate kind, when real PRs across different ecosystems prove things in genuinely different, equally valid ways — an added unit test counted in a suite-wide number, or a tool whose idiomatic invocation simply doesn't produce a count. The final wording explicitly separates what always needs full pasted output (the plan's own specific, targeted check — the direct proof of the fix) from what can be proven more flexibly (the repo's broader, pre-existing checks), and ties the flexibility to a concrete, checkable fact (does the tool's standard output include a count) rather than a vague allowance that could be stretched to excuse genuinely vague reporting.

### Trade-offs

Letting an automated test's inclusion in a suite-wide count substitute for a separately captured run trades some independent verification for recognizing real engineering practice — a reviewer can't see that specific test's output in isolation from this evidence alone, only that the suite total is consistent with it passing. I accepted this because the alternative (demanding a standalone capture for every single plan-named check) effectively penalizes writing proper automated tests, which is exactly backwards: a PR that encodes its proof as a permanent regression test is more valuable long-term than one that only pastes a one-off manual script's output. The risk is a diff that adds a test which *doesn't* actually cover the claimed behavior as precisely as described — but that's a gap "Description matches the diff" is positioned to catch independently, not something this check needs to also guard against by itself. Similarly, carving out "no count required" for tools like `go test` and lint checkers trades a uniform evidence bar across all PRs for respecting real tooling conventions; the risk is a Python/Rust PR could try to claim the same leniency for a tool that *does* produce counts by default, phrasing a result vaguely to dodge the stricter bar. I judged this acceptable because the check's wording ties the exception to the tool's actual default behavior, not to how the PR author chose to phrase things — a `pytest`/`cargo test` result reported without a count is still failing this check regardless of phrasing, since those tools do produce counts by default.
