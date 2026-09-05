# GPS, IMU and Attitude Evidence

This note separates vehicle payloads from recorder-provided location and derived
motion estimates. It does not specify a client platform or presentation layer.

## Confirmed scope

| Capability | Evidence |
| --- | --- |
| Vehicle IMU | `2003` has six signed little-endian 16-bit axes: acceleration X/Y/Z, then gyroscope X/Y/Z. |
| IMU physical units | Unvalidated; raw layout tests do not establish acceleration or angular-rate scales. |
| Sensor mounting orientation | Unvalidated; axis permutation and signs need physical references. |
| Vehicle GPS | No GPS characteristic has been identified in the catalog examined. STF describes GPS as recorder-provided context. |
| Native lean/pitch/yaw | No validated attitude or quaternion field has been identified. |

The six-axis payload layout does not establish physical units, notification
cadence or accuracy. The absence of an identified GPS payload is not proof about
all vehicle hardware or future firmware.

## Derived values

Raw axes and derived attitude must remain separate. A stationary tilt estimate
can use gravity after axis/scale calibration; riding acceleration and cornering
forces make that estimate insufficient for dynamic lean. Gyro integration also
requires a measured scale, timestamps and bias/drift handling.

The six-axis record contains no magnetometer field and supplies no independently
validated absolute heading. Recorder GNSS can provide course while moving, but
it must remain distinct from a vehicle-sourced compass or heading measurement.

## Missing physical evidence

- Stable stationary captures to measure gyro bias, the acceleration magnitude at
  one gravity, sample timing and packet loss.
- Known positive and negative roll/pitch references to establish mounting axes.
- Timed rotations to measure angular-rate scale and signs.
- Repeated measurements across restarts and identified firmware versions.
- A synchronized independent angle reference during controlled riding before
  claiming dynamic lean, G-force or airtime accuracy.

Only derived calibration constants and synthetic fixtures belong in public
implementation repositories. Raw captures and location histories can identify
a rider or vehicle and must remain private.

See [payload layouts](../ble/payloads.md) and the public
[STF reference](https://github.com/b1naryth1ef/svag-telemetry-format).
