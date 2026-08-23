# BLE Authentication

There are two distinct security layers:

1. OS-level Bluetooth bonding/encryption.
2. App-level challenge-response on characteristic `00001001`.

Both can matter. A successful connection, service discovery, or CCCD write does not prove the app-level session is authenticated.

## Pairing Identity

The pairing identity is built from:

```text
NORMALIZED_VIN + "-" + NORMALIZED_DATE
```

Rules:

- VIN is uppercased and stripped to letters and digits.
- Date is stripped to digits and the first 8 digits are used.
- If no usable date is available, use `19700101`.
- The final PIN is six decimal digits with left zero padding.

## Bonding PIN Derivation

Validated clean-room rule:

```text
identity = NORMALIZED_VIN + "-" + NORMALIZED_DATE
h = SHA256(identity)

d1 = ((h[3]  XOR 197) + 228) mod 10
d2 = ((h[14] XOR 236) + 209) mod 10
d3 = ((h[9]  XOR 158) + 100) mod 10
d4 = ((h[15] XOR 179) + 239) mod 10

pin = d1 + d2*10 + d3*100 + d4*1000
display = pin left-padded to 6 digits
```

This derives the Bluetooth pairing PIN. It does not replace the app-level challenge-response.

## Challenge-Response Flow on `00001001`

Observed flow:

1. Discover the Bike service `00001000`.
2. Find characteristic `00001001`.
3. Discover descriptors and require CCCD `0x2902`.
4. Enable notifications on `00001001`.
5. Read `00001001`.
6. Require a 32-byte nonce from the vehicle.
7. Build a V2 response using VIN, normalized date, variant `2`, and the nonce.
8. Write the response to `00001001` with response.
9. Wait for a notification.
10. Treat first byte `0x01` as success.
11. Disable security notifications.
12. Start telemetry subscriptions.

## V2 Response Shape

The response is 34 bytes:

```text
02 01 || SHA256(derived_key_16 || 02 01 || nonce_32)
```

Where:

- `02 01` is the observed V2 header.
- `nonce_32` is the 32-byte value read from `00001001`.
- `derived_key_16` is produced from the normalized pairing identity and variant-2 byte rules.

Do not log or publish real nonces or real response payloads. Synthetic fixtures are fine.

## Cloud Key Endpoint Relationship

An authenticated vehicle-key endpoint has been observed returning:

- a short value that matches the meaningful four digits of the Bluetooth PIN after zero padding;
- a 32-character value that must be treated as secret material.

This confirms the PIN relationship, but it does not prove that the endpoint can replace the local `00001001` challenge-response. The BLE V2 flow should be treated as a local challenge-response unless an authorized source documents otherwise.

## Failure Modes

| Symptom | Likely Meaning |
| --- | --- |
| `Insufficient Authentication` | Link may not be bonded or encrypted enough. |
| `Insufficient Encryption` | OS-level BLE encryption is missing or incomplete. |
| CCCD enabled, no telemetry | App-level authentication may not have completed. |
| Nonce length not 32 bytes | Do not build a response; fail the security phase. |
| Result byte not `0x01` | Treat authentication as failed. |

## Implementation Notes

- Keep the security state machine strict.
- Accept a nonce only while reading a nonce.
- Accept an auth result only after writing the response.
- Subscribe to protected telemetry only after result byte `0x01`.
- Log security phase names and byte lengths, never secret bytes.

