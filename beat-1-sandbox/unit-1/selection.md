# Unit 1 — Issue Selection

## Chosen issue

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/68

## Skill's live-mode verdict

<!-- REPLACE this block with your actual terminal output — this is a
reconstruction from a paste that got mangled by terminal line-wrapping.
Copy the real JSON block from your own run before submitting. -->

```json
{
  "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/68",
  "checks": [
    {"name": "Maintainer alive", "grade": "pass", "evidence": "Last default-branch commit 2026-09-16 by Aburke225 (COLLABORATOR, human), 5 days before today"},
    {"name": "Repo in active use", "grade": "pass", "evidence": "archived: false, pushed_at 2026-09-16T21:48:27Z, within 180 days"},
    {"name": "Bounded scope", "grade": "pass", "evidence": "Single bug in one function (KeywordSearcher.index()), one root cause, relevant files named, no umbrella framing or debate"},
    {"name": "Not currently claimed", "grade": "pass", "evidence": "No assignees, no open linked PR; sole comment is a classmate's claim (yulijasso, 2026-09-20), which the Path Review house rule says does not block"},
    {"name": "AI-contribution allowed", "grade": "pass", "evidence": "docs/CONTRIBUTING.md contains workflow/CI rules but no AI-use ban or restriction; silence passes"},
    {"name": "Human-originated issue", "grade": "pass", "evidence": "user.login = Aburke225, author_association COLLABORATOR (not a [bot] account)"},
    {"name": "Good-first-issue label", "grade": "pass", "evidence": "labels include 'good first issue'"},
    {"name": "Reproduction / acceptance clarity", "grade": "pass", "evidence": "Body states the exact call (index([])) that raises ZeroDivisionError and the expected behavior (search() should return [] as it already does)"},
    {"name": "No abandoned-attempt pattern", "grade": "pass", "evidence": "No closed unmerged PRs found (repo has only PR #74, unrelated); one active claim (yulijasso), not a repeated claim/abandon cycle"}
  ],
  "verdict": "accept"
}
```

## Reflection

### Run history

1. Smoke test on 3 items (`--limit 3`) with the first filled-in rubric: 2/3 agreement. issue-01 (gold `accept`) came back `reject` on the Bounded scope check, which read a multi-file documentation checklist as an "umbrella issue meant to be split."
2. Reworded the Bounded scope check to distinguish a checklist of steps toward one cohesive change from a true tracking/epic issue. Re-ran `--only issue-01,issue-02,issue-03`: 3/3.
3. First full run (`--save-run`): 16/20, category floor met, but below the 18/20 bar. Four disagreements: issue-01 and issue-19 (false rejects on Bounded scope), issue-15 and issue-20 (false accepts, both graded accept on only a preferred-check fail).
4. Debugged the two false accepts individually (`--only issue-01,issue-15,issue-19,issue-20`). Found issue-15 had a multi-year "graveyard" pattern of abandoned contributor claims that a preferred-only check couldn't block; issue-20 was opened by `cursor[bot]`, a signal nothing in the rubric checked for.
5. Promoted the abandoned-attempt check to `required` and added a new required "Human-originated issue" check. Re-ran the previously-accepted items plus the four disagreements together (`--only`, 10 items) to check for regressions before spending on a full run: 9/10, zero regressions, only issue-19 still disagreeing.
6. Final full run: 19/20, PASS, category floor met (`scope 4/4`, `claimed 4/4`, `dead-repo 3/3`, `policy 1/1`, `clear-accept 7/8`). Saved as `eval-run.txt`.
7. Ran live mode on three open Path Review issues (#68, #69, #73); all three accepted, ranked by fit profile. Chose #68.

### Issue analysis

**issue-20** — gold verdict `reject`; my rubric's first full run graded it `accept` (`failed_checks: ["Good-first-issue label"]`, a preferred check that couldn't have blocked acceptance). Reading the bundle, the issue was opened by `cursor[bot]` — an AI tool auto-filing a feature request, with zero comments and no human or maintainer engagement since. Nothing distinguished "a real user or maintainer wants this" from "an AI proposed an idea nobody has looked at." I believe the rubric initially missed this because none of its checks examined *who opened* the issue, only who has *commented on* it. After adding a required "Human-originated issue" check, the re-run correctly rejected it (`"Human-originated issue", "grade": "fail", "evidence": "opened by cursor[bot] (NONE) on 2026-08-02"`).

### Check rationale

The check as it currently reads in my rubric:

> **Human-originated issue** | Who opened the issue (account name shown in the bundle/UI) | Fails if the issue was opened by a bot account (username ending in `[bot]`, e.g. `cursor[bot]`, `dependabot[bot]`). A bot-filed issue is not evidence any human or maintainer actually wants this; who has since *commented* doesn't matter, only who opened it. Passes for any human-opened issue, maintainer or not. | required

I made this `required` rather than `preferred` because it isn't a ranking signal, it's a validity gate: an issue nobody actually asked for isn't a worse first issue, it's not a real first issue at all, regardless of how clean or bounded the described work looks. I deliberately scoped it to *who opened* the issue, not who has commented, because a bot-filed issue that a maintainer later engages with in the thread would otherwise slip past a looser check aimed at "any bot involvement."

### Trade-offs

Making "No abandoned-attempt pattern" `required` instead of `preferred` trades recall for precision: it will now hard-reject any issue with 2+ closed unmerged PRs or 3+ claim/abandon cycles, even in cases where those failed attempts happened for reasons unrelated to real difficulty (e.g. a contributor who simply went inactive, or a PR closed for an unrelated organizational reason). I judged this an acceptable trade because the assignment's own evidence guide names this exact pattern as a real difficulty signal, and a false reject here just means one fewer candidate to rank, not a wasted week of a contributor's time — the asymmetry of costs favors the stricter check. The one remaining disagreement in my 19/20 run, issue-19 (gold `accept`, mine `reject` on Bounded scope, for bundling several distinct technical approaches into one bug report), I left unresolved rather than loosen the scope check further, since over-loosening it risked reopening the false accepts on issue-01/issue-19's siblings that the original wording was built to catch.
