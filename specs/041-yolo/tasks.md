# Tasks: YOLO mode

**Input**: `specs/041-yolo/` (spec.md, plan.md)

## Phase 1: Constitution

<!-- utr-100: 4.2 — spec-kit task lines: file, action and conditions in one item -->
- [x] T001 `.specify/memory/constitution.md`: principle II — YOLO mode (owner only, one new Touch ID, no term, ended by `yolo off`, `lock`, a policy change, a restart, a logout; `gh` without the window). Version 3.1.0, a separate commit

<!-- STE deviation: spec-kit task lines keep the file, the action and the conditions in one item. -->

## Phase 2: Helper agent

- [x] T002 `helper/store/main.swift`: `Agent.yolo`, `yoloValues`; request `yolo` (new Touch ID, screen not locked, no `SECRET_NO_DIALOG`, read all values)
- [x] T003 `helper/store/main.swift`: `read` serves YOLO values with source `y`; with a locked screen a YOLO name is not missing; `verifyYolo()` drops changed and removed names
- [x] T004 `helper/store/main.swift`: `yolo-off`, `yolo-status`, `yolo-put`; `check()`, `endGrace()`, `test-lock`, `test-sleep` keep the agent while YOLO is on
- [x] T005 `helper/store/main.swift`: client commands `yolo REASON`, `yolo-off`, `yolo-status` (no agent start for off and status); `write` sends `yolo-put` to a running agent

## Phase 3: Command

- [x] T006 `bin/secret`: `cmd_yolo on|off|status`, help text, audit `yolo-on`/`yolo-off`
- [x] T007 `bin/secret`: `touchid status` prints `yolo: on (N secrets)` or `yolo: off`
- [x] T008 `bin/secret`: `read_values` and `lease_detail` mark YOLO reads `yolo` in the log
- [x] T009 `bin/secret`: `cmd_gh` skips the window when YOLO is on and says so on stderr

## Phase 4: Agent guard and docs

- [x] T010 `hooks/claude-pretooluse.sh`: stop `secret yolo on`
- [x] T011 `docs/claude-md-secrets.md`: rule "Do NOT run `secret yolo on`"; what YOLO means for the agent
- [x] T012 `README.md`, `README.ru.md`: a short YOLO section

## Phase 6: SecretBar switch (User Story 4)

- [x] T014 `helper/store/main.swift`: the `status` client line gets ` yolo=N` when YOLO is on
- [x] T015 `app/SecretBar/Model.swift`: `Status.yolo`, parse `yolo=N`, self-test lines; `Store.setYolo(_:)` runs `secret yolo on` or `off`
- [x] T016 `app/SecretBar/Views.swift`: `YoloRow` with a switch under the search field; red when on, with the count
- [x] T017 `app/SecretBar/Localizable.xcstrings`: the new strings in en, ru, de, fr, es, it, pt-PT and pl
- [ ] T018 `app/build.sh`: the app builds (done 2026-11-20, `--selftest` and `--snapshot` with YOLO on and off); a manual check of the switch with the real store is open

## Phase 5: Tests

- [x] T013 `tests/run.sh`: block 041 — on/off/status, reads with a locked screen and after a sleep without Touch ID, `set` while on, `rm` while on, `lock` ends it, `gh` without the window, refused Touch ID, `SECRET_NO_DIALOG`, file keychain store refused, the log, the hook
