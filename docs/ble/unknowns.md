# Unknowns and Validation Checklist

This file lists what should not be treated as product-ready.

## High-Value Unknowns

| Area | UUID | Current State | Needed Validation |
| --- | --- | --- | --- |
| Traction current | `6009`, `7002`, or TLV | Unknown | Controlled captures while riding, coasting, charging, and stationary. |
| Instant power | Unknown | Unknown | Identify real traction current before calculating power. |
| Trip counters | `2005` | Partly validated | Compare all four counters against known ride/reset states. |
| Range/time/power estimate | `2006` | Unknown | Capture at different SOC levels and compare with displayed range/time. |
| Racing/mode metadata | `2007` | Unknown | Capture while changing configured maps and racing modes. |
| Inverter temperature labels | `7003` | Values validated, labels unknown | Thermal comparison or trusted component mapping. |
| VCU inputs | `4002`, `4100` | Partly known | Capture brake, start, kill, pump, fan, and map switch changes independently. |
| Lock/control state | `1002` and unknown writes | Unknown | Do not implement without authorization and a safety process. |

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

- Do not compute motor power until traction current is identified.
- Do not label charger current as battery discharge or motor current.
- Do not treat SOH `0` as 0 percent when observed data indicates unknown.
- Do not infer `Off` from notification timeout alone.
- Do not claim lock/unlock support from status fields.
- Do not map mode indexes to names globally; configured labels can vary.

## Capture Safety

Prefer stationary captures first. For riding captures, use a second person or automated logging so the rider is not operating a display or phone.

Never share raw captures that contain identifying VINs, serial numbers, GPS tracks, account IDs, tokens, or security material.
