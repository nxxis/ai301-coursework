My pull request to wheelgap has been merged! I fixed how wheelgap, an open-source Python tool, handles older lockfiles, so it uses the package information already saved in a project instead of looking it up online again.

wheelgap checks whether the packages a Python project depends on are ready to install on each platform it targets, such as Windows, macOS or Linux. A lockfile is the file that records a project's exact dependencies, and older ones kept that information in a different place, which the tool ignored.

The most useful review comment was about a test I thought was doing its job. I checked that one step made no internet requests, but that step never makes those requests in the first place. The check would have passed even with the bug still there.

I rewrote it to run the step that actually goes online and check that only the one package missing from the lockfile was looked up. Now it fails without the fix.

The maintainer reviewed the change twice, with help from Claude. I worked through the feedback and two merge conflicts with other contributors' changes, and the final test run finished with 267 passed and 1 skipped.

I'll be taking that testing habit into my next contribution: before trusting a test, I run it against the broken code to make sure it can fail.

https://github.com/wheelgap/wheelgap/pull/67

#OpenSource #Python #SoftwareEngineering
