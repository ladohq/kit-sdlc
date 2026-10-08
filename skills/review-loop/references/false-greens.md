# False greens

Checks that exit zero without checking anything. Each shape here was met in a real
project; the project's own are in **Review traps** (`sdlc-settings`). The cure is the
same for all: see the check fail on a deliberate error before you trust its green.

## In the pipeline

- **CI that proves nothing.** Read what the merge gate runs before you count its green:
  a pipeline with no tests, or whose steps end in `|| true`, `continue-on-error` or
  `set +e`, gates nothing. Say what CI does not prove, and run that locally.
- **A pipe replacing the exit code.** `cmd | tail` reports `tail`'s status; a failed run
  reads as success, and the truncated output may hide the `N failed` line.
- **A runner that collected nothing.** A path filter or a renamed folder, and the test
  runner exits zero with no tests. Read the count, not the exit code.
- **A linter or type checker given no files**, or reading a stale or shadowed config
  (generated type folders, a build cache): it passes at once. A probe file with a
  deliberate error shows whether it still reports.
- **A build that rewrites tracked files** (generated types, lockfiles, snapshots): not a
  false green, but it leaks into the commit; restore them.
- **A slow suite under load.** Browser suites run beside a build fail on timeouts: a
  false red. Rerun the one failing spec alone before you call it a regression.
- **A background wait that never ends**, or ends on a timeout reported as "no findings".

## In the tests

- An assertion on a name that does not exist, so its negation always holds.
- A negative assertion about a field or parameter the change removed.
- A default value (`get(key, default)`) hiding that the key disappeared.
- A bare mock where every attribute is truthy, so the guard under test always passes.
- A substring that is always present in the output, whatever the code does.
- A contract test over a mocked service that returns exactly what the test expects.
- A new assertion after one that fails earlier, so the probe never reaches it.
- A probe that re-implements the code it checks instead of calling it.
- A test that pins paths or calls while its docstring promises behaviour.
- A constant asserted against itself (`called with TIMEOUT` where `TIMEOUT` is the code's
  own constant), proving neither the value nor that it is wired.
- An assertion on a container while the contract is about one member of it.
- A test green only on a machine in one time zone, locale or order of execution.
