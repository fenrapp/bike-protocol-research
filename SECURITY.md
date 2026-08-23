# Security Policy

This repository documents interoperability research. It must not become a place to publish secrets or operational credentials.

## Do Not Publish

- Bearer tokens, refresh tokens, cookies, or session material.
- Bluetooth key material.
- Real challenge nonces.
- Real authentication response payloads.
- Proprietary binaries, third-party source code, or private cryptographic material.
- Raw captures containing VINs, serial numbers, account IDs, GPS trails, or other identifying data.

## Reporting Sensitive Issues

If you find sensitive material in this repository, open a minimal issue that identifies the file and section without repeating the secret. If private coordination is available, use that path for details.

## Research Boundary

The documented BLE work is read-only telemetry research. Control commands, account flows, ownership transfer, payment, subscription, and firmware update paths are out of scope unless there is explicit authorization and a safe disclosure path.

