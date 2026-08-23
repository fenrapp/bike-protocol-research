# BLE Overview

## Protocol Shape

The vehicle exposes proprietary BLE services under a common UUID suffix:

```text
XXXXXXXX-5374-6172-4b20-467574757265
```

The suffix decodes to the ASCII text `Stark Future`. The first 32 bits identify each service or characteristic.

The vehicle advertises with the VIN as the BLE device name in observed flows. A client can scan for the VIN, connect over BLE, discover services, complete security, and then subscribe to telemetry notifications.

## Single-Client VCU Constraint

Observed behavior indicates that the VCU only accepts one active BLE client connection. If another client is already connected to the bike, a new BLE client usually cannot connect or cannot progress far enough to receive protected telemetry.

For testing:

1. Disconnect any existing BLE client from the bike.
2. Start the custom client only after the existing connection has dropped.
3. If connection attempts fail silently, check for another connected client before debugging UUIDs, pairing, or authentication.

## Connection Flow

Observed read-only telemetry flow:

1. Scan for a BLE peripheral whose name matches the target VIN.
2. Connect over LE.
3. Complete OS-level bonding if the platform requires it.
4. Discover services and characteristics.
5. Run app-level authentication on `00001001`.
6. Subscribe to read-only telemetry characteristics.
7. Decode notification payloads by characteristic UUID and length.

Notification setup alone is not enough. Enabling CCCD or receiving a notification-state callback only proves that the client requested notifications. It does not prove the vehicle accepted the app-level security challenge.

## Read-Only Research Profile

The current read-only research profile only subscribes to telemetry and battery-health data. It does not write control or configuration values.

Base telemetry:

| Short UUID | Purpose |
| --- | --- |
| `1002` | Vehicle status and flags |
| `2001` | Speed and motor RPM |
| `2004` | Map index |
| `2005` | Live counters, first counter validated as odometer |
| `4100` | VCU TLV, brake activity decoded for specific frames |
| `6004` | Battery SOC, SOH, and DC bus |

Battery/charging health:

| Short UUID | Purpose |
| --- | --- |
| `5001` | Charger data |
| `6001` | BMS status capture |
| `6005` | Battery temperatures |
| `6006` | Battery DC bus capture |
| `6007` | Cell voltages |
| `6008` | Cell balancing bitmap |
| `6009` | Battery signals capture |
| `7003` | Inverter temperatures |

## Safety Boundary

This repository documents read-only interoperability. Do not write to vehicle control, configuration, lock, unlock, charging, firmware, or ownership-related paths from community tools unless you have explicit authorization and a validated safety process.
