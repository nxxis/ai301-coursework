# Evidence guide: where proof lives in a reproduction package

## Environment

**Where it lives.** In an eval bundle: the report's opening
environment block (tool version, OS, install method) and the issue
context's own stated version/OS. In live mode: the same fields in the
student's draft, checked against the live issue's stated version/OS in
the thread or the issue body.

**What good looks like.** The named version and OS either match what
the issue targets, or a mismatch is called out explicitly with a
reason it shouldn't matter (e.g. "issue filed on 4.53.2, I tested
current release 4.53.3 to confirm it's still present"). An environment
block that's simply present is not the same as one that's relevant —
the version has to be the one the bug is actually about, or the
difference has to be addressed head-on, not silently swapped.

## Steps

**Where it lives.** The report's preparation/execution section: the
exact input given (a file, a pasted block, a command's arguments) and
the exact command run. Compare directly against the issue body's own
stated input and command.

**What good looks like.** A stranger could copy the input and command
verbatim and land in the same starting state the issue describes — not
"a similar input," an identical one, character for character where it
matters (quoting, delimiters, syntax choice). Any deliberate variation
from the issue's input is named and justified in the text itself, not
left for a reader to notice on their own.

## Behavior shown

**Where it lives.** The report's execution/output block: whatever
command output, log, stack trace, or error text is actually pasted.
Compare its class of failure (crash/panic vs. handled error vs. wrong
value vs. no error at all) against the issue's stated actual behavior.

**What good looks like.** The pasted output demonstrates the same kind
of failure the issue reports, not merely "an error of some sort." A
graceful, handled error message is not evidence for a reported
unhandled panic, even when both technically represent "the command
didn't work." If the report's own steps produced a different input
than the issue's (see Steps above), the output here is almost always
the tell: a different bug producing a different failure.

## Honesty

**Where it lives.** Every sentence anywhere in the claim comment or
report that asserts something was confirmed, verified, reproduced, or
understood — matched against whichever command/output block (Behavior
shown) actually backs that specific sentence.

**What good looks like.** Every confirmation claim has its own directly
supporting evidence right there in the package; one shown test cannot
retroactively back a second, separate claim about a different run or
version. An honest "I could not reproduce this" backed by an accurate
account of what was tried is a fully sufficient report — it is not a
lesser one than a confirmed repro. What fails this family is a
narrative that says more than its own artifacts show: "this confirms
the bug" written over output that shows something else, or a claim
about a second environment with no output pasted for it at all.

## Comms

**Where it lives.** The repo-facts block's stated contribution policy
and any AI-use disclosure requirement, checked against whether the
claim comment or report actually discloses AI assistance where
required. Also: the claim comment's own content against the issue it
names (does it reference the specific issue, or read as boilerplate
that could paste onto any issue?).

**What good looks like.** If the repo's policy states a disclosure
requirement, the comment says so plainly, without waiting to be asked.
If the policy is silent, no disclosure is needed and none should be
manufactured. Separately, a claim comment names the specific issue and
what the commenter intends to do next (investigate, not "fix by
Friday"); a comment that would read identically pasted onto a
different issue is boilerplate, not communication.