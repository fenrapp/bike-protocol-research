# Telemetry Ingest Contracts

Telemetry ingest is documented only as architecture context for local recording/export design. These endpoints are internal and authenticated.

Do not publish auth headers, raw private rides, VIN-linked telemetry, GPS tracks, or request signatures.

## Local Data Shape

Observed drive-data models combine:

| Field Group | Notes |
| --- | --- |
| vehicle identity | VIN and vehicle metadata. Private. |
| user/ride identity | User ID, ride ID, tags, and sync metadata. Private. |
| BLE telemetry | Battery, inverter, VCU, charger, status, speed, IMU, and derived values. |
| route context | GPS, altitude, heading, accuracy, map/track/trail references. |
| diagnostics | Faults, warning types, versions, and sync flags. |

## `POST /insert`

Status: `Observed`

Request:

| Part | Type | Notes |
| --- | --- | --- |
| body | JSON | Drive-data or telemetry sample payload. Exact schema not validated for publication. |
| auth | headers | Required. Do not publish values. |

Response:

| Field | Type | Notes |
| --- | --- | --- |
| success/error envelope | object | Exact fields unknown. |

## `POST /ingest`

Status: `Observed`

Request:

| Part | Type | Notes |
| --- | --- | --- |
| body | Avro binary | Telemetry batch payload. Exact Avro schema not published. |
| compression | header/body encoding | `Content-Encoding: zstd` observed. |
| auth | headers | Required. Do not publish values. |

Response:

| Field | Type | Notes |
| --- | --- | --- |
| success/error envelope | object | Exact fields unknown. |

## Export Design Implication

A community recorder should keep its own stable local schema and export format. Do not couple local storage directly to these internal ingest shapes.

