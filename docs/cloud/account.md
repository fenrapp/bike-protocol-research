# Account Contracts

These models are partial field inventories from observed internal endpoints. They are not a public API contract.

Do not publish tokens, cookies, account IDs, email addresses, phone numbers, or private profile data.

## Shared Headers

Most private endpoints require:

| Header | Required | Notes |
| --- | --- | --- |
| `Authorization` | Yes | Bearer token. Value must never be logged or shared. |
| `app-version` | Observed | Client version metadata. |
| `app-env` | Observed | Environment metadata. |

## `POST /v1/user/exists`

Status: `Observed`

Request intent: check whether a user identity exists.

Known request fields:

| Field | Type | Notes |
| --- | --- | --- |
| `email` or login identifier | string | Exact accepted identifiers need validation. |

Known response fields:

| Field | Type | Notes |
| --- | --- | --- |
| existence flag | boolean | Exact field name not validated for publication. |

## `POST /v1/user/sign-in`

Status: `Observed`

Request intent: exchange user credentials for an authenticated session.

Known request fields:

| Field | Type | Notes |
| --- | --- | --- |
| login identifier | string | Private. Do not publish. |
| password or credential material | string | Secret. Do not publish. |

Known response fields:

| Field | Type | Notes |
| --- | --- | --- |
| access token | string | Secret. Redact. |
| refresh/session token | string | Secret. Redact if present. |
| user profile | object | See profile field inventory below. |

## `GET /v1/user/me`

Status: `Observed`

Request body: none.

Known response profile fields:

| Field Group | Notes |
| --- | --- |
| identity | User ID, name, email, and contact data have been observed. Treat as private. |
| locale | Country and language metadata. |
| account state | Status, roles, dealer/unit relationships. |
| commerce | Stripe/subscription references may appear. Treat as private. |

## Other Account Endpoints

| Endpoint | Request | Response |
| --- | --- | --- |
| `GET /v1/user/sign-with-google` | OAuth-style sign-in parameters. | Auth/session response. Exact model not published. |
| `POST /v1/user/sign-up` | Registration profile and credentials. | Auth/session and profile response. Exact model not published. |
| `GET /v1/user/sign-out` | No body observed. | Success/error envelope. |
| `GET /v1/user/refresh` | Refresh/session credential. | New auth/session response. |
| `POST /v1/user/reset-password` | Login identifier. | Success/error envelope. |
| `PUT /v1/user/{userId}` | Profile update object. | Updated profile or success envelope. |
| `POST /v2/user/parental` | Parental settings object. | Updated settings or success envelope. |

