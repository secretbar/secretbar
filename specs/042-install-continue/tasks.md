# Tasks: install.sh goes on after a failed auth helper update

**Input**: `specs/042-install-continue/` (spec.md, plan.md)

- [x] T001 `install.sh`: a failed `__rehelper` removes `secret-auth.new`, keeps the old build and goes on (FR-001, FR-002)
- [x] T002 `install.sh`: a warning line and exit code 1 at the end (FR-003)
- [x] T003 `tests/run.sh`: the install with stub helpers, `__rehelper` fails and succeeds (FR-004)
- [x] T004 `tests/run.sh` passes
