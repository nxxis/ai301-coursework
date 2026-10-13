# Procedure: how this skill grades a plan package

## Read order

1. Read the issue context first (title, body, thread highlights) to understand what bug is being addressed and whether a maintainer has already suggested a direction.
2. Read the repro evidence next (the student's posted repro comment, or the house repro pack). Note exactly what it pins down: the exact command/input used, the exact failure shown, and the root cause it identifies, if any.
3. Read the candidate plan (`plan.md`) next, noting its diagnosis, scope (in/out), approach, test plan, and risks/unknowns sections as separate pieces of evidence.
4. Read the candidate plan comment last, the way a maintainer encountering it fresh on the thread would — after already knowing the issue and the repro evidence, but independent of the fuller plan.md.

This order matters because the Diagnosis-matches-evidence check requires knowing exactly what the repro evidence established *before* judging whether the plan's stated cause is consistent with it; reading the plan before the comment also makes it possible to check whether the posted comment accurately represents the fuller plan rather than contradicting or overselling it.

## Evidence gathering

For each check's evidence family, pull and record as direct quotes:

- **Diagnosis and grounding**: the plan's stated cause (its "Diagnosis"/"Root cause" section) and the repro evidence's demonstrated failure (the exact error/traceback/behavior shown).
- **Scope**: the plan's explicit in-scope and not-in-scope statements, and any list of files/areas it names touching.
- **Executability**: the plan's approach/order-of-work section, specifically the first concrete action it names, if any.
- **Test plan**: the plan's test plan section and the repro evidence's own reproduction steps, recorded side by side.
- **Honesty**: the plan's risks/unknowns section, and any assertion elsewhere in the plan stated without hedging that the evidence doesn't actually establish.
- **Comms**: the plan comment's text, the issue thread's highlighted maintainer comments (if any), and the repo-facts block's stated contribution policy, templates, and AI-use disclosure requirement.

Gather each family once, before grading any check — do not re-read the whole package per check.

## Check execution

1. Execute the checks in the order they appear in `rubric.md`'s table, top to bottom.
2. Grade each check `pass`, `fail`, or `unclear` using only the evidence already gathered for that check's family — never re-read the whole package mid-check.
3. If the evidence a check needs is genuinely absent from the package (not merely hard to find), grade `unclear` and say so plainly. Do not infer what probably happened.
4. Grade each check independently: a fail on one check never changes how a different check is graded.

## Verdict assembly

1. Apply the rubric's verdict rule exactly as written: accept only if every required check passed; any required check graded fail or unclear makes the verdict reject.
2. In the output's evidence line for each check, quote the specific fact or text that decided the grade — never a paraphrase of the rubric's pass condition itself.
3. On a reject verdict, the summary names every failing or unclear required check with its one-line reason, so the student knows exactly what to revise.
4. The same gathered evidence must produce the same verdict every time — verdict assembly is mechanical once the checks are graded, not a fresh judgment call.