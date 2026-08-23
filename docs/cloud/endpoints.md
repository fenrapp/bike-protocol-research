# Cloud Endpoint Inventory

This is an observed inventory of internal network surfaces. It is not an API specification and should not be treated as stable or supported.

Do not build community tooling that depends on these endpoints without authorization. Do not publish credentials, tokens, cookies, request signatures, or private account data.

## Hosts

| Host | Observed Use |
| --- | --- |
| `https://api.starkfuture.com` | Account, vehicles, rides, tracks, trails, diagnostics, payments, subscriptions, and updates. |
| `https://api-services.starkfuture.com` | Group rides and auxiliary app services. |
| `https://bike-streaming.starkfuture.com` | Telemetry ingestion. |

## Headers

Observed main API headers:

| Header | Notes |
| --- | --- |
| `Authorization: Bearer <token>` | Required for private resources. Never publish tokens. |
| `app-version` | Client version metadata. |
| `app-env: production` | Environment metadata. |

Some service calls use an `x-auth` style client-derived header. The value and derivation are intentionally not documented here.

## Account and User

Model notes: [Account contracts](account.md)

| Method | Path |
| --- | --- |
| `POST` | `/v1/user/exists` |
| `POST` | `/v1/user/sign-in` |
| `GET` | `/v1/user/sign-with-google` |
| `POST` | `/v1/user/sign-up` |
| `GET` | `/v1/user/sign-out` |
| `GET` | `/v1/user/refresh` |
| `POST` | `/v1/user/reset-password` |
| `GET` | `/v1/user/me` |
| `PUT` | `/v1/user/{userId}` |
| `POST` | `/v2/user/parental` |

## Vehicles and Security

Model notes: [Vehicle contracts](vehicles.md)

| Method | Path |
| --- | --- |
| `GET` | `/v2/vehicles` |
| `GET` | `/v2/vehicles/{vin}` |
| `PUT` | `/v2/vehicles/{vin}/activate` |
| `PUT` | `/v2/vehicles/{vin}/deactivate` |
| `GET` | `/v2/vehicles/{vin}/bt-keys` |
| `GET` | `/v2/vehicles/{vin}/bt-keys?forceDate=...` |
| `PUT` | `/v2/vehicles/config/{vin}` |
| `PUT` | `/v2/vehicles/{vin}/sold-date` |
| `POST` | `/v2/vehicles/{vin}/transfer-ownership` |
| `PUT` | `/v2/vehicles/{vin}/confirm-transfer-ownership/{code}` |
| `GET` | `/v2/vehicles/warning-types/{vin}` |
| `GET` / `POST` | `/v2/vehicles/{vin}/errors-history` |
| `POST` | `/v2/vehicles/{vin}/upload-files` |

Observed vehicle models include VIN, product/model metadata, configuration, state, owners, sale date, activation, warranty, country, and purpose.

Security note: `bt-keys` responses include secret material. Do not log, store in fixtures, or publish values from this endpoint.

## Rides

Model notes: [Ride, track, and trail contracts](rides-tracks.md)

| Method | Path |
| --- | --- |
| `GET` / `POST` | `/v2/vehicles/{vin}/rides` |
| `GET` / `PUT` / `DELETE` | `/v2/vehicles/{vin}/rides/{ride_id}` |
| `POST` | `/v2/vehicles/{vin}/rides/{ride_id}/chart` |
| `POST` | `/v2/vehicles/{vin}/rides/{ride_id}/stats` |
| `GET` | `/v2/vehicles/{vin}/rides/{rideId}/laps` |
| `POST` | `/v2/vehicles/{vin}/rides/{ride_id}/add-gpx` |
| `POST` | `/v2/vehicles/{vin}/rides/{ride_id}/add-thumbnail` |
| `GET` / `PUT` | `/v1/rides/ride-charts/{vin}/{ride_id}` on `api-services` |

## Tracks and Trails

Model notes: [Ride, track, and trail contracts](rides-tracks.md)

| Method | Path |
| --- | --- |
| `GET` / `POST` | `/v2/tracks` |
| `GET` / `PUT` / `DELETE` | `/v2/tracks/{trackId}` |
| `POST` | `/v2/tracks/{trackId}/laps` |
| `GET` | `/v2/tracks/{trackId}/rides` |
| `POST` | `/v2/tracks/{trackId}/thumbnail` |
| `GET` / `POST` | `/v2/trails` |
| `GET` | `/v2/trails/{id}` |
| `PUT` / `DELETE` | `/v2/trails/{trailId}` |
| `POST` | `/v2/trails/{vin}/add-gpx/{trail_id}` |
| `POST` | `/v2/trails/{vin}/add-thumbnail/{trail_id}` |

## Group Rides

Observed on `api-services`:

Model notes: [Group ride contracts](group-rides.md)

| Method | Path |
| --- | --- |
| `POST` | `/v3/app/group-rides` |
| `POST` | `/v3/app/group-rides/join` |
| `POST` | `/v3/app/group-rides/{rideId}/leave` |
| `POST` | `/v3/app/group-rides/{rideId}/close` |

Responses include temporary MQTT connection details: endpoint, authorizer, user, publish topic, and subscribe topics. Treat them as dynamic credentials, not static configuration.

## Telemetry Ingest

Observed upload shapes:

Model notes: [Telemetry ingest contracts](telemetry-ingest.md)

| Method | Path | Notes |
| --- | --- | --- |
| `POST` | `/insert` | JSON ingestion candidate. |
| `POST` | `/ingest` | Avro ingestion candidate, optionally compressed with `Content-Encoding: zstd`. |

These paths are useful as architectural context for a local recorder/outbox, not as a public integration contract.
