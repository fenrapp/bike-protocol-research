# Vehicle Contracts

These models are partial, sanitized notes for internal authenticated endpoints. They document shapes useful for protocol research, especially how vehicle identity relates to BLE pairing.

Do not publish private VINs, owner IDs, keys, activation data, or account-linked responses.

## Shared Vehicle Object

Status: `Partly validated`

Observed vehicle responses include:

| Field Group | Notes |
| --- | --- |
| identity | VIN and product/model metadata. |
| configuration | Bike configuration, purpose, country, and variant-like metadata. |
| ownership | Current/past owner references. Private. |
| lifecycle | Backend state, activation state, warranty dates, shipping/sale dates. |
| security date | Sale date is relevant to BLE PIN/auth identity normalization. |
| firmware | Firmware/version fields may be present or empty. |

Important behavior:

- The vehicle list does not include Bluetooth key material.
- A backend state that looks unfinished does not prove the vehicle cannot connect over BLE.
- Warranty or shipping dates must not be substituted for the sale date when deriving BLE identity.

## `GET /v2/vehicles`

Status: `Partly validated`

Request body: none.

Response:

```text
Vehicle[]
```

Known response model:

| Field | Type | Notes |
| --- | --- | --- |
| `vin` | string | Private vehicle identifier. Redact in captures. |
| product/model fields | string/object | Exact field names vary in observed models. |
| configuration | object | Vehicle configuration metadata. |
| state/status | string | Backend lifecycle state. |
| owners | array/object | Private account links. |
| sale date | timestamp/string | Used to normalize BLE pairing identity when available. |
| activation data | object/string/null | May be empty. Do not treat empty data as a local BLE failure. |
| firmware fields | object/string/null | May be empty. |

## `GET /v2/vehicles/{vin}`

Status: `Observed`

Request:

| Location | Field | Notes |
| --- | --- | --- |
| path | `vin` | Private vehicle identifier. |

Response:

```text
Vehicle
```

Use the shared vehicle object above.

## `GET /v2/vehicles/{vin}/bt-keys`

Status: `Partly validated`

Request:

| Location | Field | Notes |
| --- | --- | --- |
| path | `vin` | Private vehicle identifier. |
| query | `forceDate` | Optional 8-digit date override observed for validation. |

Response:

| Field | Type | Notes |
| --- | --- | --- |
| short pairing key | string | Four-character value observed. Pads to the six-digit BLE pairing PIN. Treat as sensitive. |
| 32-character secret | string | Secret material. Do not log, store in fixtures, or publish. |

This endpoint validates the BLE pairing PIN relationship. It does not replace the local `00001001` challenge-response.

## Vehicle Mutation Endpoints

These endpoints are listed for completeness only. Do not call them from community tooling without authorization.

| Endpoint | Request | Response |
| --- | --- | --- |
| `PUT /v2/vehicles/{vin}/activate` | Activation request. Exact model not published. | Updated vehicle or success envelope. |
| `PUT /v2/vehicles/{vin}/deactivate` | Deactivation request. Exact model not published. | Updated vehicle or success envelope. |
| `PUT /v2/vehicles/config/{vin}` | Vehicle configuration object. | Updated configuration or vehicle. |
| `PUT /v2/vehicles/{vin}/sold-date` | Sale-date update object. | Updated vehicle or success envelope. |
| `POST /v2/vehicles/{vin}/transfer-ownership` | Transfer request, likely recipient/contact data. | Transfer state or success envelope. |
| `PUT /v2/vehicles/{vin}/confirm-transfer-ownership/{code}` | Confirmation code in path. | Updated transfer/ownership state. |
| `GET /v2/vehicles/warning-types/{vin}` | No body observed. | Warning type list. |
| `GET /v2/vehicles/{vin}/errors-history` | Query parameters unknown. | Error history list. |
| `POST /v2/vehicles/{vin}/errors-history` | Error-history filter or upload object. | Error history response or success envelope. |
| `POST /v2/vehicles/{vin}/upload-files` | Multipart or file upload body. | Uploaded file metadata or success envelope. |

