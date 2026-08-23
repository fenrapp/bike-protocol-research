# BLE UUID Catalog

All proprietary UUIDs use this form:

```text
XXXXXXXX-5374-6172-4b20-467574757265
```

The tables below use the short `XXXXXXXX` prefix.

## Services

| Area | Short UUID | Status | Notes |
| --- | --- | --- | --- |
| Bike | `00001000` | Validated | Security, status, VIN, versions, and bike TLV. |
| Live | `00002000` | Validated | Riding telemetry and live TLV. |
| Dock | `00003000` | Observed | Dock and Qi data. |
| VCU | `00004000` | Validated | VCU versions, info, configuration, and TLV. |
| Charger | `00005000` | Validated | Charger data and TLV. |
| Battery | `00006000` | Validated | BMS, SOC, cells, balancing, and battery TLV. |
| Inverter | `00007000` | Validated | Inverter info, signals, temperatures, PCB, and TLV. |
| Lights | `00008000` | Observed | Lights TLV. |

## Bike Service `00001000`

| Short UUID | Status | Purpose |
| --- | --- | --- |
| `00001001` | Observed | App-level security challenge-response. |
| `00001002` | Validated | Status flags and derived vehicle state. |
| `00001003` | Observed | VIN as ASCII bytes. |
| `00001005` | Observed | BLE/version information. |
| `00001006` | Observed | Phone state of charge. |
| `00001100` | Observed | Bike telemetry TLV. |
| `00001101` | Observed | Bike telemetry TLV configuration. |
| `00002A19` | Observed | Standard Bluetooth Battery Level characteristic. |

## Live Service `00002000`

| Short UUID | Status | Purpose |
| --- | --- | --- |
| `00002001` | Validated | Speed and motor RPM. |
| `00002002` | Observed | Throttle and current feedback values. |
| `00002003` | Observed | IMU acceleration and gyroscope values. |
| `00002004` | Validated | Active map index. |
| `00002005` | Partly validated | Four counters; first is odometer in centi-kilometers. |
| `00002006` | Unknown | Estimation candidate: range, time, or power. |
| `00002007` | Unknown | Racing/category/mode candidate. |
| `00002008` | Observed | Live configuration. |
| `00002100` | Observed | Live telemetry TLV. |
| `00002101` | Observed | Live telemetry TLV configuration. |

## Dock Service `00003000`

| Short UUID | Status | Purpose |
| --- | --- | --- |
| `00003001` | Observed | Dock version. |
| `00003002` | Observed | Qi status. |
| `00003100` | Observed | Dock telemetry TLV. |
| `00003101` | Observed | Dock telemetry TLV configuration. |

## VCU Service `00004000`

| Short UUID | Status | Purpose |
| --- | --- | --- |
| `00004001` | Observed | VCU versions and serials. |
| `00004002` | Unknown | VCU information. |
| `00004005` | Observed | Bike configuration. |
| `00004100` | Partly validated | VCU telemetry TLV; brake activity decoded for frames with header `05 0F`. |

## Charger Service `00005000`

| Short UUID | Status | Purpose |
| --- | --- | --- |
| `00005001` | Validated | Charger current, limits, target cell voltage, status, enable flag, and charger type. |
| `00005100` | Observed | Charger telemetry TLV. |
| `00005101` | Observed | Charger telemetry TLV configuration. |

## Battery Service `00006000`

| Short UUID | Status | Purpose |
| --- | --- | --- |
| `00006001` | Observed | BMS status/fault capture. |
| `00006002` | Observed | Battery firmware and serial information. |
| `00006003` | Observed | Battery pack parameters. |
| `00006004` | Validated | SOC, SOH, and DC bus. |
| `00006005` | Validated | Pack temperatures. |
| `00006006` | Observed | Battery DC bus capture. |
| `00006007` | Validated | 100 cell voltages. |
| `00006008` | Validated | Cell balancing bitmap. |
| `00006009` | Observed | BMS signal capture; current semantics not validated. |
| `0000600A` | Observed | Battery configuration. |
| `00006100` | Observed | Battery telemetry TLV. |
| `00006101` | Observed | Battery telemetry TLV configuration. |

## Inverter Service `00007000`

| Short UUID | Status | Purpose |
| --- | --- | --- |
| `00007001` | Observed | Inverter information. |
| `00007002` | Unknown | Inverter signals; possible current/power candidates remain unvalidated. |
| `00007003` | Validated | Eight inverter temperature values. |
| `00007004` | Observed | Inverter PCB data. |
| `00007100` | Observed | Inverter telemetry TLV. |
| `00007101` | Observed | Inverter telemetry TLV configuration. |

## Lights Service `00008000`

| Short UUID | Status | Purpose |
| --- | --- | --- |
| `00008100` | Observed | Lights telemetry TLV. |
| `00008101` | Observed | Lights telemetry TLV configuration. |

