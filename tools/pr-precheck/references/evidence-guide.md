# Evidence guide: where evidence lives in a PR package

## Plan fidelity (harness category: silent-drift)

**Where it lives.** In an eval bundle: the plan-context block's scope pair (in-scope/not-in-scope) and any deviation note, read against the candidate PR's diff and description. In live mode: `plan.md`'s scope section and its `## Deviations` heading, read against `git diff main...HEAD` and `pr_draft.md`'s summary/changes list.

**What good looks like.** Every changed file falls inside the plan's stated boundary, or a deviation note explicitly accounts for it. A description claiming more or less than the diff actually delivers is silent drift too, not a separate communication problem — the fidelity question is always "does what's said match what's shown," whether the "said" is the plan or the description.

## Test evidence (harness category: not-tested)

**Where it lives.** The candidate PR's test-evidence section (eval bundle) or `test_evidence.md` (live), read against the plan's test plan and the issue's original repro steps.

**What good looks like.** The plan's own named observable check is actually re-run, with real pasted before/after output — this is the one place "tests pass" as a bare sentence fails, since it's the direct proof of the fix. The repo's own broader required checks (the full test suite, lint, typecheck) are different: a specific, quantified one-line result ("1462 passed, 12 skipped") is sufficient evidence for these — nobody pastes a thousand lines of suite output into a PR, and a real count is itself verifiable. What fails this family is a check silently skipped, a bare unquantified "tests pass" with no numbers, or a disclosed failure hidden rather than honestly reported.

## Diff quality (harness category: unreviewable)

**Where it lives.** The unified diff and the commit list.

**What good looks like.** A reviewer can see the actual fix clearly, with nothing unrelated riding along: no debug prints or commented-out blocks left in, no drive-by formatting changes mixed into the functional change, no files touched that the plan's scope doesn't name. Debris doesn't have to be malicious to fail this check — an accidental leftover `print()` is enough.

## Standards and comms (harness category: standards-wall)

**Where it lives.** The repo's stated PR template sections, `docs/CONTRIBUTING.md`/policy (including any AI-use disclosure ask), and the issue thread's explicit maintainer comments, read against the PR description's filled sections and disclosure.

**What good looks like.** Every section the repo's template asks for carries real, specific content — not the template's own placeholder text left unedited. If the repo's policy asks for AI-use disclosure, it's present and says something real about how AI was used, not just a check mark. If a maintainer already said something in the thread, the description engages with it rather than silently doing something else. (Whether the description's claims match the diff is plan fidelity, above, not this family.)