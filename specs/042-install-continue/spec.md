# Feature Specification: install.sh goes on after a failed auth helper update

**Feature Branch**: `fix/042-install-continue`

**Created**: 2026-11-21

**Status**: Implemented 2026-11-21, tests/run.sh 598 pass

**Input**: On 2026-11-21 the owner ran `install.sh --app` for 1.10.0. The new build of the Touch ID
helper for the file keychain needs the password of the keychain `secrets` (`secret __rehelper`).
The password did not match three times. `install.sh` stopped at this step.

The store helper and SecretBar stayed at the old version, and the new `secret yolo` did not work. The store of the
owner is the Secure Enclave store, which does not use the auth helper. The owner said: "fix
install.sh".

## Decisions

- A failed `__rehelper` keeps the old build of the auth helper, as before. The old build still
  reads its items, so nothing breaks.
- After this failure, `install.sh` does all the next steps: completion, the store helper, the
  cookies helper, the bridge, SecretBar and the Safari extension.
- At the end, `install.sh` prints a warning line and exits with code 1. Thus the failure stays
  visible to the user and to scripts.

<!-- utr-100: 8.2 — heading set by the spec-kit template -->
## User Scenarios & Testing *(mandatory)*

### User Story 1 - An update with a wrong keychain password (Priority: P1)

The user updates from a clone. The keychain password dialog gets a wrong password or Cancel.

**Acceptance Scenarios**:

<!-- STE deviation: spec-kit Given/When/Then scenarios keep the condition in the middle. -->

1. **Given** `__rehelper` fails, **When** `install.sh` runs, **Then** the old auth helper stays,
   `build/secret-auth.new` is removed, and the store helper build runs.
2. **Given** the same failure, **When** `install.sh` ends, **Then** the last line tells that the
   auth helper kept the old build, and the exit code is 1.
3. **Given** `__rehelper` succeeds, **When** `install.sh` runs, **Then** the result is as before
   (exit code 0).

## Requirements *(mandatory)*

- **FR-001**: A failed `__rehelper` does not stop `install.sh`.
- **FR-002**: The old auth helper and its checksum file stay. The file `secret-auth.new` is removed.
- **FR-003**: `install.sh` exits with code 1 and prints one warning line at the end after FR-001.
- **FR-004**: `tests/run.sh` checks FR-001 to FR-003 with stub helpers in a temporary root. The test
  does not touch `/Applications` or the real store.

## Success Criteria *(mandatory)*

- **SC-001**: With a wrong keychain password, an update still installs the store helper and the app.
