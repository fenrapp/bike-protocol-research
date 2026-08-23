# Ride, Track, and Trail Contracts

These endpoints describe ride history and map-related resources. Models are partial and should be treated as internal.

Do not publish GPS trails, thumbnails, lap data, account IDs, or vehicle identifiers unless they are intentionally public and anonymized.

## Shared Ride Object

Status: `Observed`

Known field groups:

| Field Group | Notes |
| --- | --- |
| identity | Ride ID and VIN association. |
| timing | Start/end timestamps, duration, and lap timing. |
| telemetry summaries | Stats, charts, energy, speed, and distance summaries. |
| route | GPS/GPX-derived geometry and track references. |
| media | Thumbnail references. |
| sync state | Local/server version and upload state may exist. |

## Ride Endpoints

| Endpoint | Request | Response |
| --- | --- | --- |
| `GET /v2/vehicles/{vin}/rides` | Path VIN, optional pagination/filter query. | `Ride[]` or paginated ride list. |
| `POST /v2/vehicles/{vin}/rides` | Ride create/upload object. | Created `Ride`. |
| `GET /v2/vehicles/{vin}/rides/{ride_id}` | Path VIN and ride ID. | `Ride`. |
| `PUT /v2/vehicles/{vin}/rides/{ride_id}` | Ride update object. | Updated `Ride`. |
| `DELETE /v2/vehicles/{vin}/rides/{ride_id}` | Path VIN and ride ID. | Success envelope. |
| `POST /v2/vehicles/{vin}/rides/{ride_id}/chart` | Chart data or chart request object. | Chart response. |
| `POST /v2/vehicles/{vin}/rides/{ride_id}/stats` | Stats data or stats request object. | Stats response. |
| `GET /v2/vehicles/{vin}/rides/{rideId}/laps` | Path VIN and ride ID. | Lap list. |
| `POST /v2/vehicles/{vin}/rides/{ride_id}/add-gpx` | GPX upload body. | Updated ride or GPX metadata. |
| `POST /v2/vehicles/{vin}/rides/{ride_id}/add-thumbnail` | Image upload body. | Thumbnail metadata or updated ride. |
| `GET /v1/rides/ride-charts/{vin}/{ride_id}` | Path VIN and ride ID on `api-services`. | Ride chart. |
| `PUT /v1/rides/ride-charts/{vin}/{ride_id}` | Chart update object on `api-services`. | Updated chart or success envelope. |

## Shared Track/Trail Object

Status: `Observed`

Known field groups:

| Field Group | Notes |
| --- | --- |
| identity | Track/trail ID. |
| owner | Account or creator metadata. Private unless public. |
| geometry | Route, GPX, or map data. |
| media | Thumbnail references. |
| ride links | Associated rides or laps. |

## Track and Trail Endpoints

| Endpoint | Request | Response |
| --- | --- | --- |
| `GET /v2/tracks` | Optional query/filter. | `Track[]` or paginated track list. |
| `POST /v2/tracks` | Track create object. | Created `Track`. |
| `GET /v2/tracks/{trackId}` | Path track ID. | `Track`. |
| `PUT /v2/tracks/{trackId}` | Track update object. | Updated `Track`. |
| `DELETE /v2/tracks/{trackId}` | Path track ID. | Success envelope. |
| `POST /v2/tracks/{trackId}/laps` | Lap object. | Created/updated lap response. |
| `GET /v2/tracks/{trackId}/rides` | Path track ID. | Ride list for track. |
| `POST /v2/tracks/{trackId}/thumbnail` | Image upload body. | Thumbnail metadata. |
| `GET /v2/trails` | Optional query/filter. | `Trail[]` or paginated trail list. |
| `POST /v2/trails` | Trail create object. | Created `Trail`. |
| `GET /v2/trails/{id}` | Path trail ID. | `Trail`. |
| `PUT /v2/trails/{trailId}` | Trail update object. | Updated `Trail`. |
| `DELETE /v2/trails/{trailId}` | Path trail ID. | Success envelope. |
| `POST /v2/trails/{vin}/add-gpx/{trail_id}` | GPX upload body. | Updated trail or GPX metadata. |
| `POST /v2/trails/{vin}/add-thumbnail/{trail_id}` | Image upload body. | Thumbnail metadata or updated trail. |

