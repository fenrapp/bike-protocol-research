# Telemetry Model Notes

A telemetry client should keep three concepts separate:

1. BLE payloads: characteristic-specific bytes and decoder outputs.
2. Local samples: app-owned telemetry snapshots with timestamps and source metadata.
3. Export/sync DTOs: file or network formats such as STF, Avro, JSON, or future APIs.

Keeping these separate avoids baking an unstable BLE layout into the UI or a public file format.

## Recommended Local Flow

```text
BLE notifications
  -> protocol decoders
  -> domain telemetry sample
  -> recorder database
  -> outbox/export
```

Diagnostics should be able to run without the recorder. Recording and sync should be separate modules or services.

## Units

Preserve protocol-native units at the decoder boundary, then expose semantic domain values.

| Field | Protocol Unit | Domain Value |
| --- | --- | --- |
| Speed | km/h x 10 | km/h as decimal |
| Odometer | centi-kilometers | kilometers as decimal |
| DC bus | V x 10 | volts as decimal |
| Cell voltage | V x 10000 | volts as decimal |
| Temperature | C x 10 | Celsius as decimal |
| Charger current | A x 10 | amperes as decimal |
| Charger max power | W | watts |

UI can convert units for display, but stored domain values should stay canonical.

## Relation to STF

`svag-telemetry-format` is a useful public reference for telemetry recording. It models ride samples with fields for VCU, inverter, battery, IMU, GPS, and metadata.

Important compatibility notes:

- STF records cell voltages in 1/10000 V, matching the validated `6007` layout.
- STF includes GPS as recorder-provided context because GPS is not available from the VCU.
- STF includes battery current and power calculation guidance, but clients should not expose traction current or power until the source field is independently validated.

## Future Recorder Guidance

For a community recorder:

- Store source characteristic UUID and capture time for raw/debug samples.
- Store decoded domain samples separately from raw packet logs.
- Use an outbox for export/sync so BLE capture is not blocked by network availability.
- Keep private identifiers optional and redacted by default.
- Do not store security material in the recorder database.
