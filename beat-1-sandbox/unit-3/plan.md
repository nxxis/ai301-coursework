# Plan — issue #68 (manifest H-01): `KeywordSearcher.index([])` raises `ZeroDivisionError`

## Diagnosis

`KeywordSearcher.index()` unconditionally calls `BM25Okapi(tokenized_corpus)` (`rag/retriever/keyword_search.py:25`) even when `tokenized_corpus` is `[]`. My Unit 2 reproduction confirmed the mechanism directly:

```
Traceback (most recent call last):
  File "<string>", line 5, in <module>
  File "rag/retriever/keyword_search.py", line 25, in index
    self.bm25 = BM25Okapi(tokenized_corpus)
  File ".venv-repro/lib/python3.11/site-packages/rank_bm25.py", line 27, in __init__
    nd = self._initialize(corpus)
  File ".venv-repro/lib/python3.11/site-packages/rank_bm25.py", line 52, in _initialize
    self.avgdl = num_doc / self.corpus_size
ZeroDivisionError: division by zero
```

(full repro comment: https://github.com/codepath/pathreview-ai301-fa26-s1/issues/68#issuecomment-5882341492)

`search()` already guards against an empty index (`if not self.bm25 or not self.chunks: return []`), so the intended behavior is for `index()` to reach that same safe state without ever constructing a `BM25Okapi` on an empty corpus.

## Scope

**In scope:** `KeywordSearcher.index()` in `rag/retriever/keyword_search.py` — guard against constructing `BM25Okapi` on an empty corpus, matching the pattern `search()` already uses.

**Not in scope:** `rank_bm25`'s own `_initialize` (a third-party dependency, not this repo's code); any change to `search()` itself (already correct); any broader refactor of `KeywordSearcher`'s indexing logic beyond this one guard.

**Files:**
- `rag/retriever/keyword_search.py` (the fix)
- `tests/unit/test_keyword_search.py` (update the existing `test_empty_index`, currently marked `xfail`)

## Approach

In `index()`, check whether `tokenized_corpus` is empty *before* calling `BM25Okapi(tokenized_corpus)`. If empty, skip that construction entirely and explicitly set `self.bm25 = None`, so `search()`'s existing `if not self.bm25 or not self.chunks: return []` guard continues to treat it as empty, exactly as it does today for other empty-index cases. Set `self.chunks` as normal either way, so state stays consistent regardless of corpus size.

First concrete action: open `rag/retriever/keyword_search.py`, locate `self.bm25 = BM25Okapi(tokenized_corpus)` inside `index()`, and wrap it in a conditional on whether `tokenized_corpus` is non-empty.

## Test plan

Re-run my Unit 2 repro command directly:

```python
from rag.retriever.keyword_search import KeywordSearcher

searcher = KeywordSearcher()
searcher.index([])
print('no error raised')
```

- **Before (from my repro comment):** raises `ZeroDivisionError: division by zero`.
- **After (expected):** prints `no error raised`; `searcher.bm25 is None`; `searcher.chunks == []`.

Also directly exercise the stale-index case the risk section names: call `index()` with a non-empty corpus, then call `index([])` on the same `KeywordSearcher` instance, then confirm `search(...)` returns `[]` rather than still querying the stale `BM25Okapi` from the first call. This is the one scenario the fresh-object repro above doesn't cover.

Also re-run the repo's own covering test:

```
pytest tests/unit/test_keyword_search.py::TestKeywordSearcher::test_empty_index -v
```

- **Before:** `XFAIL` (test body raises `ZeroDivisionError` before its assertion runs).
- **After (expected):** the `xfail(strict=True)` marker needs to be removed — the test body already asserts `searcher.search(...) == []`; with the exception gone, `strict=True` would otherwise turn the now-passing test into a failure (`XPASS`). No other change to the test is needed beyond removing the marker.

Will also run the full `tests/unit/` suite once the fix is in, to confirm no regression in the non-empty-corpus path.

## Risks and unknowns

- Explicitly setting `self.bm25 = None` in the empty-corpus branch matters beyond just satisfying the empty case on a fresh object: if `index()` is called a second time with an empty corpus *after* a previous non-empty `index()` call, this reset is what clears the stale `BM25Okapi` from the prior call — without it, `search()` would keep querying the old index instead of correctly returning `[]`.
- `self.bm25` is already initialized to `None` in `__init__`, so explicitly setting it to `None` in `index()`'s empty-corpus branch reuses an existing state rather than introducing a new one. I grepped the codebase for other `self.bm25` call sites: it's only referenced in `keyword_search.py` itself; `hybrid.py` only reads the `bm25_score` result, not `self.bm25` directly, so no other code path assumes it's always a `BM25Okapi` instance.
- Open question, not yet investigated: a non-empty corpus containing only empty-string entries (e.g. `[{"text": ""}]`) would pass my guard (the corpus itself isn't empty) but could still produce an average document length of `0` inside `rank_bm25`, for reasons unrelated to this fix. I haven't checked whether that's a real, separate failure mode or a non-issue; worth a quick check during the build, before assuming this fix covers every zero-length-average case.
- The existing `xfail(strict=True)` marker on `test_empty_index` must be **removed** as part of this change (per the issue's own note to remove the marker as part of the fix), not merely adjusted — an unmodified `xfail(strict=True)` would fail (`XPASS`) once the bug no longer reproduces.

## Deviations

The build matched the plan: the only code changes are the empty-corpus guard in `index()` and the removal of the `xfail(strict=True)` marker on `test_empty_index`. Nothing in scope, files, or approach changed. Two small notes:

- I wrote the guard as a one-line conditional expression (`self.bm25 = BM25Okapi(tokenized_corpus) if tokenized_corpus else None`) rather than an `if`/`else` block. The behavior is the one the plan describes; only the shape of the line differs.
- I checked the open question from Risks and unknowns during the build. A corpus of only empty-string entries (`index([{"text": ""}])`) does raise `ZeroDivisionError`, but at a different place, `rank_bm25.py:101` (`self.average_idf = idf_sum / len(self.idf)`), because the corpus has documents but no tokens. It fails the same way on `main` before my change, so this fix neither causes nor covers it. I left it out of this change on purpose: it is a different cause from the one I diagnosed for #68, and fixing it here would widen the scope beyond the one guard the plan commits to. It needs its own report.

Test plan results: the repro went from `ZeroDivisionError` to `no error raised` with `bm25 is None` and `chunks == []`; the stale-index case returns `[]`; `test_empty_index` went from `XFAIL` to `PASSED`; the full `tests/unit/` run went from 375 passed / 53 xfailed to 376 passed / 52 xfailed.
