# Contribution: pdm.lock: read the old [metadata.files] table (lock_version < 4)

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** [1]
**Student:** Sudip Sharma
**GitHub Username:** nxxis
**Issue:** https://github.com/wheelgap/wheelgap/issues/57
**Pull Request:** https://github.com/wheelgap/wheelgap/pull/67

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5: Built issue-scout and fixed two scan bugs before it found a usable issue**

I installed issue-scout over my Unit 1 skill and wrote search-strategy.md with queries traced to my fit profile. The first scan got zero hits from one query, because GitHub treated my quoted "rag OR retrieval OR ..." string as one literal phrase rather than an OR. It also let a mirror repo created that same day (0 stars, 1 commit, copied issues) take all five graded slots, because copied issues look fresh and uncommented, which games a "fewest comments, newest first" ranking. I split the OR query into one search per term and added a "keep at most 2 results per repo" filter; the second scan surfaced the issue I claimed.

**Unit 6: Claimed wheelgap/wheelgap#57; checked my comments by hand instead of with the voice-guard hook**

I read wheelgap's CONTRIBUTING.md (claim by commenting, AI tools allowed if you ran the tests yourself and can explain the change) and posted my claim with `gh issue comment`. I did not install the Unit 6 voice-guard hook or write a CLAUDE.local.md before posting, so my comments were checked only by hand against my Unit 2 voice guide. That fell short: plan-check later flagged that my claim stated the plan as decided and never offered to adjust, which is exactly the kind of line the guard exists to catch. Next time I would install the guard before posting anything upstream.

**Unit 7: Ran the plan and project checks by hand instead of through the ship-gate; opened the real PR**

I did not install the Unit 7 ship-gate before opening my PR, so I ran its pieces by hand: the project's own `uv run --group dev pytest` (176 passed, 1 skipped) and `ruff check .` (clean), plus plan-check, which rejected my first plan for ignoring the maintainer's follow-up review and accepted the revised one. Opening the PR failed once with "No commits between main and fix/57-pdm-legacy-metadata-files" because `gh` looked for my branch on the upstream repo; passing `--repo wheelgap/wheelgap --head nxxis:fix/57-pdm-legacy-metadata-files` fixed it. Next time I would install the ship-gate first so these checks can't be skipped.

**Unit 8: [one line on the tool work]**

[2-4 sentences: the PR-monitor and your playbook. What it read, what it
drafted, what you did with the draft.]

---

## Why I Chose This Issue

I searched with issue-scout, using my fit profile from Unit 1: Python, Flask APIs and LLM-integration code, a wish to get better at working inside a larger unfamiliar codebase and at real open-source review, and a wish to avoid pure frontend work and unfamiliar languages. My first scan was broken in two ways (see the Unit 5 entry above). Even so, the scout did one thing well there: it accepted a copied issue from the mirror repo, then told me in its summary not to take it, because the mirror was a day old and the real discussion was upstream. After I fixed the query and added the per-repo cap, the second scan accepted three issues: wheelgap/wheelgap#57, wheelgap/wheelgap#56, and WingedGuardian/GENesis-AGI#4208. It rejected two copied issues from the mirror on bounded scope. On #57 every check passed, including both preferred checks (good-first-issue label, clear acceptance criteria). My scout's rubric is the Unit 1 version and has no difficulty-note section, so it gave no difficulty note.

I chose #57 over the others for three reasons. Its "What to do" names an existing function in the same file, `parse_poetry_lock()`, as a model, so I could see the shape of the fix before writing code. The maintainer had merged an outside contributor's PR (#48) that day, and the repo's CONTRIBUTING.md says reviews come "within a day or so", so a PR would actually get read. And it is Python logic with a test suite, which matches my profile. I passed on #4208 because 198 of that repo's last 200 merged PRs were by the owner. I hope to learn how to make a change in someone else's codebase that matches its existing style and how to work with a maintainer's review.

Honest read of the scout: the rubric checks did real work on claim status, maintainer activity, scope and AI policy. The ranking did not. "Fewest comments, then newest" turned out to mean just "newest" (every top result had 0 comments), and a copied repo games it easily. The "Maintainer alive" check also passes on a single commit made the day a repo was created. I logged both in scan-notes.md and did not patch the rubric.

**Claim comment:** https://github.com/wheelgap/wheelgap/issues/57#issuecomment-6732626421

**Toolkit**

- Tool: issue-scout (my Unit 1 rubric plus my search-strategy.md), run twice with `claude "issue-scout: run a scan"`
- What it produced: scan 1 accepted only a copied issue from a one-day-old mirror repo and recommended against it; scan 2 accepted wheelgap#57, wheelgap#56 and GENesis-AGI#4208 and rejected two mirror issues on bounded scope
- Difficulties: one query returned 0 results (quoted OR string read as a phrase); a copied repo filled all five graded slots in scan 1 and two in scan 2; "Maintainer alive" passes on one same-day commit
- What changed: split the OR query into one search per term and added a max-2-per-repo ranking filter; would still add a repo-age or star floor and tighten "Maintainer alive" in the rubric

---

## Understanding the Issue

**Problem Description**

Older PDM lockfiles (lock_version 3 and earlier) don't list each package's wheel and sdist files on its own `[[package]]` entry the way newer ones do. They keep every package's files in one shared `[metadata.files]` table, keyed by `"name version"`. wheelgap's PDM reader, `parse_pdm_lock()`, never read that table.

**Expected Behavior**

For an old-format `pdm.lock`, wheelgap should read each package's file list from `[metadata.files]` and judge it offline, the same way it already handles newer PDM lockfiles and Poetry's equivalent legacy table.

**Current Behavior**

Every package without its own `files` key was left with an unknown file list, so wheelgap looked it up on PyPI over the network, even though the lockfile already had the answer. The result was usually still right, but the check was no longer offline.

**Affected Components**

`parse_pdm_lock()` in `src/wheelgap/sources.py`, and the PDM tests in `tests/test_sources.py`. `parse_poetry_lock()` in the same file was the model for the change but was not modified.

**Toolkit**

- Tool: no tool for this phase; I read the issue body and `src/wheelgap/sources.py` myself
- What it produced: the diagnosis above; the issue's description matched the code
- Difficulties: none met in reading the code, but I missed that the maintainer had replied with an extra requirement (non-normalized names) until plan-check pointed it out
- What changed: I now re-read the whole thread before writing a plan, not only before claiming

---

## Reproduction Process

**Environment Setup**

macOS (Apple Silicon), Python 3.11.15 run through uv 0.13.7 (installed with `brew install uv`), and wheelgap at commit `ba05d4c` on `main`. I forked the repo with `gh repo fork wheelgap/wheelgap --clone` and ran the setup from CONTRIBUTING.md: `uv run --group dev pytest` gave 175 passed, 1 skipped on `main`. The only problems were outside the repo: uv was not installed at first, and `gh repo fork` rejected `--remote` together with a repo name, so I dropped that flag.

**Steps to Reproduce**

1. Write a `pdm.lock` in the old format (`version = "3.0"`) whose packages have no `files` key, with a `[metadata.files]` table holding a `"oldpkg 1.0"` entry for one of them.
2. Load it with `wheelgap.sources.load_inputs([path])`, which calls `parse_pdm_lock()`.
3. Observed result: `oldpkg`'s `files` is `None`, so wheelgap would look the package up on PyPI even though the lockfile lists its wheel.

**Reproduction Evidence**

- **Commit showing reproduction:** none. I reproduced it by running my new test with the fix stashed (`git stash push -- src/wheelgap/sources.py`) on branch `fix/57-pdm-legacy-metadata-files`
- **Screenshots/logs:**
  ```
  >       assert pkgs["oldpkg"].files == ["oldpkg-1.0-py3-none-any.whl"]
  E       AssertionError: assert None == ['oldpkg-1.0-py3-none-any.whl']
  E        +  where None = Package(name='oldpkg', version='1.0', files=None, skip=None, error=None, origin='pdm.lock', markers=None).files
  FAILED tests/test_sources.py::test_pdm_lock_legacy_metadata_files
  1 failed, 21 deselected in 0.04s
  ```
- **My findings:** the bug is real on current `main`: even an exact `"name version"` match is ignored, so the reader never looks at `[metadata.files]` at all. The maintainer pointed out one more case on the thread that my repro did not cover by itself: old lockfiles keep the name as PDM wrote it, so a key can be `"Jinja2 3.1.2"` while the package is `jinja2`.

**Toolkit**

- Tool: pytest with a hand-written old-format lockfile, run against the unfixed code via `git stash`; I did not use repro-check this round
- What it produced: the failing output above, from the same test that passes after the fix
- Difficulties: the issue was written by the maintainer, so there was no user report or repro comment to start from, and I wrote the test before reproducing, so my repro comes from the test rather than the other way round
- What changed: next time I would reproduce on `main` first and grade a repro draft with repro-check before building

---

## Solution Approach

**Analysis**

`parse_pdm_lock()` in `src/wheelgap/sources.py` sets a package's files only when that package has its own `files` key; otherwise it leaves `files=None`, which tells the rest of wheelgap to ask PyPI. Old lockfiles keep files in `[metadata.files]` instead, and the function never reads that table. `parse_poetry_lock()`, in the same file, already handles Poetry's equivalent legacy table by checking it when a package has no `files` key.

**Proposed Solution**

When a package has no `files` key, look in `[metadata.files]` for the key `f"{name} {version}"`. If that misses, look for an entry whose name normalizes (with `canonicalize_name`) to the same name and whose version matches, since PDM kept the name as it wrote it. If neither is found, leave `files=None` so the existing PyPI lookup still runs. I am not changing the other lockfile readers or the PyPI fallback itself.

**Implementation Plan**

1. In `parse_pdm_lock()`, read `legacy_files = data.get("metadata", {}).get("files", {})`.
2. For a package with no `files` key, try the exact `f"{name} {version}"` key.
3. If that misses, scan `legacy_files` for a key whose name part normalizes to the package's name and whose version part matches.
4. If neither matches, leave `files=None`.
5. Add `test_pdm_lock_legacy_metadata_files` in `tests/test_sources.py`: an exact-key package, a package missing from the table, and a capitalized key (`"Jinja2 3.1.2"`) for `jinja2`, plus `fake_pypi == []` to show no network calls.
6. Add a line to `CHANGELOG.md` under `## Unreleased`.

Deviation: steps 3 and the `Jinja2` case in step 5 were not in my first plan. @patrickballard asked for them in his reply to my claim (https://github.com/wheelgap/wheelgap/issues/57#issuecomment-6732635046), and plan-check rejected my first plan for leaving them out. I added both and said so in a follow-up comment.

**Toolkit**

- Tool: plan-check, with its scope.md pointed at wheelgap/wheelgap#57 instead of the Path Review sandbox, run as `claude "plan-check: grade my plan"`
- What it produced: first run reject (repro evidence unclear, test plan unclear, plan comment not thread-aware); second run accept, 7 of 7 checks
- Difficulties: it expects a posted repro comment, and this maintainer-written issue had none; I had also written the plan after building, which the rubric does not check
- What changed: added the normalized-name fallback and the Jinja2 test, quoted the before-fix failure in plan.md, and posted a follow-up comment crediting the maintainer

---

## Testing Strategy

I ran the same test before and after the fix, then the project's checks from CONTRIBUTING.md.

- Before the fix (fix stashed): `test_pdm_lock_legacy_metadata_files` fails with `AssertionError: assert None == ['oldpkg-1.0-py3-none-any.whl']`.
- After the fix: it passes. It checks that `oldpkg` gets its file from the exact key, `jinja2` gets its file from the `"Jinja2 3.1.2"` key, `newpkg` (not in the table) keeps `files=None`, and `fake_pypi` records no calls.
- `uv run --group dev pytest -k pdm_lock -v`: 8 passed, including the existing `test_pdm_lock_entry_without_files_is_looked_up`, which shows packages missing from the table still go to PyPI.
- `uv run --group dev pytest`: 175 passed, 1 skipped on `main`; 176 passed, 1 skipped on the branch.
- `uv run --group dev ruff check .`: All checks passed!

I did not run `ruff format` or `mypy`, which CONTRIBUTING.md also lists; CI on the PR runs them.

**Toolkit**

- Tool: pytest and ruff through uv, and `git stash` to run the new test against the unfixed code
- What it produced: the before/after results and suite counts above
- Difficulties: I skipped two of the four pre-push commands in CONTRIBUTING.md (`ruff format`, `mypy`)
- What changed: a ship-gate manifest listing all four would have caught that; I would set one up first next time

---

## Implementation Notes

Note on timing: I was away at a conference during the weeks of units 5 to 7, and my instructor approved my request to complete the Assignment 5 work later. I did the work for all four units in one session on November 8 and 9, 2026, in the order below, so every entry carries those dates.

**2026-11-08**

Installed issue-scout and wrote search-strategy.md. Scan 1 had a query with 0 results and a one-day-old mirror repo filling all five graded slots. Split the query into one search per term, added a max-2-per-repo filter, and ran scan 2, which accepted wheelgap#57, wheelgap#56 and GENesis-AGI#4208.

**2026-11-08**

Picked #57 and claimed it with `gh issue comment`. Forked and cloned wheelgap; the suite was 175 passed, 1 skipped on `main`.

**2026-11-09**

Added the `[metadata.files]` lookup to `parse_pdm_lock()` and a test with an old-format lockfile. Wrote plan.md after building it, which was the wrong order. plan-check rejected it: the maintainer had replied asking for a fallback to normalized names and a test with a capitalized name, and I hadn't seen the reply.

**2026-11-09**

Added the normalized-name fallback and the `Jinja2 3.1.2` case, showed the test fails with the fix stashed, posted a follow-up comment crediting the maintainer, and re-ran plan-check: accept. Committed on `fix/57-pdm-legacy-metadata-files` with a CHANGELOG line, pushed, and opened PR #67 after one failed `gh pr create`.

**Toolkit**

- Tool: git and gh (branch, stash, commit, push, PR), plus plan-check for the re-grade
- What it produced: commit `c0771dd` on `fix/57-pdm-legacy-metadata-files` and PR #67
- Difficulties: I built on `main` before making a branch, and plan-check pointed that out; the re-grade also noted that the fallback compares versions as exact strings, so `1.0` and `1.0.0` would not match
- What changed: moved the work to a branch before committing; I left the version-string point out of this PR because the issue didn't ask for it

---

## Pull Request

**PR Link:** https://github.com/wheelgap/wheelgap/pull/67

**PR Description:** It explains that `parse_pdm_lock()` never read the old `[metadata.files]` table and that the fix tries the exact `"name version"` key and then a normalized-name match, crediting the second part to @patrickballard's review. It says `Fixes #57`, shows the test failing before the fix (`AssertionError: assert None == ['oldpkg-1.0-py3-none-any.whl']`), 176 passed, 1 skipped after it (175 on `main`), and `ruff check .` clean, and says I used Claude Code to help draft the fix, test and description and ran every command myself.

**Maintainer Feedback:**

- 2026-11-09: none yet

**Status:** Awaiting review

**Toolkit**

- Tool: the `gh` CLI (`gh repo set-default`, `gh pr create`); I opened the PR without the ship-gate
- What it produced: PR #67 on wheelgap/wheelgap from `nxxis:fix/57-pdm-legacy-metadata-files`
- Difficulties: the first `gh pr create` failed with "No commits between main and fix/57-pdm-legacy-metadata-files"; I also removed Claude's co-author line from the commit after pushing, which needed a force-push
- What changed: passed `--repo` and `--head` explicitly, and used `git push --force-with-lease` on my own branch

---

## Learnings and Reflections

**Technical Skills Gained**

How lockfile formats store distribution files (newer PDM per package, older PDM and Poetry in a shared table) and how wheelgap reads each one. Using `packaging.utils.canonicalize_name` to compare package names. Writing a test that fails without the fix and checks there are no network calls with a monkeypatched fixture (`fake_pypi`). Opening a PR from a fork with `gh pr create --repo ... --head owner:branch`.

**Challenges Overcome**

Getting my scout to give useful results: a query that silently returned nothing and a copied repo that filled the ranking. Catching up with a maintainer reply I had missed, which meant changing the code, the test, the plan and posting a follow-up. Opening the PR when `gh` looked for my branch in the wrong repo.

**What I'd Do Differently Next Time**

Because I was at a conference, I did all four units' work in one session under an approved extension instead of week by week. Compressing it that way is why I skipped the voice guard, ship-gate and repro-check, and wrote my plan after the code. Next time I would still spread the steps out, even within a short window. I'd reproduce on `main` first, then plan, then build. I'd re-read the issue thread just before planning. And I'd install the hooks before posting anything, so the checks I skipped would have run automatically.

---

## Resources Used

- wheelgap CONTRIBUTING.md: https://github.com/wheelgap/wheelgap/blob/main/CONTRIBUTING.md (setup, claiming, pre-push checks, AI policy)
- @patrickballard's reply on the issue, which asked for the normalized-name fallback: https://github.com/wheelgap/wheelgap/issues/57#issuecomment-6732635046
- `parse_poetry_lock()` in `src/wheelgap/sources.py`, the model for the fix
