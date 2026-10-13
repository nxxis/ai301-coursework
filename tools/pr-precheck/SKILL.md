---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

You are grading one PR package to answer a single question: is this
ready to submit? A PR package is a candidate pull request — its title,
description, commits, and diff, plus its test evidence — read against
the plan it claims to implement and the issue that plan belongs to.
Grade exactly one package per run. Answer by executing the checks in
`rubric.md`, following `procedure.md` step by step, against evidence
gathered per `references/evidence-guide.md` — never from gut feel, and
never by inventing a check `rubric.md` and `procedure.md` don't
define.

## Inputs and modes

One of:

- **Live mode**: the student's own submission, checked before it goes
  out. Inputs: their `plan.md` (including its `## Deviations` notes),
  the diff on their branch — everything the branch changes relative to
  `main`, produced by `git diff main...HEAD` (three dots), run from the
  working copy — their draft PR title and description (`pr_draft.md`),
  their test evidence (`test_evidence.md`), and their issue's live
  thread. A house-chain student reads the house plan and the house
  repro pack in place of their own `plan.md`; the same checks grade the
  same things either way. Gather issue-side evidence live: the repo's
  PR template, `docs/CONTRIBUTING.md`, and the issue thread's comments.
- **Eval mode**: a package bundle is the whole world. Every fact comes
  from the bundle text — the plan-context block, the candidate PR's
  diff, commits, description, and test-evidence section, and the
  repo-facts block. Fetch nothing else. Eval mode always grades a
  complete package: every check, the full verdict rule.

## The scope seam (live mode only)

In live mode, read `scope.md` before anything else. It names the repo
the student's PR must belong to and any house rules of that
environment. Refuse to grade a PR package for an issue or repo outside
the scoped source. If the scope's `Repo:` line still carries an
unfilled placeholder, stop without grading and tell the student to
fill it in with their section's Path Review repo; never guess a scope.
In eval mode, ignore `scope.md` entirely.

## The voice seam (live mode only)

In live mode, also read `voice-guide.md` — the student's own rules for
how they write upstream, carried forward from week 2. Hold the
outgoing PR text (the title and description in `pr_draft.md`) against
those rules, and report any rule the draft breaks in the summary,
quoting the rule. The voice guide never changes the verdict on its own
unless `rubric.md` has a check that explicitly reads it. In eval mode,
ignore `voice-guide.md` entirely: voice is personal and carries no
gold labels; the rubric's own standards-and-comms checks cover
universal communication quality.

## Component reads

`rubric.md` defines the checks — name, evidence, pass condition,
weight — and the verdict rule that turns their grades into `accept` or
`reject`. `references/evidence-guide.md` is the map: for each evidence
family a check names, it says where that evidence lives in a package
(and, live, in the student's working copy and on GitHub) and what good
looks like there. `procedure.md` is the operating sequence — execute
it exactly as written: its read order, its evidence-gathering steps,
its check-execution rules, and its verdict-assembly rule. Where
`procedure.md` is silent on a step, report the gap in the summary;
never invent a step to fill it. If `rubric.md` has no checks filled
in, or `procedure.md` has no steps filled in, stop and say so: this
tool cannot grade without both, and that refusal is by design.

## Verdict and output

The verdict space is binary: `accept` (ready to submit) or `reject`
(hold). There is no third verdict and no score; any reservation
belongs in a check's evidence line, not in the verdict. End the reply
with the fenced JSON block below, valid and last, with nothing after
it:

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

A short, readable per-check summary may come before the block; the
block itself is the machine-read result.

## Grading discipline

- Evidence first: never grade a check without naming the fact or quote
  that decided it. "Looks fine" is not evidence.
- Grade the thing, not the polish: a terse, complete PR can be ready,
  and a long, confident one can be hiding drift. Every check reads the
  artifact itself — the diff, the evidence, the description — against
  the plan, the issue, and the repo's stated standards, never the
  write-up's shape.
- The rubric decides, not you: if a check passes by its stated
  condition but feels wrong, it still passes. Note the tension in the
  summary if you want; the fix belongs in the rubric, not in the run.
- The procedure decides how, not you: follow `procedure.md` as
  written, and report its gaps instead of silently inventing around
  them.
- Unclear defaults to fail: treat `unclear` as `rubric.md`'s verdict
  rule directs; where that rule is silent, an unverifiable claim
  fails. A PR you cannot verify from the package is not ready to
  submit.