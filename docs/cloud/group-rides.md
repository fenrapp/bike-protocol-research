# Group Ride Contracts

Group rides use `api-services` and temporary MQTT connection details. Treat all returned MQTT values as short-lived credentials.

Do not publish participant names, location, group PINs, MQTT authorizers, topics, or connection usernames.

## Shared Group Ride Request Fields

Status: `Observed`

| Field | Type | Notes |
| --- | --- | --- |
| participant name | string | Private display name. |
| position | object | Latitude/longitude or location payload. Private. |
| radius | number | Join/search radius candidate. |
| max members | number | Group size limit candidate. |
| PIN | string/number | Group join secret. Do not publish. |

## Shared MQTT Response Fields

Status: `Observed`

| Field | Type | Notes |
| --- | --- | --- |
| endpoint | string | MQTT over secure WebSocket endpoint. Treat as dynamic. |
| authorizer | string | Dynamic credential/authorizer reference. |
| user | string | Connection identity. Private. |
| publish topic | string | Topic for local position/event publishing. |
| subscribe topics | array | Topics for group updates. |

Observed MQTT behavior:

- MQTT 5 over secure WebSocket.
- Port `443`.
- Path `/mqtt`.
- QoS at least once for position/event messages.

## Endpoints

| Endpoint | Request | Response |
| --- | --- | --- |
| `POST /v3/app/group-rides` | Create-group request with participant, position, radius, and max-member fields. | Group ride object plus temporary MQTT fields. |
| `POST /v3/app/group-rides/join` | Join request with group identifier or PIN plus participant/position fields. | Group ride object plus temporary MQTT fields. |
| `POST /v3/app/group-rides/{rideId}/leave` | Path ride ID and participant/session identity. | Success envelope or updated group state. |
| `POST /v3/app/group-rides/{rideId}/close` | Path ride ID and owner/session identity. | Success envelope or closed group state. |

