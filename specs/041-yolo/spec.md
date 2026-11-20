# Feature Specification: YOLO mode

**Feature Branch**: `feat/041-yolo`

**Created**: 2026-11-20

**Status**: Draft

**Input**: The owner asked (2026-11-20) for a YOLO mode. It is off by default. When the owner
turns it on, the agent can do all work without a confirmation from the owner. The owner said:
"if YOLO is on, the agent does everything without a confirmation of the user".

## Decisions

- Only the owner turns YOLO on, with one Touch ID. The Secure Enclave key opens only after
  `.userPresence`, so the first Touch ID cannot go away. After it, no command asks again.
- YOLO is a lease of all names without a term. A locked screen and a sleep of the Mac do not end
  it. These events end it: `secret yolo off`, `secret lock`, a change of the Touch ID settings,
  a restart, a logout.
- In YOLO, `secret gh` sends the value without the confirmation window.
- YOLO removes confirmations. It does not open values to the chat: the mask, the hook and the
  rules about `secret get` and `cat .env` stay. The agent does not need a value in the chat to
  do its work.
- Steps that need a person to type do not change: `secret ask` (a new value), `secret export`
  and `secret restore` (the backup password).
- YOLO works only in the Secure Enclave store.

## User Scenarios & Testing *(mandatory)*

<!-- utr-100: 8.2 — heading set by the spec-kit template -->
### User Story 1 - The agent works without questions (Priority: P1)

The owner runs `secret yolo on` and confirms Touch ID once. Then the agent reads any secret
with `secret run`, also with a locked screen and after a sleep, and no window opens.

**Independent Test**: use the test build. Run `secret yolo on` with the test Touch ID answer
"allow". Send the screen lock signal. Then `secret run` of any name works, and the Touch ID
log has no new line.

**Acceptance Scenarios**:

<!-- STE deviation: Given/When/Then lines of the spec-kit template. -->
1. **Given** YOLO is on, **When** `secret run` reads any name, **Then** the command gets the
   value and no Touch ID window opens.
2. **Given** YOLO is on, **When** the screen is locked or the Mac sleeps, **Then** YOLO stays on.
3. **Given** YOLO is on, **When** a secret is added or changed with `set`, `gen`, `ask` or `mv`,
   **Then** the agent reads the new value without Touch ID.
4. **Given** YOLO is on, **When** `secret gh NAME -R o/r` runs, **Then** the value goes to
   `gh secret set` without the confirmation window.

### User Story 2 - The owner sees and ends YOLO (Priority: P1)

**Acceptance Scenarios**:

<!-- STE deviation: Given/When/Then lines of the spec-kit template. -->
1. **Given** YOLO is on, **When** the owner runs `secret yolo status` or `secret touchid
   status`, **Then** the output says that YOLO is on and how many secrets it holds.
2. **Given** YOLO is on, **When** `secret yolo off` or `secret lock` runs, **Then** the next
   read asks Touch ID again. `yolo off` needs no Touch ID.
3. **Given** YOLO is on, **When** the log is read, **Then** each read from YOLO is marked
   `yolo`, and the log has the `yolo-on` and `yolo-off` lines.

### User Story 3 - The agent cannot turn YOLO on (Priority: P1)

**Acceptance Scenarios**:

<!-- STE deviation: Given/When/Then lines of the spec-kit template. -->
1. **Given** the Claude Code hook, **When** the agent runs `secret yolo on`, **Then** the hook
   stops the command. `secret yolo off` and `secret yolo status` pass.
2. **Given** `SECRET_NO_DIALOG=1` or a locked screen, **When** `secret yolo on` runs, **Then** it
   fails and opens no window.

### Edge Cases

- `secret yolo on` while YOLO is on: a new Touch ID, the values are read again.
- A refused Touch ID: YOLO stays off.
- The file keychain store: `secret yolo` fails with `yolo needs the secure enclave store`.
- A secret that is removed leaves YOLO memory.

## Requirements *(mandatory)*

<!-- STE deviation: requirement lines of the spec-kit template. -->
- **FR-001**: `secret yolo on|off|status`. The default is off.
- **FR-002**: `on` asks a new Touch ID. Grace does not stand in for it. The window says that
  agents get every secret without Touch ID until `secret yolo off`.
- **FR-003**: in YOLO, the agent of the helper keeps all values in memory and serves each read
  without Touch ID, also with a locked screen.
- **FR-004**: a write of the helper gives a new value to the agent when YOLO is on.
- **FR-005**: in YOLO, `secret gh` does not show the confirmation window.
- **FR-006**: `off`, `lock`, a change of the policy, a restart and a logout end YOLO.
- **FR-007**: the log marks reads from YOLO and has `yolo-on` and `yolo-off`. No values.
- **FR-008**: the hook stops `secret yolo on`. The rules for agents say: do not run it.
- **FR-009**: the mask, the hook rules and `get`/`copy` do not change.

## Success Criteria *(mandatory)*

- **SC-001**: with YOLO on and a locked screen, `secret run` of any name works without a Touch
  ID question.
- **SC-002**: after `secret yolo off`, the next read asks Touch ID.
- **SC-003**: the hook stops `secret yolo on` from the agent.
