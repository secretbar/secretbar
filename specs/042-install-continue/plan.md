# Implementation Plan: install.sh goes on after a failed auth helper update

**Branch**: `fix/042-install-continue` | **Spec**: `specs/042-install-continue/spec.md`

## Change

- `install.sh`: a variable `warn` is empty at the start.
- When `secret __rehelper` fails, the script removes `secret-auth.new`, sets `warn` and goes to
  the next step. It does not move the new build into place.
- At the end: if `warn` is set, the script prints `install.sh: <warn>` to stderr and exits with
  code 1.

## Test

`tests/run.sh` makes a temporary root:

- a copy of `install.sh`.
- a stub `bin/secret` whose `__rehelper` exits with 1.
- a stub `secret-auth.swift`, an old `build/secret-auth` and a stub `helper/store/build.sh` that
  writes a marker file.
- stubs for `swiftc` and `codesign` first in `PATH`.
- a temporary `HOME` and `ZSH_CUSTOM`.

The stub app builds fail, so the script does not copy an app to `/Applications`. The test checks
the exit code, the marker of the store build, the old auth helper and the warning line. A second
run with a `__rehelper` that succeeds checks the exit code 0.
