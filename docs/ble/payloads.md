# Payload Layouts

All multi-byte values below are little-endian unless stated otherwise.

## Confidence Labels

| Label | Meaning |
| --- | --- |
| Validated | Confirmed by captures, implementation, and tests. |
| Partly validated | Some fields are known; others are preserved as raw counters. |
| Observed | Documented protocol behavior or candidate layout that is not fully validated. |
| Unknown | Discovery target only. |

## `1002` Bike Status

Status: `Validated`

Minimum length: 18 bytes. Payloads with extra trailing bytes have been observed and should be tolerated.

| Offset | Field | Type | Notes |
| --- | --- | --- | --- |
| 0 | `miscBits` | `UInt16` | Walk/crawl bits live here. |
| 2 | `indicatorBits` | `UInt16` | High beam, blinkers, check-engine. |
| 4 | `alertBits` | `UInt16` | Non-zero means a fault/alert is active. |
| 6 | `faultBits` | `UInt16` | Raw fault bits. |
| 8 | `infoBits` | `UInt16` | Charging, connected charger, gear, on, pump, fan. |
| 10 | `lockStatus` | `UInt8` | Observed only; lock behavior is not validated. |
| 11 | `lockTime` | `UInt16` | Observed only. |
| 13 | `updateAvailable` | `UInt8` | `1` means update available. |
| 14 | `batteryStatus` | `UInt32` | Raw battery status. |

Validated masks:

| Field | Mask | Meaning |
| --- | --- | --- |
| `infoBits` | `0x0001` | Charging |
| `infoBits` | `0x0002` | Charger connected |
| `infoBits` | `0x0008` | In gear |
| `infoBits` | `0x0010` | On |
| `infoBits` | `0x0020` | Pump on |
| `infoBits` | `0x0040` | Fan on |
| `indicatorBits` | `0x0002` | High beam |
| `indicatorBits` | `0x0004` | Right blinker |
| `indicatorBits` | `0x0008` | Left blinker |
| `indicatorBits` | `0x1000` | Check-engine light |
| `miscBits & 0x000F` | `0x08` | Crawl active |
| `miscBits & 0x000F` | `0x04` | Crawl forward |

## `2001` Live Speed

Status: `Validated`

Required length: 4 bytes.

| Offset | Field | Type | Unit |
| --- | --- | --- | --- |
| 0 | `speedKmhX10` | `Int16` | km/h x 10 |
| 2 | `motorRPM` | `Int16` | RPM |

`speedKmh = speedKmhX10 / 10`.

## `2002` Live Throttle

Status: `Observed`

Required length: 6 bytes.

| Offset | Field | Type | Notes |
| --- | --- | --- | --- |
| 0 | `idFeedbackRaw` | `Int16` | Raw feedback. |
| 2 | `iqFeedbackRaw` | `Int16` | Raw feedback. |
| 4 | `positionRaw` | `Int16` | Raw throttle position candidate. |

## `2003` Live IMU

Status: `Observed`

Required length: 12 bytes.

| Offset | Field | Type |
| --- | --- | --- |
| 0 | `accelerationXRaw` | `Int16` |
| 2 | `accelerationYRaw` | `Int16` |
| 4 | `accelerationZRaw` | `Int16` |
| 6 | `gyroscopeXRaw` | `Int16` |
| 8 | `gyroscopeYRaw` | `Int16` |
| 10 | `gyroscopeZRaw` | `Int16` |

Scales and physical orientation are not validated.

## `2004` Live Map

Status: `Validated`

Required length: 1 byte.

| Offset | Field | Type | Notes |
| --- | --- | --- | --- |
| 0 | `modeIndex` | `UInt8` | Active map index. Labels are user/configuration dependent. |

## `2005` Live Totals

Status: `Partly validated`

Required length: 16 bytes.

| Offset | Field | Type | Notes |
| --- | --- | --- | --- |
| 0 | `firstRawCounter` | `UInt32` | Validated as odometer in centi-kilometers. |
| 4 | `secondRawCounter` | `UInt32` | Unknown. |
| 8 | `thirdRawCounter` | `UInt32` | Unknown. |
| 12 | `fourthRawCounter` | `UInt32` | Unknown. |

`odometerKm = firstRawCounter / 100`.

## `4100` VCU Telemetry TLV

Status: `Partly validated`

Brake activity is decoded only for frames starting with:

```text
05 0F
```

Minimum decoded length: 8 bytes.

| Offset | Field | Type | Notes |
| --- | --- | --- | --- |
| 0 | header | 2 bytes | Must be `05 0F`. |
| 2 | `primaryBrakeSignal` | `UInt16` | Non-zero means brake active. |
| 6 | `secondaryBrakeSignal` | `UInt16` | Non-zero means brake active. |

Do not infer front/rear brake separation from these two signals.

## `5001` Charger Data

Status: `Validated`

Required length: 19 bytes.

| Offset | Field | Type | Unit |
| --- | --- | --- | --- |
| 0 | `requestedCurrent` | `UInt16` | A x 10 |
| 2 | `reportedCurrent` | `UInt16` | A x 10 |
| 4 | `targetCellVoltage` | `UInt16` | V x 10000 |
| 6 | `maximumCurrent` | `UInt16` | A x 10 |
| 8 | `maximumPower` | `UInt16` | W |
| 10 | `maximumStateOfCharge` | `UInt16` | percent |
| 12 | `requestedVoltageRaw` | `UInt16` | Raw |
| 14 | `reportedVoltageRaw` | `UInt16` | Raw |
| 16 | `statusRaw` | `UInt8` | Raw |
| 17 | `enabled` | `UInt8` | Non-zero is enabled. |
| 18 | `typeRaw` | `UInt8` | Raw charger type. |

This is charger current. It is not traction current.

## `6004` Battery SOC/SOH/DC Bus

Status: `Validated`

Minimum length: 2 bytes. Optional fields appear when payload length reaches 4 or 6 bytes.

| Offset | Field | Type | Unit |
| --- | --- | --- | --- |
| 0 | `stateOfCharge` | `UInt16` | percent, direct |
| 2 | `stateOfHealth` | `UInt16` | percent; `0` means unknown in observed data |
| 4 | `dcBusRaw` | `UInt16` | V x 10 |

`dcBusVolts = dcBusRaw / 10`.

## `6005` Battery Temperatures

Status: `Validated`

Required length: 27 bytes.

| Offset | Field | Type | Unit |
| --- | --- | --- | --- |
| 0 | 12 temperature values | `UInt16[12]` | C x 10 |
| 24 | `validSensorMask` | `UInt16` | Raw mask |
| 26 | `usedSensorCount` | `UInt8` | Count |

`temperatureC = raw / 10`.

## `6007` Cell Voltages

Status: `Validated`

Required length: 200 bytes.

| Offset | Field | Type | Unit |
| --- | --- | --- | --- |
| 0 | 100 cell voltages | `UInt16[100]` | V x 10000 |

`cellVoltage = raw / 10000`.

The pack is represented as 100 cell groups. Public STF notes describe indexes `0...49` and `50...99` as the two pack halves.

## `6008` Cell Balancing

Status: `Validated`

Required length: 13 bytes.

This is a bitmap with one little-endian bit per cell group. Only the first 100 bits map to the 100 cell groups.

## `6009` Battery Signals

Status: `Observed`

Contains BMS signal candidates, including an observed signed value that may relate to current. Units and semantics are not validated. Keep as raw capture data until controlled tests prove the scale.

## `7003` Inverter Temperatures

Status: `Validated`

Required length: 16 bytes.

| Offset | Field | Type | Unit |
| --- | --- | --- | --- |
| 0 | 8 temperature values | `UInt16[8]` | C x 10 |

`0` means sensor unavailable in observed data. Physical labels for the eight positions are not validated.

