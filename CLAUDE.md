# dot-dash-code — compiled firmware binaries

**This repo is PUBLIC.** Anything committed here is readable by anyone, and
`strings` on a `.bin` reveals whatever the firmware hardcodes. Never add
credentials, keys or notes containing them.

This repo holds no source. It holds the binaries devices download, plus the one
file that triggers an update.

## The two release levers — do not touch unless asked in that message

- `version.txt` here, and `FIRMWARE_VERSION` in each firmware `Globals.h`.
- `Ota.ino` fetches `version.txt` and updates when `latestVersion >
  FIRMWARE_VERSION`. Bumping either one ships to every device in the field.
- Both are at **3** today. Sam decides the moment updates go out.

## Slots

| Line | Test slot — safe, inert | Public slot — OTA, never rename |
|---|---|---|
| C3 | `dot-dash-code-t.bin` | `dot-dash-code.bin` |
| C6 (small partition) | `dot-dash-code-c6-t.bin` | `dot-dash-code-c6.bin` |
| C62 (large partition) | `dot-dash-code-c62-t.bin` | `dot-dash-code-c62.bin` |

Test bins are referenced by no firmware — they are loaded by pasting their URL
into the device's Wi-Fi portal. Overwriting them is inert, which is why it needs
no permission.

Three lines, not two: the C6 fleet is split across two partition layouts and OTA
cannot repartition, so one binary cannot serve both.

## How bins get here

`"~/Documents/Arduino/Dot Dash/build-test-bins.sh"` builds all three lines,
reports headroom, copies into the test slots, and asserts the two release levers
are unchanged before it exits. It does not commit.

**Stale-binary trap:** `arduino-cli` builds in an internal temp dir and does not
populate `sketch/build/`. Always confirm the bins show as modified in git after a
real source change — "nothing to publish" right after editing source means the
build did not take. Copy the plain `.ino.bin`, never `.merged.bin`.

The `-m` bins (`dot-dash-code-m.bin`, `dot-dash-code-c62-m.bin`) are stale
leftovers from the MQTT-key branch, which is merged. They are not current.
