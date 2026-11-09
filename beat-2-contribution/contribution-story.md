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

2026-10-19: I installed issue-scout over my Unit 1 skill and wrote search-strategy.md with queries traced to my fit profile. The first scan got zero hits from one query, because GitHub treated my quoted "rag OR retrieval OR ..." string as one literal phrase rather than an OR. It also let a mirror repo created that same day (0 stars, 1 commit, copied issues) take all five graded slots, because copied issues look fresh and uncommented, which games a "fewest comments, newest first" ranking. I split the OR query into one search per term and added a "keep at most 2 results per repo" filter; the second scan surfaced the issue I claimed.

**Unit 6: Installed the voice guard and wrote CLAUDE.local.md, after I had already posted my claim**

2026-10-26: I merged my three voice-guide copies into one at ~/.claude/voice-guard/voice-guide.md (the pr-precheck copy already held all 9 rules), added an Upstream section with 4 rules for claims, questions and pings, and installed the hook; the wiring test was stopped with "no body the guard can read". Because I installed it after posting, I ran the guard by hand on my two posted comments, and it stopped both: the claim broke my new "Claim with intent, a first step, and an offer to adjust" rule, and the follow-up's "Opening the PR shortly." broke "Promise investigation, not a fix or a date". My CLAUDE.local.md listed all four of CONTRIBUTING.md's pre-push commands, and running the two I had skipped (`ruff format --check`, `mypy`) showed my new test was not formatted. I skipped the unit's rubric-scope patch and wrote the memory file by hand instead of running a scoped orientation agent.

**Unit 7: Installed the ship-gate and wrote a manifest; it stopped my branch three times before passing**

2026-11-02: My manifest lists CONTRIBUTING.md's four checks (ruff format --check, ruff check, mypy, pytest). The gate stopped first on formatting (three single-quoted lines in my test, fixed in commit 72b2ba7), then my pr-precheck rejected the description for ignoring the repo's PR template and using "Fixes #57" where CONTRIBUTING.md asks for "Closes", then rejected the branch because plan.md's Scope didn't cover the CHANGELOG.md line. After I rewrote the description to the template and added the changelog line to the plan's Scope and Deviations, it printed "ship-gate: PASS" and I updated PR #67's description through Claude Code. I had opened PR #67 before installing the gate, so the original description went out unchecked; the gate fixed what I post from now on, not what was already posted.

**Unit 8: Installed the PR monitor and playbook; it drafted a rebase plan for the merge conflict a real review asked me to fix**

2026-11-09: `--state` first said "NO PR YET" because I had pushed without `-u`, so gh couldn't tie my branch to PR #67; `git push -u` fixed it, and the monitor then reported the PR as CONFLICTING. I wrote the playbook from wheelgap's own numbers (outside PRs #45, #48 and #58 merged 2 to 6 minutes after opening; CONTRIBUTING.md says review comes "within a day or so"), so I nudge only after 3 quiet days. Its draft saw my unpushed merge commit and told me not to redo it, but it assumed main still had a "### Fixed" section under "## Unreleased", which had moved into 0.5.0 when the maintainer released it. I didn't post the draft; I pushed my fixes, wrote my-reply.md myself from my response rules, and posted it through Claude Code, where the voice guard let it through.

---

## Why I Chose This Issue

I searched with issue-scout, using my fit profile from Unit 1: Python, Flask APIs and LLM-integration code, a wish to get better at working inside a larger unfamiliar codebase and at real open-source review, and a wish to avoid pure frontend work and unfamiliar languages. My first scan was broken in two ways (see the Unit 5 entry above). Even so, the scout did one thing well there: it accepted a copied issue from the mirror repo, then told me in its summary not to take it, because the mirror was a day old and the real discussion was upstream. After I fixed the query and added the per-repo cap, the second scan accepted three issues: wheelgap/wheelgap#57, wheelgap/wheelgap#56, and WingedGuardian/GENesis-AGI#4208. It rejected two copied issues from the mirror on bounded scope. On #57 every check passed, including both preferred checks (good-first-issue label, clear acceptance criteria). I did not apply the Unit 6 rubric-scope patch, so my scout gave no difficulty note.

I chose #57 over the others for three reasons. Its "What to do" names an existing function in the same file, `parse_poetry_lock()`, as a model, so I could see the shape of the fix before writing code. The maintainer had merged an outside contributor's PR (#48) that day, and the repo's CONTRIBUTING.md says reviews come "within a day or so", so a PR would actually get read. And it is Python logic with a test suite, which matches my profile. I passed on #4208 because 198 of that repo's last 200 merged PRs were by the owner. I hope to learn how to make a change in someone else's codebase that matches its existing style and how to work with a maintainer's review. Honest read of the scout: the rubric checks did real work on claim status, maintainer activity, scope and AI policy. The ranking did not. "Fewest comments, then newest" turned out to mean just "newest" (every top result had 0 comments), and a copied repo games it easily. The "Maintainer alive" check also passes on a single commit made the day a repo was created. I logged both in scan-notes.md and did not patch the rubric.

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

`parse_pdm_lock()` in `src/wheelgap/sources.py`, and the PDM tests in `tests/test_sources.py`. `parse_poetry_lock()` in the same file was the model for the change but was not modified. The lookups the fix avoids happen later, in `fill_all()` in `src/wheelgap/pypi.py`.

**Toolkit**

- Tool: no tool for this phase; I read the issue body and `src/wheelgap/sources.py` myself
- What it produced: the diagnosis above; the issue's description matched the code
- Difficulties: I missed that the maintainer had replied with an extra requirement (non-normalized names) until plan-check pointed it out
- What changed: I now re-read the whole thread before writing a plan, not only before claiming

---

## Reproduction Process

**Environment Setup**

macOS (Apple Silicon), Python 3.11.15 run through uv 0.13.7 (installed with `brew install uv`), and wheelgap at commit `ba05d4c` on `main`. I forked the repo with `gh repo fork wheelgap/wheelgap --clone` and ran the setup from CONTRIBUTING.md: `uv run --group dev pytest` gave 175 passed, 1 skipped on `main`. The only problems were outside the repo: uv was not installed at first, and `gh repo fork` rejected `--remote` together with a repo name, so I dropped that flag.

**Steps to Reproduce**

1. Write a `pdm.lock` in the old format (`[metadata]` with `lock_version = "3.0"`) whose packages have no `files` key, with a `[metadata.files]` table holding a `"oldpkg 1.0"` entry for one of them.
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
- **My findings:** the bug is real on `main`: even an exact `"name version"` match is ignored, so the reader never looks at `[metadata.files]` at all. The maintainer pointed out one more case on the thread that my repro did not cover by itself: old lockfiles keep the name as PDM wrote it, so a key can be `"Jinja2 3.1.2"` while the package is `jinja2`.

**Toolkit**

- Tool: pytest with a hand-written old-format lockfile, run against the unfixed code via `git stash`; I did not use repro-check, though I installed its real-repo scope in Unit 6
- What it produced: the failing output above, from the same test that passes after the fix
- Difficulties: the issue was written by the maintainer, so there was no user report or repro comment to start from, and I wrote the test before reproducing, so my repro comes from the test rather than the other way round. My first fixture also used a top-level `version = "3.0"`, which isn't how PDM records the format; the maintainer caught it in review
- What changed: the fixture now uses `[metadata]` with `lock_version = "3.0"`; next time I would reproduce on `main` first and grade a repro draft with repro-check before building

---

## Solution Approach

**Analysis**

`parse_pdm_lock()` in `src/wheelgap/sources.py` sets a package's files only when that package has its own `files` key; otherwise it leaves `files=None`, which tells `fill_all()` in `pypi.py` to ask the index. Old lockfiles keep files in `[metadata.files]` instead, and the function never reads that table. `parse_poetry_lock()`, in the same file, already handles Poetry's equivalent legacy table by checking it when a package has no `files` key.

**Proposed Solution**

When a package has no `files` key, look in `[metadata.files]` for the key `f"{name} {version}"`. If that misses, look for an entry whose name normalizes (with `canonicalize_name`) to the same name and whose version matches, since PDM kept the name as it wrote it. If neither is found, leave `files=None` so the existing PyPI lookup still runs. I am not changing the other lockfile readers, the PyPI lookup itself, or how VCS and path packages are skipped.

**Implementation Plan**

1. In `parse_pdm_lock()`, read `legacy_files = data.get("metadata", {}).get("files", {})`.
2. For a package with no `files` key, try the exact `f"{name} {version}"` key.
3. If that misses, scan `legacy_files` for a key whose name part normalizes to the package's name and whose version part matches.
4. If neither matches, leave `files=None`.
5. Add `test_pdm_lock_legacy_metadata_files` in `tests/test_sources.py`: an exact-key package, a package missing from the table, and a capitalized key (`"Jinja2 3.1.2"`) for `jinja2`.
6. Add a line to `CHANGELOG.md` under `## Unreleased`.

Deviations, in the order they happened:

- Step 3 and the `Jinja2` case were not in my first plan. @patrickballard asked for them in his reply to my claim (https://github.com/wheelgap/wheelgap/issues/57#issuecomment-6732635046), and plan-check rejected my first plan for leaving them out.
- Step 6 was not in my first plan's Scope; the ship-gate's pr-precheck rejected the branch for it, and I added it to Scope and Deviations in plan.md.
- After his review on the PR, I merged `main` and kept its new `_add_once(pkgs, seen, pkg)` in `parse_pdm_lock()`, so my lookup sits inside the deduplication added by #55 and #60. I also replaced the test's `assert fake_pypi == []`, which could not fail, with a `fill_all()` call that asserts only `newpkg` is fetched, and changed the fixture header to `[metadata]` with `lock_version = "3.0"`.

**Toolkit**

- Tool: plan-check, with its scope pointed at wheelgap/wheelgap#57 instead of the Path Review sandbox, run as `claude "plan-check: grade my plan"`
- What it produced: first run reject (repro evidence unclear, test plan unclear, plan comment not thread-aware); second run accept, 7 of 7 checks
- Difficulties: it expects a posted repro comment, and this maintainer-written issue had none; I had also written the plan after building, which the rubric does not check. It also accepted a test plan whose network assertion couldn't fail, which only the maintainer caught
- What changed: added the normalized-name fallback and the Jinja2 test, quoted the before-fix failure in plan.md, and posted a follow-up comment crediting the maintainer; a test-plan check could ask "would this assertion fail without the fix?"

---

## Testing Strategy

I ran the same test before and after the fix, then the four checks from CONTRIBUTING.md through the ship-gate.

- Before the fix (fix stashed): `test_pdm_lock_legacy_metadata_files` fails with `AssertionError: assert None == ['oldpkg-1.0-py3-none-any.whl']`.
- After the fix: it passes. `oldpkg` gets its file from the exact key, `jinja2` gets its file from the `"Jinja2 3.1.2"` key, and `newpkg` (not in the table) keeps `files=None`. Then `fill_all(src.packages)` runs against the `fake_pypi` fixture, and the test asserts that every URL fetched is `newpkg`'s. Without the fix, `oldpkg` and `jinja2` would be fetched too, so this assertion fails on the old code, unlike the `assert fake_pypi == []` it replaced.
- `uv run --group dev pytest -k pdm_lock -v`: 9 passed, including `test_pdm_lock_entry_without_files_is_looked_up` (packages missing from the table still go to PyPI) and `test_pdm_lock_extras_variants_count_once` (main's `_add_once` survived my merge).
- `uv run --group dev pytest`: 175 passed, 1 skipped on `main` when I branched; 248 passed, 1 skipped after merging `main` at 0.5.0, which added many tests of its own.
- `uv run --group dev ruff format .`: 28 files left unchanged. `uv run --group dev ruff check .`: All checks passed! `uv run --group dev mypy`: Success: no issues found in 11 source files.

**Toolkit**

- Tool: pytest, ruff and mypy through uv, `git stash` for the before run, and the ship-gate's manifest to run all four checks in a fresh terminal
- What it produced: the before/after results and suite counts above, and a gate STOP on `ruff format --check` the first time
- Difficulties: before the ship-gate, I had skipped two of the four checks (`ruff format`, `mypy`), and my original network assertion passed with or without the fix
- What changed: the manifest now runs all four checks every time; the `fill_all()` assertion replaced the one that couldn't fail

---

## Implementation Notes

**2026-10-19**

Installed issue-scout and wrote search-strategy.md. Scan 1 had a query with 0 results and a one-day-old mirror repo filling all five graded slots. Split the query into one search per term, added a max-2-per-repo filter, and ran scan 2, which accepted wheelgap#57, wheelgap#56 and GENesis-AGI#4208.

**2026-10-26**

Picked #57 and claimed it with `gh issue comment`. Forked and cloned wheelgap; the suite was 175 passed, 1 skipped on `main`.

Installed the voice guard and wrote CLAUDE.local.md. The guard stopped both of my already-posted comments when I ran it by hand. Running the two checks I had skipped showed `ruff format --check` failing on my test.

**2026-11-02**

Added the `[metadata.files]` lookup to `parse_pdm_lock()` and a test with an old-format lockfile. Wrote plan.md after building it, which was the wrong order. plan-check rejected it: the maintainer had replied asking for a fallback to normalized names and a test with a capitalized name, and I hadn't seen the reply.

Added the normalized-name fallback and the `Jinja2 3.1.2` case, showed the test fails with the fix stashed, posted a follow-up comment crediting the maintainer, and re-ran plan-check: accept. Committed on `fix/57-pdm-legacy-metadata-files` with a CHANGELOG line, pushed, and opened PR #67 after one failed `gh pr create`.

Installed the ship-gate and wrote the manifest. Three STOPs (formatting, PR template and "Closes", changelog outside the plan's Scope), each fixed, then PASS. Updated PR #67's description to the template through Claude Code.

**2026-11-09**

Installed the PR monitor and wrote my playbook. Found the maintainer's review and a merge conflict. Merged `main` (dc21681), changed the test (6268810), pushed, and posted my reply through the voice guard.

**Toolkit**

- Tool: git and gh (branch, stash, commit, merge, push, PR), plus plan-check for the re-grade
- What it produced: commits c0771dd, 72b2ba7, dc21681 and 6268810 on `fix/57-pdm-legacy-metadata-files`, and PR #67
- Difficulties: I built on `main` before making a branch, which plan-check pointed out; the merge with `main` conflicted in `parse_pdm_lock()`, and git put my changelog line in the 0.5.0 list instead of `## Unreleased`. The plan-check re-grade also noted that the fallback compares versions as exact strings, so `1.0` and `1.0.0` would not match
- What changed: moved the work to a branch before committing; resolved the conflict keeping `_add_once`, and moved the changelog line by hand; I left the version-string point out of this PR because the issue didn't ask for it

---

## Pull Request

**PR Link:** https://github.com/wheelgap/wheelgap/pull/67

**PR Description:** It follows the repo's PR template: `Closes #57`, a "What this changes" section explaining that `parse_pdm_lock()` never read the old `[metadata.files]` table and that the fix tries the exact `"name version"` key and then a normalized-name match (crediting that part to @patrickballard's suggestion), and the template's checklist, with the README box left unticked and an N/A reason. Below that it shows the test failing before the fix (`AssertionError: assert None == ['oldpkg-1.0-py3-none-any.whl']`), the checks passing after it, and an "AI use" section saying I used Claude Code to help draft the fix, test and description and ran every command myself.

**Maintainer Feedback:**

- 2026-11-09: @patrickballard reviewed: "the logic here is right", then asked for three changes: merge `main` and keep `_add_once` (with the changelog line under `## Unreleased` and a thanks link), drop `assert fake_pypi == []` because it can't fail (or use `fill_all()` for a real offline check), and use `[metadata]` with `lock_version = "3.0"` in the fixture.
- 2026-11-09: I made all three in dc21681 and 6268810, pushed them together, and replied with what changed and the checks' output, asking one question about the changelog's place after the 0.5.0 release: https://github.com/wheelgap/wheelgap/pull/67#issuecomment-6733399562
- 2026-11-09: @patrickballard: "that's all three done, and CI is green", and confirmed the top of `## Unreleased` is the right place for the changelog line, since entries there go into the next release, newest first.
- 2026-11-09: no reply needed. Another PR (#8) had landed in `## Unreleased` and conflicted with my line, so I merged `main` again (5e4488c), kept both changelog lines with mine below #8's, and re-ran the four checks (267 passed, 1 skipped) before pushing.
- 2026-11-09: after a closer read, @patrickballard said the code is right and the offline test "really proves it", and asked for two small changes before merging: `[metadata.files]` was written by PDM until 2.8 (lock_version 4.0 to 4.2 too), so the changelog line and code comment shouldn't say "lock_version < 4"; and the fixture mixed eras, since real 3.x locks use `{file = ...}` entries, not `{url = ...}`.
- 2026-11-09: I changed both in 6fd6a50: the changelog line now reads "Read package files from the `[metadata.files]` table in older `pdm.lock` files (PDM before 2.8) instead of looking them up on PyPI.", the code comment says "PDM before 2.8", and `oldpkg`'s fixture entry uses the `{file = ...}` form while `Jinja2` keeps `{url = ...}`, so both shapes are covered. The four checks passed before the commit, and I replied with the commit and their output: https://github.com/wheelgap/wheelgap/pull/67#issuecomment-6733774633

**Status:** Awaiting review

**Toolkit**

- Tool: the ship-gate (`check_gate.py --branch`) with my manifest, then `gh pr edit` through Claude Code; I opened the PR itself with `gh pr create` before installing the gate
- What it produced: three STOPs (formatting; description not following the PR template and using "Fixes"; changelog outside the plan's Scope), then "ship-gate: PASS" and the updated PR description
- Difficulties: the first `gh pr create` failed with "No commits between main and fix/57-pdm-legacy-metadata-files" because gh looked for my branch on the upstream repo; and because the gate came after the PR, the description reviewers first saw had not been checked
- What changed: passed `--repo` and `--head nxxis:...` explicitly; fixed the test format, rewrote the description to the template, and added the changelog line to plan.md's Scope and Deviations

---

## Learnings and Reflections

**Technical Skills Gained**

How lockfile formats store distribution files (newer PDM per package, older PDM and Poetry in a shared table) and how wheelgap reads each one. Using `packaging.utils.canonicalize_name` to compare package names. Writing a test that fails without the fix, and learning from review that an assertion has to be able to fail: `assert fake_pypi == []` after parsing proved nothing, while asserting what `fill_all()` fetches does. Resolving a merge conflict so that another contributor's change (`_add_once`) survives. Opening a PR from a fork with `gh pr create --repo ... --head owner:branch`.

**Challenges Overcome**

Getting my scout to give useful results: a query that silently returned nothing and a copied repo that filled the ranking. Catching up with a maintainer reply I had missed, which meant changing the code, the test, the plan and posting a follow-up. Getting through three ship-gate stops. Merging a `main` that had moved a release ahead of my branch, including a changelog line that git put in the wrong section.

**What I'd Do Differently Next Time**

Because I was at a conference, I did all four units' work in one session under an approved extension instead of week by week, and I did it out of order: I claimed and opened the PR before installing the voice guard and ship-gate, so both of my early comments and the first PR description went out unchecked, and the guard later stopped the comments when I ran it by hand. Next time I'd install the hooks before posting anything, reproduce on `main` before writing code, write the plan before building, and re-read the issue thread just before planning.

---

## Resources Used

- wheelgap CONTRIBUTING.md: https://github.com/wheelgap/wheelgap/blob/main/CONTRIBUTING.md (setup, claiming, pre-push checks, AI policy)
- wheelgap's PR template: https://github.com/wheelgap/wheelgap/blob/main/.github/pull_request_template.md
- @patrickballard's reply on the issue, which asked for the normalized-name fallback: https://github.com/wheelgap/wheelgap/issues/57#issuecomment-6732635046
- @patrickballard's review on PR #67 (merge conflict, the assertion that couldn't fail, the fixture header): https://github.com/wheelgap/wheelgap/pull/67
- `parse_poetry_lock()` in `src/wheelgap/sources.py`, the model for the fix