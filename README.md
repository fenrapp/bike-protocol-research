# VCU BLE Protocol Research

Unofficial interoperability notes for read-only BLE telemetry and related data flows.

This repository is a public research notebook, not a product SDK. It documents what has been validated from observed behavior, open-source prior art, controlled BLE captures, and clean-room implementation work. The goal is to help builders understand the protocol surface without redistributing proprietary code, credentials, or private captures.

## Status 🔎

| Area | Status | Notes |
| --- | --- | --- |
| BLE UUID catalog | Validated | 8 proprietary services, 48 proprietary characteristics, and one standard Battery Level characteristic. |
| BLE authentication flow | Observed | App-level challenge-response on `00001001` is required before protected telemetry. |
| Core telemetry payloads | Partly validated | Several ride, battery, charger, inverter, and status payloads are decoded. |
| Cloud endpoints | Observed | Internal authenticated endpoints are listed only as research context. |
| Control commands | Unknown | No lock, unlock, power, mode-write, or configuration command is documented as safe or validated. |

## Testing Gotcha

The VCU only accepts one active BLE client connection. If another client is already connected to the bike, a new client will usually fail to connect or will never reach a useful telemetry session. For protocol testing, disconnect any existing client before starting a new session.

## Start Here 🧭

- [BLE overview](docs/ble/overview.md)
- [UUID catalog](docs/ble/uuid-catalog.md)
- [Authentication](docs/ble/authentication.md)
- [Payload layouts](docs/ble/payloads.md)
- [Unknowns and validation checklist](docs/ble/unknowns.md)
- [Cloud endpoint inventory](docs/cloud/endpoints.md)
- [Telemetry model notes](docs/data/telemetry-model.md)
- [Open-source references](docs/references/open-source-projects.md)

## Safety Rules ⚠️

- Read-only telemetry only.
- Do not write control, lock, unlock, mode, charging, or configuration characteristics.
- Do not publish bearer tokens, Bluetooth keys, nonce values, challenge responses, private VINs, or raw captures that identify a vehicle or account.
- Treat all cloud endpoints as internal and unstable.
- Label findings as `Validated`, `Observed`, `Inferred`, or `Unknown`.

## License

The documentation in this repository is released under [CC0 1.0 Universal](LICENSE). That keeps reuse friction low for research, ports, and independent tools.

## Credits 🤝

This work builds on public open-source exploration and local validation. See [Open-source references](docs/references/open-source-projects.md) for project-level credits and what each source contributed.

## Disclaimer

This project is unofficial, reverse-engineered research. It is not affiliated with, endorsed by, or supported by the vehicle manufacturer. Interfaces may change, and using unofficial tooling around a vehicle can carry safety, warranty, legal, and account risks. Use this information carefully and at your own risk.
