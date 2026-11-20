# Implementation Plan: YOLO mode

**Branch**: `feat/041-yolo` | **Spec**: `specs/041-yolo/spec.md`

## How YOLO works

YOLO is a lease of all names without a term, in the agent of the store helper. `secret yolo on`
reads all values after one new Touch ID and keeps them in memory. Reads take them from there.
A write tells the agent the new value. `bin/secret` skips the `gh` window when YOLO is on.

## Technical Context

- `helper/store/main.swift`, `Agent`:
  - `yolo: Bool`, `yoloValues: [name: (ref, value)]`.
  - The requests `yolo`, `yolo-off`, `yolo-status` and `yolo-put`.
- `read`: leased names first, then YOLO values, then grace. The source flag is `y`.
- With a locked screen, a name in YOLO memory is not "missing".
- `check()` and `endGrace()`: the agent goes on while YOLO is on.
- `write` (client): after the store file is written, if an agent runs, the helper sends
  `yolo-put NAME VALUE` for each record. When YOLO is off, the agent ignores it. The agent keeps
  the value only when the reference of the item matches the store file.
- `bin/secret`:
  - `cmd_yolo`.
  - `touchid status` gets a `yolo:` line.
  - `cmd_gh` skips `confirm` when `yolo-status` says on.
  - `read_values` writes the `y` names to the log detail as `yolo`.
- `hooks/claude-pretooluse.sh`: a rule for `secret yolo on`.
- Tests: a block `041` in `tests/run.sh`, in the store part, after `026`.

## Constitution Check

Principle II says: "The command gives no way around these steps". YOLO is a way around the
Touch ID question after the first one. This is a change of the constitution: version 3.1.0, a
separate commit. Principle I stays as it is: each new command shows names only.

## Complexity Tracking

| Deviation | Why | Simpler option rejected because |
|---|---|---|
| Principle II: reads without Touch ID with no term | the owner asked for it (2026-11-20) | a lease of 24 hours does not cover the request; the owner wants "everything without a confirmation" |
| `secret gh` without the window | the same request | the window is a confirmation, and YOLO removes confirmations |

## Security notes

- A spoofed agent on the socket can answer "yolo on" to `bin/secret`. Then `gh` skips the
  window. But the fake agent has no values, because only Touch ID opens the Secure Enclave key.
  So the values that go to `gh` are wrong values.
- `yolo-put` from any process of the user puts a value into memory only for a name whose
  reference matches the store file. Such a process can already write the store, because a
  write needs no Touch ID. So YOLO adds no new attack.
