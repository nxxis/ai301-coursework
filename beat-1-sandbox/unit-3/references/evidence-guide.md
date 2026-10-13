# Evidence guide: where evidence lives in a plan package

## Diagnosis and grounding

**Where it lives.** In an eval bundle: the candidate plan's "Diagnosis"/"Root cause" section, and the bundle's repro-evidence block (the quoted reproduction command and output). In live mode: the plan's own diagnosis section, and the student's posted repro comment on the issue thread (or the house repro pack).

**What good looks like.** The stated cause names a specific mechanism (a function, a line, a code path) that the repro evidence's own shown output actually implicates. A plausible-sounding guess the evidence never demonstrates, or a cause the evidence directly contradicts (blaming a different function than the one the traceback shows failing), fails this family.

## Scope

**Where it lives.** The plan's explicit "In scope"/"Not in scope" statements and any list of files or areas it names touching.

**What good looks like.** A single, bounded change proportional to the diagnosed cause — typically one function or a small, named set of files — with an explicit line distinguishing it from related-but-separate work a maintainer might otherwise expect bundled in. A plan naming one function to change is bounded; a plan proposing to "clean up the module while I'm in there" is not.

## Executability

**Where it lives.** The plan's approach/"How" section and any stated order of work.

**What good looks like.** The very first action is nameable without asking the author anything — a specific file and a specific change, not an intent ("investigate the cause") restated as though it were a step. A stranger reading only the plan could open the named file and start editing.

## Test plan

**Where it lives.** The plan's "Test plan" section, read against the repro evidence's own reproduction steps (the exact command/input that originally triggered the bug).

**What good looks like.** The test plan re-runs, or explicitly adapts, the repro evidence's own steps, and states what observable change confirms the fix — not "test it works" but "re-run `X`; before: raises `Y`; after: returns `[]`." A test plan that never references the repro evidence's actual steps is unmoored from the bug actually being fixed.

## Honesty

**Where it lives.** The plan's "Risks"/"Unknowns" section, read against claims made elsewhere in the plan without hedging.

**What good looks like.** Anything the plan doesn't actually know (whether the fix could affect other callers, whether a dependency needs a version bump, whether the test environment matches production) is named as a risk or unknown, not silently asserted as settled. A plan with no risks/unknowns section and total confidence everywhere is a red flag, not a sign of thoroughness.

## Comms

**Where it lives.** The plan comment's text, the issue thread's highlighted maintainer comments (or the live thread), and the repo-facts block's stated contribution policy, templates, and AI-use disclosure requirement.

**What good looks like.** If a maintainer already pointed at a direction in the thread, the comment engages with it — building on it, or explicitly explaining a different choice — rather than silently ignoring it. The comment also meets whatever the repo's own conventions ask for: conditions are terms to follow, not reasons to walk away, and silence from the repo is not itself a requirement to manufacture one.