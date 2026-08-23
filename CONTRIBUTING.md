# Contributing

Contributions should improve protocol clarity without exposing private data.

## Good Contributions

- Better descriptions of validated BLE payloads.
- New captures summarized as byte layout findings, with identifying data removed.
- Firmware/model notes that explain when a layout was observed.
- Test vectors built from synthetic data.
- Corrections that distinguish a confirmed fact from an inference.

## Capture Hygiene

Before sharing anything, remove:

- VINs and account IDs.
- Bearer tokens and API cookies.
- Bluetooth keys, including short PIN material and 32-character keys.
- Real nonces and real authentication responses.
- Device serial numbers when they can identify a vehicle.
- GPS tracks unless they are intentionally public and anonymized.

Use this format when reporting a payload:

```text
Model/variant: <vehicle family, if safe to share>
Firmware/app context: <version or "unknown">
Characteristic: <short UUID>
Length: <byte count>
Operation: notify/read/write result
Confidence: Validated | Observed | Inferred | Unknown
Known external reference: <display value, charger setting, measured value, etc.>
Sanitized bytes: <redacted or synthetic unless safe>
```

## Confidence Labels

- `Validated`: Confirmed by repeated captures or implementation tests against known behavior.
- `Observed`: Documented protocol behavior or candidate layout that is not fully validated.
- `Inferred`: Reasonable interpretation from nearby fields, names, or behavior.
- `Unknown`: Documented as a discovery target only.

## Tone

Keep writing plain and useful. Avoid hype, speculation, and claims that make private interfaces look official or stable.

