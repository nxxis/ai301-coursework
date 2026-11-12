# Fixing offline checks for older PDM lockfiles in wheelgap

## Overview

I worked on a bug in wheelgap, a Python tool that checks whether a project's dependencies provide wheels for its target platforms. The problem was with older PDM lockfiles: they already contained the package file lists wheelgap needed, but the reader ignored those lists and fell back to looking up packages on PyPI.

I added support for that older format and opened [PR #67](https://github.com/wheelgap/wheelgap/pull/67). It was merged after two rounds of review. The fix itself was fairly small; getting the test right took more work than I expected.

## What happened

### Reproducing the bug

PDM lockfiles written before PDM 2.8 store package files in a shared `[metadata.files]` table. Newer lockfiles put the files on each package entry. wheelgap's `parse_pdm_lock()` handled the newer format but never read the shared table.

To check the bug, I used a small lockfile fixture with three packages. I ran the regression test with my fix stashed, so it exercised the original reader. The first assertion failed:

```text
>       assert pkgs["oldpkg"].files == ["oldpkg-1.0-py3-none-any.whl"]
E       AssertionError: assert None == ['oldpkg-1.0-py3-none-any.whl']
FAILED tests/test_sources.py::test_pdm_lock_legacy_metadata_files
1 failed, 21 deselected in 0.04s
```

The file list was in the lockfile, but the parser left `files` as `None`. That tells a later step to fetch the package's files from PyPI, so the check needed a network request even though the information was already available locally.

### Reading the old file table

`parse_poetry_lock()` already handled a similar legacy table in the same source file, which gave me a starting point. I added a lookup using the package's name and version.

There was one detail my first plan missed. A table key could be `"Jinja2 3.1.2"` while the package entry used `jinja2`. The maintainer had mentioned this in the issue thread, but I had missed the reply. My plan-checking tool caught the omission, and I added a fallback using `canonicalize_name()`:

```python
entries = legacy_files.get(f"{p['name']} {p.get('version')}")
if entries is None:
    target_name = canonicalize_name(p["name"])
    target_version = p.get("version")
    for key, value in legacy_files.items():
        key_name, _, key_version = key.rpartition(" ")
        if key_version == target_version and canonicalize_name(key_name) == target_name:
            entries = value
            break
```

If neither lookup finds an entry, `files` stays `None` and the existing PyPI lookup still runs. The test covers an exact match, a normalized-name match, and a package missing from the table.

### The assertion that proved nothing

The maintainer's reviews were marked as assisted by Claude. One comment pointed out a problem with my attempt to test the offline behavior:

```python
assert fake_pypi == []
```

I had put that assertion after `load_inputs()`. It looked reasonable, but parsing never contacts PyPI. The assertion would pass whether my fix worked or not.

I changed the test to call `fill_all()`, where the actual lookups happen:

```python
fill_all(src.packages)
# Only the package missing from [metadata.files] should need a lookup.
assert fake_pypi and all("/newpkg/" in url for url in fake_pypi)
```

Now the test checks that `newpkg`, which has no entry in the table, is fetched. Without the fix, `oldpkg` and `jinja2` would be fetched too, causing the assertion to fail.

### Working through review

The first review also asked me to merge `main`. Another change had added `_add_once()` to deduplicate packages in `parse_pdm_lock()`, so I had to resolve the conflict while keeping that behavior. Its regression test, `test_pdm_lock_extras_variants_count_once`, still passed afterward. A later changelog conflict meant merging `main` a second time and keeping both entries.

The second review caught two more details. My comment and changelog tied the old table only to lock version 3, even though PDM kept writing it until PDM 2.8. My fixture also used `{url = ...}` entries from newer lockfiles. I corrected the wording and changed `oldpkg` to use the older `{file = ...}` form, while retaining a URL entry so the test covers both shapes.

After those updates, the full suite finished with **267 passed and 1 skipped**, and the pull request was merged.

## Limitations and what's next

The fallback still compares versions as exact strings. A table key containing `1.0` would not match a package recorded as `1.0.0`. PDM normally writes the same string in both places, so this did not block the fix. A follow-up could compare parsed versions with `packaging.version.Version` and add a test for those two spellings.

The review changed how I check regression tests. I had tested the parser correctly and still made a claim about network behavior that the test couldn't support. I now follow the code through the step I want to test and check that the assertion fails without the fix.
