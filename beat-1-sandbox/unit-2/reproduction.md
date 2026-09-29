# Unit 2 — Claim and Reproduce

GitHub username: nxxis

## Claim comment

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/68#issuecomment-5881676639

> Hi! I'd like to pick up this keyword-search issue as my first contribution here. The report that `KeywordSearcher.index([])` raises a `ZeroDivisionError` — while `search()` on an empty index already returns `[]` cleanly — looks like a well-scoped, single-function bug. I'd like to dig into the divide in `index()` and report back what I find before proposing a fix.

## Repro comment

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/68#issuecomment-5882341492

> Repro report: `KeywordSearcher.index([])` raises `ZeroDivisionError`
>
> Environment: Python 3.11.15, macOS 27.0 (arm64), `rank-bm25` 0.2.2 (current PyPI release), tested at commit `f89c06f` (upstream `main`, same commit as this fork's HEAD).
>
> Repro:
>
> ```
> from rag.retriever.keyword_search import KeywordSearcher
>
> searcher = KeywordSearcher()
> searcher.index([])
> ```
>
> Actual output:
>
> ```
> Traceback (most recent call last):
>   File "<string>", line 5, in <module>
>   File "rag/retriever/keyword_search.py", line 25, in index
>     self.bm25 = BM25Okapi(tokenized_corpus)
>   File ".venv-repro/lib/python3.11/site-packages/rank_bm25.py", line 27, in __init__
>     nd = self._initialize(corpus)
>   File ".venv-repro/lib/python3.11/site-packages/rank_bm25.py", line 52, in _initialize
>     self.avgdl = num_doc / self.corpus_size
> ZeroDivisionError: division by zero
> ```
>
> Expected: per the issue, `search()` already handles an empty index gracefully (`if not self.bm25 or not self.chunks: ... return []`), so `index([])` shouldn't raise either.
>
> Root cause: [`index()`](https://github.com/nxxis/pathreview-ai301-fa26-s1/blob/f89c06fc3ff292df2a04a39ac51319d32a76b779/rag/retriever/keyword_search.py#L25) unconditionally calls `BM25Okapi(tokenized_corpus)` even when `tokenized_corpus` is `[]`. Inside `rank_bm25`, `_initialize` sets `corpus_size = len(corpus)` (`0` here), then divides `num_doc / corpus_size` — a `rank_bm25` limitation `index()` needs to guard against before calling in, e.g. by skipping the `BM25Okapi` construction entirely on an empty `chunks` list, matching the pattern `search()` already uses.
>
> Conclusion: reproduced exactly as described — unhandled `ZeroDivisionError: division by zero` from `rank_bm25`'s `_initialize`, not caught anywhere in `index()`.
>
> I used an AI assistant (Claude Code) to help draft this write-up; every command and output above was run and checked by me directly.

## Reflection

### Run history

1. Built an initial rubric from the shipped evidence-guide template's five proof families (Environment, Steps, Behavior shown, Honesty, Comms), plus a required disclosure check the assignment explicitly flagged as necessary for the category floor.
2. Hand-graded the two practice calibration packages before spending eval credit: `calib-03` (a confident-looking report that actually swapped the issue's input syntax, producing a different failure class — correctly `hold`) and `calib-02` (an enthusiastic report with zero evidence at all — correctly `hold`, and revealed a missing "steps are followable" check).
3. First full eval run: 17/20. Three misses (`pkg-05`, `pkg-10`, `pkg-12`), all gold `accept`, concentrated in the `clear-accept` category — a signal the rubric was too literal-minded about legitimate reports, not too lenient.
4. Debugged all three: `pkg-05`/`pkg-12` were failing "Steps followable" for not pasting whole files verbatim, even though the operative trigger detail was stated explicitly; `pkg-10` was failing "Claims-backed" for a corroborating remark that added no new fact beyond already-shown output. Reworded both checks to focus on operative/new-fact distinctions rather than literal completeness.
5. Canary-checked the loosening against `pkg-02` (a genuinely wrong-target package) before trusting the fix — it held rejected through every round.
6. Full run 2: 17/20 again, but with a *new* miss (`pkg-16`, a false accept with nothing failing at all) alongside a re-emerged `pkg-09` miss. Traced `pkg-16` to a real structural gap: no check anywhere examined whether the tested software version was current, and the candidate had reproduced on `pandas 1.5.3` against a `3.0.5` latest release, ignoring the project's own template demand to confirm on latest/main. Added a new required version-staleness check.
7. That new check regressed `pkg-07` (a legitimate `accept` testing a non-latest-but-newer version, on a repo whose template doesn't demand latest/main). Reworded the check to gate strictly on whether the *project's own template* demands latest/main confirmation, rather than a blanket "must be latest release" bar.
8. Also re-tightened "Claims-backed" after `pkg-09` regressed: distinguished a claim that *repeats the same already-shown outcome across more trials* (ruling out flakiness — should pass) from a claim that introduces a *new, different* unshown fact (should fail).
9. Confirming full run: 19/20, PASS, category floor met in every category (`disclosure 1/1`, `no-evidence 4/4`, `unfollowable-comms 3/3`, `wrong-target 4/4`, `clear-accept 7/8`). Saved as `eval-run.txt`.
10. Ran the rubric in claim-only live mode on my draft claim comment for issue #68 — passed clean — then posted it. Reproduced the bug myself in a fork sandbox, wrote the repro report, ran the full-package live-mode check, revised twice for voice-guide issues (a softened overclaim, trimmed local paths, a dangling disclosure reference, fork-naming clarity), reverified, then posted.

### Package analysis

**`pkg-03`** — gold verdict `accept`; my rubric's final run graded it `reject` on "Every confirmation claim is backed by shown output." The report reproduces a ripgrep multiline-replace line-numbering bug with an exact, fully-backed match to the issue's input, command, and output. The one flagged sentence — "Dropping `-r '$1'` from the same command reports 1, 4, 7, 10 correctly" — asserts a specific, different numeric result from a second command variant that is never actually shown anywhere in the report. I judged this a genuine, defensible miss rather than a rubric bug: the core reproduction is airtight, but this aside introduces new unshown data, not merely a repetition or restatement of something already established (unlike, say, `pkg-09`'s "ran this 5 times" remark, which repeats an already-shown outcome and correctly passes). I considered loosening the check further to forgive this, but rejected that fix because the same looser wording would also have forgiven `pkg-02`'s genuinely bad unbacked claims ("ran ten times, always crashes the same way," a second unshown environment) — and `pkg-02` needs to stay rejected on its own separate grounds regardless, but I didn't want Claims-backed itself contributing a false pass there. I left `pkg-03` as one of the assignment's acknowledged "genuinely arguable" cases rather than keep loosening a check whose stricter reading is doing real work elsewhere.

### Check rationale

The check as it currently reads in my rubric:

> **Tested version is current, or staleness is justified** | The report's stated tool/library version against the repo-facts block's latest release and the version the issue was originally filed against; whether the issue or its bug-report template asks for confirmation on the latest version or main branch; any reasoning the report gives for testing an older version | Passes if the tested version is the latest release, OR — when the repo's own bug-report template does NOT explicitly demand confirmation on the latest release/main branch — the tested version is newer than the version the issue was originally filed against, with the report saying so. Fails when the repo's template explicitly demands latest/main confirmation and the report doesn't meet or acknowledge that, or when the tested version isn't even newer than the issue's own originally-filed version, with no justification given. | required

This check didn't exist in my rubric until `pkg-16` exposed the gap: a report that reproduced a pandas bug on version `1.5.3`, several major versions behind the `3.0.5` latest release, while the project's own bug-report template explicitly asks reporters to confirm on the latest release or main branch — and got a clean pass on every other check, because none of them looked at version relevance at all. I made this check gate strictly on the *project's own stated demand* (rather than a universal "must be latest" bar) after a first, stricter version of it wrongly rejected `pkg-07`, a legitimate accept where the tested version was older than latest but the project's template asked for no such confirmation, and the report was honest about the version gap. The rationale: a version check should measure against what the specific project actually asks for, not impose a blanket freshness bar every project doesn't equally demand.

### Trade-offs

The "Claims-backed" check's final wording — distinguishing a claim that repeats an already-shown outcome from one that introduces a new unshown fact — trades some strictness for recognizing that repeated trials in service of an honest finding (especially a cannot-reproduce) are due diligence, not overclaiming. The risk is a report could still smuggle a new, false-but-plausible-sounding claim past this check as long as it's phrased to sound like mere repetition rather than a distinct assertion; I accepted this risk because the alternative (treating every confirmatory remark as needing its own shown test) was actively punishing careful, honest non-repro reports like `pkg-09` and `pkg-10`, which the assignment explicitly wants rewarded. Similarly, gating the version-staleness check on the project's own template demand rather than a universal latest-release bar means a project with no stated freshness expectation gets a more lenient bar than one that explicitly asks for it — which I judged correct (matching what the project itself considers due diligence) rather than a loophole, but it does mean the check's strictness varies by repo rather than applying one fixed standard everywhere.
