# VCU BLE Protocol Research

Unofficial interoperability notes for BLE telemetry, authentication and scoped configuration records.

This repository is a public research notebook, not a product SDK. It documents what has been validated from observed behavior, open-source prior art, controlled BLE captures, and clean-room implementation work. The goal is to help builders understand the protocol surface without redistributing proprietary code, credentials, or private captures.

## Status 🔎

| Area | Status | Notes |
| --- | --- | --- |
| BLE UUID catalog | Validated | 8 proprietary services, 48 proprietary characteristics, and one standard Battery Level characteristic. |
| BLE authentication flow | Observed | App-level challenge-response on `00001001` is required before protected telemetry. |
| Core telemetry payloads | Partly validated | Several ride, battery, charger, inverter, and status payloads are decoded. |
| Cloud endpoints | Observed | Internal authenticated endpoints are listed only as research context. |
| Configuration records | Mixed evidence | Charging power/target and base-map changes have physical evidence. Traction and lock layouts are documented separately from pending instrumented write/read-back evidence. See [VCU configuration](docs/ble/configuration.md). |

## Testing Gotcha

The VCU only accepts one active BLE client connection. If another client is already connected to the bike, a new client will usually fail to connect or will never reach a useful telemetry session. For protocol testing, disconnect any existing client before starting a new session.

## Start Here 🧭

- [BLE overview](docs/ble/overview.md)
- [UUID catalog](docs/ble/uuid-catalog.md)
- [Authentication](docs/ble/authentication.md)
- [Payload layouts](docs/ble/payloads.md)
- [VCU configuration and write evidence](docs/ble/configuration.md)
- [Unknowns and validation checklist](docs/ble/unknowns.md)
- [Cloud endpoint inventory](docs/cloud/endpoints.md)
- [Telemetry model notes](docs/data/telemetry-model.md)
- [GPS, IMU, and inclinometer feasibility](docs/data/gps-imu-inclinometer.md)
- [Open-source references](docs/references/open-source-projects.md)

## Safety Rules ⚠️

- This repository contains documentation, not an executable vehicle client. Documented configuration records do not establish a general safe write surface.
- Do not write control, lock, unlock, mode, charging, or configuration characteristics.
- Do not publish bearer tokens, Bluetooth keys, nonce values, challenge responses, private VINs, or raw captures that identify a vehicle or account.
- Treat all cloud endpoints as internal and unstable.
- Label findings as `Validated`, `Observed`, `Inferred`, or `Unknown`.

## License

The original documentation and code in this repository are released under the
[MIT License](LICENSE). It permits reuse, modification, redistribution, and
commercial use while requiring preservation of the copyright and license notice.

Referenced third-party projects retain their own licenses. The repository's
license covers our original contributions, not third-party code, trademarks,
patents, or ownership of the documented protocol.

## Credits 🤝

This work builds on public open-source exploration and local validation. See [Open-source references](docs/references/open-source-projects.md) for project-level credits and what each source contributed.

## Disclaimer

This project is unofficial, reverse-engineered research. It is not affiliated with, endorsed by, or supported by the vehicle manufacturer. Interfaces may change, and using unofficial tooling around a vehicle can carry safety, warranty, legal, and account risks. Use this information carefully and at your own risk.
