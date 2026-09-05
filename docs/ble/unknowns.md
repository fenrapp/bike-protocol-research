# Unknowns and Validation Checklist

This file lists what should not be treated as product-ready.

## High-Value Unknowns

| Area | UUID | Current State | Needed Validation |
| --- | --- | --- | --- |
| Traction current | `6009` offset 16 | Inferred | Compare against an external current measurement or validated charger output at multiple currents. |
| Instant power | `6004` x `6009` candidate | Inferred | Validate the current scale before presenting the result as measured power. |
| BMS voltage candidates | `6009` offsets 0 and 8 | Inferred | Compare with simultaneous sums of `6007` cell groups `0...49` and `50...99`. |
| Trip counters | `2005` | Partly validated | Compare all four counters against known ride/reset states. |
| Range/time/power estimate | `2006` | Observed six-byte layout, units unknown | Compare raw fields with independent distance/time/power references. |
| Racing/mode metadata | `2007` | Unknown | Capture while changing configured maps and racing modes. |
| Inverter temperature labels | `7003` | Values validated, labels unknown | Thermal comparison or trusted component mapping. |
| VCU inputs | `4002`, `4100` | Partly known | Capture brake, start, kill, pump, fan, and map switch changes independently. |
| Lock configuration | `4005` type `5`, `1002` | Observed layout and guarded implementation | Capture exact no-op, both state changes, fresh read-back and status telemetry with firmware inventory. |
| Traction configuration | `4005` type `8` | Reads observed; corrected write lacks instrumented success evidence | Capture mode `0x0F`, zero write status and exact fresh read of both signed tenths values. |
| Pack capacity | `6003` | Raw layout observed | Confirm capacity scale against a known pack; do not infer Ah or Wh. |
| IMU axes and scale | `2003` | Raw six-axis layout observed | Measure mounting axes, gravity magnitude, gyro scale and cadence before deriving riding angles. |

Charging controls and base-map writes have separate physical evidence in
[VCU configuration](configuration.md). This does not validate arbitrary curves,
other configuration types or all firmware versions.

## Validation Checklist

For each new field:

1. Make sure any existing client is fully disconnected; the VCU only accepts one active BLE client.
2. Capture characteristic UUID, operation type, payload length, and timestamp.
3. Record the external truth source: dash value, charger setting, measured voltage, mode label, or physical action.
4. Repeat across at least two states.
5. Confirm byte order and signedness.
6. Confirm scale and unit.
7. Add a synthetic or anonymized fixture.
8. Document confidence as `Validated`, `Observed`, `Inferred`, or `Unknown`.

## Do Not Infer Yet

- Treat power derived from `6009` as an estimate until traction current is independently validated.
- Do not label charger current as battery discharge or motor current.
- Do not treat SOH `0` as 0 percent when observed data indicates unknown.
- Do not infer `Off` from notification timeout alone.
- Do not claim lock/unlock support from status fields.
- Do not map mode indexes to names globally; configured labels can vary.

## Capture Safety

Prefer stationary captures first. For riding captures, use a second person or automated logging so the rider is not operating a display or phone.

Never share raw captures that contain identifying VINs, serial numbers, GPS tracks, account IDs, tokens, or security material.
