# Procedure: how this tool grades a PR package

## Read order

1. Read the plan (`plan.md`, including any `## Deviations` section) and the issue it belongs to first — this establishes the committed scope and the originally diagnosed cause everything else gets checked against.
2. Read the diff next (`git diff main...HEAD`, or the eval bundle's diff section), noting every changed file and whether each one falls inside the plan's stated scope or is covered by a deviation note.
3. Read the test evidence next, matched directly against the plan's test plan and the issue's original repro steps.
4. Read the draft PR title and description last, the way a reviewer landing on the PR page would — compare its claims against what's already known from the diff and evidence, never the other way around.
5. Read the repo's standards surface: the PR template, `docs/CONTRIBUTING.md`/stated policy, and the issue thread's maintainer comments (live mode) or the repo-facts block (eval mode).

This order matters because both "Diff matches the plan's scope" and "Description matches the diff" require the diff's actual content to be established *before* judging claims made about it — reading the description first risks anchoring on its framing rather than the artifact itself.

## Evidence gathering

For each check's evidence family, pull and record as direct quotes or line references:

- **Plan fidelity**: the plan's in-scope/not-in-scope statement and any `## Deviations` text; the diff's full list of changed files and a one-line description of what changed in each; the PR description's own summary and changes list.
- **Test evidence**: the plan's test plan section and the issue's original repro steps; `test_evidence.md`'s before/after output and the repo's own check results (`make test-unit`, `make test-integration`, `make lint`, `make typecheck`) as actually pasted.
- **Diff quality**: the unified diff itself and the commit list, scanning specifically for debug leftovers, dead or commented-out code, formatting-only churn, and any file touched that the plan's scope doesn't name.
- **Standards and comms**: the repo's PR template section headers, `docs/CONTRIBUTING.md`'s stated policy (including any AI-disclosure ask), and any explicit maintainer direction in the issue thread, read against the description's filled sections and its AI-use disclosure.

Gather each family once, before grading any check — never re-read the whole package per check.

## Check execution

1. Execute the checks in the order they appear in `rubric.md`'s table, top to bottom.
2. Grade each check `pass`, `fail`, or `unclear` using only the evidence already gathered for that check's family — never re-read the whole package mid-check.
3. If the evidence a check needs is genuinely absent from the package (not merely hard to find), grade `unclear` and say so plainly. Do not infer what probably happened.
4. Grade each check independently: a fail on one check never changes how a different check is graded.

## Verdict assembly

1. Apply the rubric's verdict rule exactly as written: accept only if every required check passed; any required check graded fail or unclear makes the verdict reject.
2. In the output's evidence line for each check, quote the specific fact or text that decided the grade — never a paraphrase of the rubric's pass condition itself.
3. When more than one required check fails, the summary names all of them, but the single fact quoted as "the deciding check" in the readable summary is the first failing required check in rubric.md's table order — plan fidelity checks before test evidence, before diff quality, before standards.
4. The same gathered evidence must produce the same verdict every time — verdict assembly is mechanical once the checks are graded, not a fresh judgment call.