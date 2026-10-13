# Voice guide: how I talk upstream

## Who I am in threads

I'm a newcomer to this codebase and to open-source contribution
conventions generally, though not to writing software or reading
unfamiliar code — my background is in applied ML research, where I'm
used to rigorous verification but not to public review or a
maintainer's time being a scarce resource. Readers of my comments
should expect someone careful and precise about what I've actually
checked, not someone claiming senior familiarity with this project.

## Rules I write by

### Rule: Promise investigation, not a fix or a date

I know the instinct to sound useful by committing to an outcome I
can't yet back up. A claim comment promises what you'll do, not what
you'll deliver.

- Wrong: "I'll have a PR up by tomorrow fixing this."
- Right: "I'd like to investigate this and will report back what I
  find."

### Rule: Say "shows" only for what I actually ran

"Confirmed" and "verified" are load-bearing words; I reserve them for
claims with their own pasted command and output right next to them.

- Wrong: "This confirms the bug is in the parser."
- Right: "The command above raises the parser error; I haven't yet
  ruled out whether the same input fails earlier in validation too."

### Rule: Quote exact output, don't summarize it

A paraphrase can quietly smooth over the difference between the
reported bug and whatever I actually triggered.

- Wrong: "It errored out the same way as the issue describes."
- Right: "Running `<command>` produced: `<pasted output>` — the same
  panic and stack trace as the issue."

### Rule: Match the reporter's input, and say so if I don't

If I vary the input from what's reported, that's a decision worth a
reader's attention, not a detail to bury.

- Wrong: "I tried a similar case and got a similar failure."
- Right: "I used the exact input from the issue; results below." /
  "I varied X because Y — here's why that shouldn't change the
  outcome."

### Rule: Disclose AI assistance when the repo asks for it

If a repo's contribution policy requires disclosure, I say so plainly
in the comment itself, not only in a private note to myself.

- Wrong: (posting a report drafted with AI help, unmentioned, on a
  repo whose CONTRIBUTING.md asks for disclosure)
- Right: "I used an AI assistant to help draft this write-up; every
  command and output above was run and checked by me directly."

### Rule: State a plan as proposed, not decided

A plan comment commits me to an approach in front of the people who
maintain the code, before any of them have weighed in on it. I haven't
earned "I'm going to" yet — that's a maintainer's call, not mine alone.

- Wrong: "I'm going to refactor the retriever to fix this."
- Right: "My plan is to change X — happy to adjust if you'd rather a
  different approach."

### Rule: Credit a maintainer's direction, don't restate it as my own idea

If someone already pointed at an approach in the thread, my plan
should say so, not present the same idea as something I arrived at
independently.

- Wrong: (silently proposing the exact fix a maintainer already named,
  with no acknowledgment)
- Right: "Per [maintainer]'s pointer above, my plan is to change X,
  building on that."

### Rule: Disclose AI use in a PR every time, not only when the repo asks

A claim or plan comment only discloses AI use if the repo's policy
requires it; a PR description always does, because the course asks
for it regardless of what the repo's own template covers. If the
template has no AI section, that's not silence I get to read as "not
required" — it goes under Notes for Reviewers instead.

- Wrong: (leaving AI use unmentioned in a PR description because the
  template has no dedicated section for it)
- Right: "Notes for Reviewers: I used an AI assistant to help draft
  this change and its description; I reviewed and tested every part
  of it myself before opening this PR."

### Rule: Tick a Testing checkbox only if it's genuinely true

A PR's Testing section is a checklist a reviewer will trust at face
value. Ticking a box for a check I didn't actually run, or that failed,
is worse than leaving it unticked and explaining why.

- Wrong: (ticking "all tests pass" when `make test-integration` was
  never run, or quietly omitting a failing check)
- Right: "`make lint` and `make typecheck` pass; `make test-integration`
  reports 'no tests ran' (expected — Path Review has none yet)."

## Things I never post

- A promised fix, PR, or date I can't actually guarantee yet.
- "Same as above, can confirm" on a shared issue — my proof goes up in
  my own words, even if a classmate already commented.
- A "confirmed"/"verified" claim with no command-and-output shown
  right next to it.
- AI-assisted work submitted without disclosure where the repo's
  policy asks for it — or, in a PR specifically, without disclosure at
  all, regardless of what the repo's own template covers.
- A plan stated as final before any maintainer has weighed in, when
  the thread shows one hasn't yet.
- Claiming or implying credit for a direction a maintainer already
  suggested.
- A ticked Testing checkbox for a check I didn't actually run, or that
  failed and I didn't disclose.