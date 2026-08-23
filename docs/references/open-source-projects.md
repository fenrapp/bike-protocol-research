# Open-Source References

This research was accelerated by existing public projects. They provided early maps, working assumptions, and comparison points. Local validation updated several details after controlled captures.

## `b1naryth1ef/svag-mini`

Project: `svag-mini`

What it contributed:

- ESP32 BLE client behavior for read-only telemetry.
- Early service and characteristic list for battery, bike status, live speed, and map.
- Notification-first approach.
- Status, SOC, speed, and mode parsing reference.
- VIN-based pairing PIN rule that was later corrected and validated with implementation tests.

Important differences found later:

- `6004` SOC is direct percent in the observed payload, not divided by 10.
- The current payload catalog uses `6007` for cell voltages and `6008` for balancing.
- App-level authentication on `00001001` is required before protected telemetry in the validated flow.

## `svag-telemetry-format`

Project: `svag-telemetry-format`

What it contributed:

- A public telemetry recording model.
- Field vocabulary for VCU, inverter, battery, IMU, GPS, and metadata.
- Confirmation that cell voltages are represented in 1/10000 V.
- A useful separation between vehicle-provided telemetry and recorder-provided GPS context.

A telemetry client should treat STF as an export/reference model, not as the internal BLE decoder model.

## `stark-varg-garmin-bridge`

Project: `stark-varg-garmin-bridge`

What it contributed:

- A practical bridge architecture: BLE central reads the bike and a second BLE peripheral exposes a compact downstream telemetry packet.
- Useful documentation style for community-facing protocol notes.
- A compact downstream packet design for Garmin-style consumers.
- Notes about bonding and connection behavior.

This repository does not copy its downstream bridge protocol, but the project is a useful reference for read-only consumer integrations.

## `Soarcer/bosch-garmin-bridge`

Project: `Soarcer/bosch-garmin-bridge`

What it contributed indirectly:

- Inspiration for phone-to-Garmin bridge architecture through the Garmin bridge work above.
- A precedent for keeping vehicle BLE parsing separate from the downstream display protocol.

## Attribution Notes

This repository uses these projects as references and credits them explicitly. It does not vendor their code.
