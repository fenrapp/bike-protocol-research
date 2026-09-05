# VCU Configuration

All multi-byte fields are little-endian. These records use characteristic `4005`
after authentication. They are protocol findings, not a general-purpose write API.

## Evidence and compatibility

| Record | Type | Compatibility gate | Evidence |
| --- | --- | --- | --- |
| Base riding map | `0` | Recognized VCU PIC version and capability/read/no-op checks | Physical reads, normalized no-op, changed values and later read-back. |
| Charger configuration | `4` | VCU PIC >= 1.9.1 | Verified charging-power and target control; synthetic layout tests. |
| Bike lock | `5` | VCU PIC >= 1.6.29 | Observed layout and implementation tests; instrumented physical write/read-back remains incomplete. |
| Traction control | `8` | VCU PIC >= 1.10.1 | Physical reads and rejected mode-zero write; mode `0x0F` has synthetic encoder/decoder test coverage only. |

The firmware numbers are the current guarded compatibility thresholds, not a
claim that every later firmware or motorcycle variant has been physically tested.
An owner report of traction-control and lock testing exists, but no corresponding
firmware inventory and instrumented successful write/read-back sequence is
available here. It does not promote these two write paths to physically validated.

Serialize requests on this shared channel. Validate operation, record type,
status, map index where applicable, and minimum length before decoding. The
current decoders accept response operation `0x00` or `0x02`; a zero status is
required. A GATT acknowledgement alone is not configuration confirmation.

Read current values before writing, preserve unrelated fields and verify an
unchanged no-op first. Require fresh telemetry or a fresh configuration read
following a change. A timed-out transaction must invalidate the response stream
until reconnection; a late reply must not be matched to a newer request.

## `4005` Base Riding-Map Configuration

Status: `Validated` for captured base-map reads and writes.

Physical captures confirmed reads, normalized no-op writes, changed torque and
regeneration writes, immediate responses, and subsequent reads retaining the
changed values.

Read request, 3 bytes:

| Offset | Field | Type | Notes |
| --- | --- | --- | --- |
| 0 | operation | `UInt8` | `0x00` means read. |
| 1 | configuration type | `UInt8` | `0x00` means base riding map. |
| 2 | map index | `UInt8` | Zero-based, `0...4`. |

Write request, 9 bytes:

| Offset | Field | Type | Notes |
| --- | --- | --- | --- |
| 0 | operation | `UInt8` | `0x01` means write. |
| 1 | configuration type | `UInt8` | `0x00` means base riding map. |
| 2 | map index | `UInt8` | Zero-based, `0...4`. |
| 3 | save | `UInt8` | `0x01` persists the configuration. |
| 4 | torque raw | `Int16` | Horsepower multiplied by `1.25` and rounded. |
| 6 | regenerative braking | `Int16` | Signed percentage. |
| 8 | curve | `UInt8` | Normalize base-map writes to `mapIndex + 1`. |

The complete packet is therefore:

```text
01 00 <map> 01 <torque Int16 LE> <regen Int16 LE> <curve>
```

Observed read response envelope:

```text
02 00 <status> <map> <torque Int16 LE> <regen Int16 LE> <curve> [...]
```

Physical read captures show `curve = 0` for the five base maps. Validated writes
normalize the selector to `mapIndex + 1`. A compatible guarded client accepts only
`0` or `mapIndex + 1` in read responses and always normalizes the outgoing selector
to `mapIndex + 1`.

A guarded client preserves the sibling base value when either horsepower or regeneration changes,
and writes all three configuration values together. Before accepting a change it reads
the selected map, sends a base-value no-op with the normalized selector, and requires a
fresh response confirming the exact torque and regeneration values. A real change is
accepted only after a second read confirms the requested horsepower and regeneration;
the returned selector may remain `0` or report `mapIndex + 1`. Arbitrary curve writes
remain outside this development scope.

## `4005` Traction-Control Configuration

Status: `Observed`

Type `8` reads have been observed against a physical VCU. The candidate write
layout has synthetic encoder/decoder test coverage only.
Firmware gating, an exact no-op, serialized transport, sibling-value preservation
and fresh-read confirmation are required for controlled validation. A
physical type `8` write/read-back sequence has not yet been captured.

Read request, 3 bytes:

```text
00 08 <map>
```

Write request, 9 bytes:

| Offset | Field | Type | Notes |
| --- | --- | --- | --- |
| 0 | operation | `UInt8` | `0x01` means write. |
| 1 | configuration type | `UInt8` | `0x08` means traction control. |
| 2 | save | `UInt8` | `0x01` persists the configuration. |
| 3 | map index | `UInt8` | Zero-based, `0...4`. |
| 4 | mode | `UInt8` | Candidate value `0x0F`; physical write validation pending. |
| 5 | power traction control | `Int16` | Signed percentage multiplied by 10. |
| 7 | braking traction control | `Int16` | Signed percentage multiplied by 10. |

The complete packet is:

```text
01 08 01 <map> 0F <TC Int16 LE> <TC regen Int16 LE>
```

Observed response envelope:

```text
02 08 <status> <map> <TC Int16 LE> <TC regen Int16 LE> [...]
```

The VCU also returns a short status after a type `8` write:

```text
01 08 <status>
```

The guarded compatibility profile accepts whole percentages from `0%` through
`100%` and multiplies each value by ten before encoding it in the signed 16-bit
fields. Changing either value resends the complete type `8` record and preserves
the other value exactly. This profile requires VCU PIC firmware 1.10.1 or newer,
a zero status in the short write response, and an exact fresh read. A physical capture confirmed that the earlier `mode = 0x00` candidate was
rejected with status `0x07` and did not persist. The candidate `mode = 0x0F` packet still
requires physical write/read-back validation.

## `4005` Charger Configuration

Status: `Validated` for the supported power and charge-target controls. Field
layouts have clean-room encoder/decoder tests; this does not validate editing
other charger parameters or every charger model.

Read request: `00 04`.

Write request, 13 bytes:

| Offset | Field | Type | Unit or value |
| --- | --- | --- | --- |
| 0 | operation | `UInt8` | `0x01` |
| 1 | configuration type | `UInt8` | `0x04` |
| 2 | save | `UInt8` | `0x01` |
| 3 | charge current limit | `UInt16` | A x 10 |
| 5 | charge power | `UInt16` | W |
| 7 | maximum state of charge | `UInt16` | percent x 10 |
| 9 | standard charger maximum power | `UInt16` | W |
| 11 | backpack charger maximum power | `UInt16` | W |

Response, minimum 19 bytes:

| Offset | Field | Type | Unit or value |
| --- | --- | --- | --- |
| 0 | operation | `UInt8` | `0x00` or `0x02` accepted by the decoder |
| 1 | configuration type | `UInt8` | `0x04` |
| 2 | status | `UInt8` | `0x00` required |
| 3 | charge current limit | `UInt16` | A x 10 |
| 5 | charge power | `UInt16` | W |
| 7 | maximum state of charge | `UInt16` | percent x 10 |
| 9 | minimum current | `UInt16` | A x 10 |
| 11 | start time | `UInt16` | Raw |
| 13 | ramp time | `UInt16` | Raw |
| 15 | standard charger maximum power | `UInt16` | W |
| 17 | backpack charger maximum power | `UInt16` | W |

The response is not a packet that can be replayed as a write. Minimum current,
start time and ramp time have no slots in the 13-byte write request.

Configuration SOC uses tenths of a percent, while `5001` telemetry's maximum SOC
is a direct percentage. Confirm a changed power/target using fresh `5001`
telemetry after the write. Require an authenticated session, compatible firmware,
a connected charger and successful no-op preparation.

Charger telemetry type values used by the current profile are `0` standard,
`2` fast and `3` backpack. Unknown types must not acquire fast-charger capability.
Current guarded limits are 300 W minimum, 100 W steps, 3300 W standard/backpack
and 7000 W fast. These are compatibility-profile limits, not the range of the
wire's unsigned integer fields.

Preserving values needs one charger-specific qualification: an observed current
limit of raw `20` (2 A) can prevent a requested power increase. The implemented
recovery replaces that exact value with the profile limit (raw `200` for
standard/backpack, `250` for fast). Other current limits are preserved. For
standard/backpack, both maximum-power fields are synchronized to
`min(max(previousStandardMaximum, requestedPower), profileMaximum)`; fast-charger
changes preserve both maxima. A target-only change preserves all the writable
current and power fields. These explicit adjustments must not be generalized to
arbitrary charger configuration.

## `4005` Bike Lock Configuration

Status: `Observed` layout with synthetic encoder/decoder tests. An owner test
report is not a substitute for an instrumented physical confirmation sequence.

Read request: `00 05`.

Write request, 7 bytes:

| Offset | Field | Type | Unit or value |
| --- | --- | --- | --- |
| 0 | operation | `UInt8` | `0x01` |
| 1 | configuration type | `UInt8` | `0x05` |
| 2 | action | `UInt8` | `0x83` |
| 3 | locked | `UInt8` | `0` unlocked, `1` locked |
| 4 | lock type | `UInt8` | Default encoder value `1`; preserve the value read |
| 5 | timeout | `Int16` | Seconds; default `0`, preserve the value read |

Response, minimum 7 bytes:

```text
<operation> 05 <status> <locked> <lockType> <timeout Int16 LE>
```

The decoder accepts operation `0x00` or `0x02`, requires status zero and rejects
lock-state values other than zero or one. No-op validation preserves lock state,
lock type and timeout exactly. A fresh matching configuration read is required
after a change; the lock field in `1002` alone does not validate this command.
Lock state confirmation must not be interpreted as evidence for ownership,
firmware, safety-control or other security-related writes.
