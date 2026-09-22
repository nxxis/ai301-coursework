# Rubric: first-issue fitness for AI-assisted contributors

<!--
Checks table. Last cell of each row is `required` or `preferred`:
`required` checks gate the verdict; `preferred` checks only rank
accepted candidates. Evidence sources per family: see
references/evidence-guide.md.
-->

| Check | Evidence to gather | Pass condition | Weight |
|---|---|---|---|
| Maintainer alive | Date of the most recent default-branch commit; whether its author is human, or a bot merging a human's PR | Most recent commit is dated within 90 days of the capture date (eval mode) or today (live mode), and is authored by a human or merges a human's pull request | required |
| Repo in active use | `archived` flag; last push date to any branch; latest release date | Repo is not archived, AND (last push within 180 days OR latest release within 365 days) | required |
| Bounded scope | Issue body and full comment thread | Fails if any of: the issue explicitly frames itself as a tracking/epic issue whose sub-items are meant to become separate issues or PRs, or bundles multiple unrelated features/changes together; the thread shows an unresolved design debate with no maintainer decision (debate requires actual back-and-forth in comments, not merely zero comments); a maintainer states the fix touches core internals; the issue is a pure usage/support question with no code change requested. A bulleted checklist of steps needed to accomplish one cohesive change (e.g. several related doc pages for a single feature) is NOT by itself an umbrella — judge whether the sub-items are steps toward one outcome or separate changes bundled together. Judge the size of the work asked for, not the polish of the writeup — a terse bug report or a checklist can still pass. | required |
| Not currently claimed | Assignees field; linked-PR list (state per PR); claim comments in the thread and any maintainer reply to them | Fails if the issue has an assignee, an open linked PR, or a claim comment ("I'll take this" / "working on this") posted within the last 30 days with no clear abandonment signal since (e.g. no long silence from that author plus a maintainer or other user re-opening the ask). Otherwise passes. | required |
| AI-contribution allowed | `CONTRIBUTING.md`, any linked contributor docs, dedicated AI-policy files (`AI_POLICY.md`, `AI_USAGE_POLICY.md`), PR/issue templates | Fails only on an outright ban on AI-generated contributions. Conditions (disclosure, human review, personal understanding, testing) pass — they are terms to follow, not reasons to reject. Silence passes. | required |
| Human-originated issue | Who opened the issue (account name shown in the bundle/UI) | Fails if the issue was opened by a bot account (username ending in `[bot]`, e.g. `cursor[bot]`, `dependabot[bot]`). A bot-filed issue is not evidence any human or maintainer actually wants this; who has since *commented* doesn't matter, only who opened it. Passes for any human-opened issue, maintainer or not. | required |
| Good-first-issue label | Issue labels | Passes if the issue carries a `good first issue` label or clear equivalent | preferred |
| Reproduction / acceptance clarity | Issue body | Passes if a bug report includes reproduction steps, or a feature/task issue includes a clear acceptance checklist or stated "done" condition | preferred |
| No abandoned-attempt pattern | Closed, unmerged linked PRs; repeated claim-then-unassign cycles visible in the thread (e.g. bot messages unassigning inactive claimants) | Fails if there are 2 or more closed, unmerged linked PRs against this issue, OR the thread shows a repeated pattern of contributors claiming and then going inactive (3 or more distinct claim/abandon cycles) — this is the "graveyard issue" pattern: a friendly label hiding real difficulty that has already defeated several contributors. A single closed-unmerged PR alone does not fail this check. | required |

## Verdict rule

Accept the issue only if **every required check** grades `pass`. If any
required check grades `fail`, or grades `unclear` (the needed evidence is
genuinely absent, not merely unchecked), the verdict is `reject`. There
is no third verdict.

Preferred checks never change this verdict. Report their grades on every
issue; in live mode, when ranking multiple accepted candidates, prefer
the one with more preferred-check passes, and say so in the summary.