---
layout: page
title: Active Aerodynamics for an Actively Stable Amateur Rocket
description: IMU-driven canard control system with Kalman filter attitude estimation for active roll and pitch stabilisation during flight.
img: assets/img/project2.jpg
importance: 2
category: work
giscus_comments: false
---

Most amateur rockets are passively stable — fixed fins keep them pointed the right way through geometry alone. This project takes a different approach: an active canard system that continuously reads the rocket's attitude and deflects four control surfaces in real time to correct deviations during flight. Think of it as a very lightweight, very fast autopilot built around a £5 IMU and an Arduino.

---

## The Problem with Passive Stability

Passive stability works, but it comes with a trade-off — the further back your centre of pressure sits relative to your centre of mass, the more stable the rocket is, but the less responsive it is to thrust vectoring or intentional manoeuvring. For my actively stable rocket concept, we want tighter attitude control throughout the flight envelope, which means closing the loop with real sensor feedback. This, in theory, should allow the rocket to stay stable in all sorts of wind conditions.

---

## Sensor Fusion — Why a Kalman Filter

The core challenge is getting a clean, reliable angle estimate from an MPU6050 IMU. The sensor gives you two sources of information:

- **Accelerometer** — good absolute angle reference, but noisy (especially on a vibrating rocket airframe)
- **Gyroscope** — smooth rate data, but drifts over time due to bias

Neither is useful on its own. A **Kalman filter** fuses both optimally — using the gyro to propagate the angle estimate between measurements, while continuously correcting for gyro bias using the accelerometer as a reference. The result is a stable, low-latency angle estimate that handles both vibration noise and long-duration drift.

The filter maintains a 2×2 error covariance matrix and tunes itself via three parameters:

| Parameter | Value | Role |
|-----------|-------|------|
| Q_angle | 0.001 | Process noise — accelerometer trust |
| Q_bias | 0.003 | Process noise — gyro bias drift rate |
| R_measure | 0.03 | Measurement noise — sensor noise level |

---

## Control Logic

Four servos drive the fins AoA, paired in opposition so that a positive pitch correction deflects surfaces 1 & 3 one way and surfaces 2 & 4 the other — producing a net moment without inducing roll. The filtered angle is mapped directly to servo position:

```cpp
int servoPos = map(filteredAngle, -90, 90, 0, 180);
servo1.write(servoPos);       // paired
servo3.write(servoPos);
servo2.write(180 - servoPos); // opposing pair
servo4.write(180 - servoPos);
```

The system runs on an Arduino with an MPU6050 over I²C, targeting a loop rate of ~200 Hz — fast enough to respond to attitude deviations well within the rocket's dynamic time constants.

---

## Current Status

The Kalman filter and servo control logic are implemented and bench-tested. Next steps are:

- SolidWorks design of the canard mount and actuator housing
- Hardware integration and closed-loop bench testing with a gimbal rig
- CFD analysis of canard aerodynamic effectiveness across the flight Mach range
- Flight test on QPL's next rocket

Images and CAD renders will be added as the hardware build progresses.

---

**Tools:** Arduino · C++ · MPU6050 IMU · SolidWorks (in progress) · ANSYS Fluent (planned)

