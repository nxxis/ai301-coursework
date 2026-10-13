# Unit 3 — Plan and Build

GitHub username: nxxis

## Plan comment

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/68#issuecomment-6152849505

> Building on my reproduction above (https://github.com/codepath/pathreview-ai301-fa26-s1/issues/68#issuecomment-5882341492), my proposed plan is: in `index()`, skip the `BM25Okapi` construction (`rag/retriever/keyword_search.py:25`), explicitly setting `self.bm25 = None` in that branch so `search()`'s existing empty-index guard continues to handle it correctly — no change to `search()` itself. Per the issue's note, I'd also remove the `xfail(strict=True)` marker on `tests/unit/test_keyword_search.py::TestKeywordSearcher::test_empty_index` as part of the fix, rather than leave it in place.
>
> Test plan: re-run `searcher.index([])` — before: `ZeroDivisionError: division by zero`; after: no exception, `searcher.bm25 is None`, `searcher.chunks == []`. `test_empty_index` should go from `XFAIL` to passing once the marker is removed.
>
> Open question I haven't investigated yet: whether a corpus of only empty-string entries (e.g. `{"text": ""}`) hits a related zero-length-average issue inside `rank_bm25` for different reasons — flagging it now rather than assuming this fix covers it.
>
> Happy to adjust if you'd rather a different approach. Will report back on how it goes.
>
> I used an AI assistant to help draft this plan; the diagnosis, approach, and test plan reflect my own understanding and were reviewed by me directly.

<!-- CONFIRM this matches what's actually posted on GitHub before uploading — paste the real text if anything differs. -->

## Branch

fix/68-empty-index-zerodivision

## Evidence

**Before (unfixed code, `f89c06f`)**

```
$ git rev-parse --short HEAD; git status --short rag tests
f89c06f

$ PYTHONPATH=. .venv-repro/bin/python repro.py
Traceback (most recent call last):
  File "repro.py", line 4, in <module>
    searcher.index([])
  File "rag/retriever/keyword_search.py", line 25, in index
    self.bm25 = BM25Okapi(tokenized_corpus)
                ^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File ".venv-repro/lib/python3.11/site-packages/rank_bm25.py", line 83, in __init__
    super().__init__(corpus, tokenizer)
  File ".venv-repro/lib/python3.11/site-packages/rank_bm25.py", line 27, in __init__
    nd = self._initialize(corpus)
         ^^^^^^^^^^^^^^^^^^^^^^^^
  File ".venv-repro/lib/python3.11/site-packages/rank_bm25.py", line 52, in _initialize
    self.avgdl = num_doc / self.corpus_size
                 ~~~~~~~~^~~~~~~~~~~~~~~~~~
ZeroDivisionError: division by zero

$ PYTHONPATH=. .venv-repro/bin/python stale.py
keyword_index_built  chunk_count=1
keyword_search_complete  query_len=1 results_count=1
after non-empty index: 1 result(s)
Traceback (most recent call last):
  File "stale.py", line 6, in <module>
    searcher.index([])
  File "rag/retriever/keyword_search.py", line 25, in index
    self.bm25 = BM25Okapi(tokenized_corpus)
                ^^^^^^^^^^^^^^^^^^^^^^^^^^^
  ...
ZeroDivisionError: division by zero

$ .venv-repro/bin/pytest tests/unit/test_keyword_search.py::TestKeywordSearcher::test_empty_index -v
tests/unit/test_keyword_search.py::TestKeywordSearcher::test_empty_index XFAIL [100%]
============================== 1 xfailed in 0.37s ===============================
```

**After (fix applied)**

```
$ git status --short rag tests
 M rag/retriever/keyword_search.py
 M tests/unit/test_keyword_search.py

$ PYTHONPATH=. .venv-repro/bin/python repro.py
keyword_index_built  chunk_count=0
no error raised
searcher.bm25 is None: True
searcher.chunks == []: True

$ PYTHONPATH=. .venv-repro/bin/python stale.py
keyword_index_built  chunk_count=1
keyword_search_complete  query_len=1 results_count=1
after non-empty index: 1 result(s)
keyword_index_built  chunk_count=0
keyword_search_empty_index
after index([]): []

$ .venv-repro/bin/pytest tests/unit/test_keyword_search.py::TestKeywordSearcher::test_empty_index -v
tests/unit/test_keyword_search.py::TestKeywordSearcher::test_empty_index PASSED [100%]
============================== 1 passed in 0.10s ===============================
```

**Full suite (run separately, not captured in the before/after files above):** `376 passed, 52 xfailed` (up from `375 passed, 53 xfailed` before the fix — exactly the one test moving from `xfail` to a genuine pass, no other regressions).

<!-- The log lines above are trimmed for readability (pytest plugin banners, full local
file paths removed). Before uploading, swap in the COMPLETE untrimmed content from
~/Desktop/unit3-evidence-issue-68.md — that file, not this draft, is the actual record. -->

## Reflection

### Run history

1. Built an initial rubric from the assignment's own named failure families (no lecture worksheet to start from): one required check each for diagnosis-matches-evidence, fixes-cause-not-symptom, bounded scope, executability, decisive test plan, honest unknowns, and thread-aware comms. `procedure.md` was rewritten from Unit 2's `repro-check` `SKILL.md` Workflow section, adapted for grading a plan instead of a reproduction. `voice-guide.md` carried over Unit 2's guide verbatim, extended with two rules for the plan-comment register (propose, don't decide; credit a maintainer's direction rather than restate it as your own).
2. Smoke test (`--limit 3`): 3/3 agreement on the first try, with zero prior tuning against real packages.
3. First full run: 18/20, already clearing the bar, but two misses (`pkg-05`, `pkg-14`) both failing "Unknowns are stated honestly," both gold `accept` — concentrated enough on one check to be worth investigating despite already passing.
4. Debugged both: `pkg-14` was also failing "A stranger could start executing it," flagged for not pinning an exact function name — but the plan actually named specific modules plus an already-working, named debug method to locate it precisely. Reworded that check to accept a concrete, already-validated method as a nameable first step, not just a literal file/line.
5. Also loosened "Unknowns are stated honestly" to excuse deferring a literal naming detail to an already-validated method. Canary-checked against `pkg-10`/`pkg-17` (genuinely unbuildable plans) to confirm the loosening didn't forgive real vagueness — it held.
6. Re-test: `pkg-14`'s executability fix held, but "Unknowns" still failed both `pkg-05` and `pkg-14`, with sharper evidence showing both assumptions actually were load-bearing by a strict reading (e.g. `pkg-05`'s approach assumed a cache file's stored timestamp while its own scope explicitly excluded "cache storage format" changes). A second attempt at a "load-bearing vs. peripheral" wording distinction didn't resolve this — the model was right both times that the claims were load-bearing.
7. Recognized the real problem: "Unknowns are stated honestly" was duplicating "Diagnosis matches the repro evidence"'s job. Almost any diagnosis is an inference beyond what's directly proven; demanding every diagnostic inference be independently hedged would fail nearly all real plans, not just bad ones. Redesigned the check to judge implementation/deployment risk of the *chosen approach* specifically, explicitly separating it from whether the diagnosis itself is grounded.
8. Re-tested the same four packages: `pkg-14` came back a fully clean accept. `pkg-05` still failed "Unknowns," but now for a different, better-justified reason: the plan's one risk line doesn't consider other callers of the modified function or fetch-failure behavior — a genuine, specific gap rather than a diagnosis-certainty complaint.
9. Confirming full run: 20/20, every category clean (`clear-accept 7/7`, `scope-creep 4/4`, `thread-convention 2/2`, `unbuildable 3/3`, `wrong-cause 4/4`). Saved as `eval-run.txt`. (`pkg-05` itself flipped to `accept` on this particular run — the same kind of close-call model wobble seen on borderline cases throughout the course; not something I chased further.)

### Package analysis

**`pkg-05`** — gold verdict `accept`. Across different runs my rubric wobbled between `accept` and `reject` on this package, always on "Unknowns are stated honestly," and I consider it the unit's genuinely defensible disagreement rather than a rubric bug. The plan (fixing a notice-cache staleness bug in `conda`) has an excellent diagnosis, a tightly bounded scope, a fully executable approach, and a decisive test plan — but its risk section names exactly one risk ("one extra network request per channel per 24h") and nothing else, for a change that modifies a shared cache-lookup function (`get_notice_response_from_cache()`). It doesn't consider whether other code paths call that function, or what should happen if the forced refetch itself fails (a network error during the backdated-timestamp re-check, say). By the letter of my redesigned check — which asks whether the plan considered genuine implementation/deployment risk of its chosen approach, not whether its diagnosis is certain — this is a fair, if narrow, miss: a real plan quality gap, not a misreading of the text. I didn't loosen the check further to forgive this, because the same leniency would also have forgiven `pkg-10`'s complete absence of any risk section at all (though that package fails on five other checks regardless, independent of this one).

### Check rationale

The check as it currently reads in my rubric:

> **Unknowns are stated honestly** | The plan's risks/unknowns section, read against the approach and scope actually chosen — not against the diagnosis's own grounding, which "Diagnosis matches the repro evidence" already judges separately | The plan names genuine implementation or deployment risks the chosen approach introduces: who else might be affected by the touched code path, whether the fix could regress other behavior, environment or version considerations the approach depends on. This check does not require hedging the diagnosis's own certainty — a diagnosis stated as a reasoned inference from evidence, even without code-level proof, is judged by "Diagnosis matches the repro evidence," not here. Fails when the risk section is missing entirely, or clearly inadequate for the chosen scope (e.g. a change touching shared code with zero consideration of other callers). | required

This check went through the most rework of any in my rubric. My first version asked whether the plan hedged anything it "didn't actually know," which sounds reasonable but is actually too broad: a diagnosis is, by definition, an inference from evidence to a believed mechanism, so a strict reading of that wording would fail almost any plan for not independently proving its own diagnosis down to the code level — including two packages (`pkg-05`, `pkg-14`) that gold scores as good plans. I tried narrowing the wording twice (first toward "load-bearing" assumptions, then toward distinguishing peripheral detail from core claims) and both attempts still failed the same two packages for the same underlying reason, because the problem wasn't the threshold, it was that this check was doing the same job as "Diagnosis matches the repro evidence." The fix was conceptual, not just wording: scope this check to the approach's implementation/deployment risk specifically, and let the separate diagnosis check own the question of whether the stated cause is grounded. That's why the current wording explicitly says what this check does *not* require.

### Trade-offs

Separating "is the diagnosis grounded" from "did the plan consider deployment risk" means a plan can state a fairly confident, unproven diagnosis and still pass this check, as long as its risk section is otherwise adequate — which trades away catching an overconfident diagnosis through this check specifically (that job now rests entirely on "Diagnosis matches the repro evidence" doing its job well). I accepted this because the alternative made the check fail almost every real plan, including clearly good ones, which is a worse failure mode than occasionally missing an overconfident diagnosis that a different check should have caught anyway. Separately, the "A stranger could start executing it" check's carve-out for "a named, already-validated method" (e.g. a working debug trace) trades some rigor for recognizing that real engineering plans legitimately defer precise naming to a concrete next step; the risk is a vaguer plan could dress up "I'll investigate" as a "validated method" without actually having done any investigation yet. I judged this acceptable because the check still requires a specific location to be named, not just a method — a plan with neither still fails cleanly, as `pkg-10`/`pkg-17` continued to show throughout every round of tuning.
