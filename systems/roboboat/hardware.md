# RoboBoat Hardware

**Purpose:** Hardware component inventory for RoboBoat 2026 ASV — Teensy PCU pinout, GPS, IMU, servos, thrusters, pump, power.
**Last Updated:** 2026-06-05
**Source Repo:** [roboboat_usv](https://github.com/Marine-Robotics-Club/roboboat_usv) — `src/RB26/`
**Confidence:** HIGH — pin definitions read directly from `RB26.h`; commit message "RoboBoat 2026 snapshot" (4 months ago)

---

## Platform

| Property | Value |
|----------|-------|
| Vehicle | Custom ASV — RoboBoat 2026 platform |
| Competition | RoboBoat (AUVSI / Robonation) |
| Firmware MCU | Teensy 4.1 (same MCU family as WAM-V 1/2) |
| Firmware Name | RB26 (RoboBoat 2026) |

---

## Compute Hardware

| Component | Model | Role | Connection | Confidence |
|-----------|-------|------|-----------|------------|
| Companion computer | NVIDIA Jetson (model UNKNOWN) | ROS 2 host, planning, perception, mission | USB serial to Teensy @ 9600 baud | INFERRED |
| Microcontroller | Teensy 4.1 | RB26 firmware, sensor I/O, thruster/servo control | `Serial` = Jetson USB | HIGH |

---

## Pin Definitions (from RB26.h)

### Thrusters / Propulsion

| Pin | Constant | Purpose |
|-----|----------|---------|
| 23 | `PORT_PW` | Port thruster PWM output |
| 3 | `STBD_PW` | Starboard thruster PWM output |
| 38 | `T500_EN` | T500 thruster enable |

**PWM Range:**
| Constant | Value | Meaning |
|----------|-------|--------|
| `PWM_MIN` | 1100 μs | Full reverse |
| `PWM_NEUTRAL` | 1500 μs | Stop |
| `PWM_MAX` | 1900 μs | Full forward |

### RC Input (SBUS Channels)

| Pin/Channel | Constant | Purpose |
|-------------|----------|---------|
| CH1 (pin 1) | `CH1` | Inverted PWM channel 1 |
| CH2 (pin 0) | `CH2` | Inverted PWM channel 2 |
| CH3 (pin 6) | `CH3` | Inverted PWM channel 3 (unused) |
| CH4 (pin 7) | `CH4` | Inverted PWM channel 4 |
| CH5 (pin 4) | `CH5` | Inverted PWM channel 5 |
| CH6 (pin 5) | `SBUS_CH6` | SBUS channel 6 |

### Lights / Status

| Pin | Constant | Color | Purpose |
|-----|----------|-------|---------|
| 30 | `LT_ORANGE` | Orange | Status light |
| 31 | `LT_GREEN` | Green | Status light |
| 27 | `LT_RED` | Red | Status light |

### Power / Sensing

| Pin | Constant | Purpose |
|-----|----------|---------|
| A3 | `BATT_V` | Battery voltage sense |
| 33 | `BATT_SENS_EN` | Battery sense enable |
| A12 | `TEMP` | Temperature sense |

### E-Stop / Safety

| Pin | Constant | Purpose |
|-----|----------|---------|
| A15 | `ESTOP_SENSE` | E-stop input sense |

### Misc

| Pin | Constant | Purpose |
|-----|----------|---------|
| 37 | `FAN_EN` | Cooling fan enable |

---

## Serial Port Mapping (from RB26.ino)

| Port | Device | Baud | Protocol | Confidence |
|------|--------|------|----------|------------|
| `Serial` (USB) | NVIDIA Jetson | 9600 | Custom ASCII (same as WAM-V) | HIGH |
| `Serial6` | Garmin GPS | 38400 | NMEA-0183 (GarminNMEA.h) | HIGH |
| `Serial3` | Pump controller | 9600 | Serial ASCII | HIGH |
| IMU | IMU on I2C or UART | UNKNOWN | Parsed via IMUReader.h | INFERRED |

> **Note:** GPS is a **Garmin device** — exact model UNKNOWN. Parsed via `GarminNMEA.cpp/.h` which handles Garmin-specific NMEA sentences. This is different from WAM-V 1/2 which use AIRMAR 110WX for weather.

---

## Sensors

| Sensor | Model | Purpose | Interface | Confidence |
|--------|-------|---------|-----------|------------|
| GPS | Garmin (model UNKNOWN) | Position | Serial6, 38400 baud, NMEA-0183 | HIGH |
| IMU | UNKNOWN (9-DOF inferred from accelX/Y/Z, gyroX/Y/Z, quatW/X/Y/Z) | Heading, orientation | I2C or UART (UNKNOWN) | INFERRED |
| Battery voltage | Analog divider | Battery monitoring | A3 | HIGH |
| Temperature | Analog sensor | Board temperature | A12 | HIGH |
| E-Stop | Analog/digital input | Emergency stop | A15 | HIGH |
| Cameras | UNKNOWN | Perception, buoy detection | UNKNOWN | INFERRED from asv_perception |

---

## Propulsion System

| Component | Model | Notes |
|-----------|-------|-------|
| Port thruster | BlueRobotics T500 (INFERRED from T500_EN) | PWM control via pin 23 |
| Stbd thruster | BlueRobotics T500 (INFERRED) | PWM control via pin 3 |
| ESC | UNKNOWN | T500_EN enables thruster ESC |
| Pump | UNKNOWN | Controlled via Serial3, 9600 baud |
| Servos | UNKNOWN | Initialized via RB26_servos_init() |

> **Note:** Pump system suggests a water jet or fire-fighting task capability (common in RoboBoat competition).

---

## Telemetry Period

- `TELEMETRY_PERIOD_MS = 100` — Teensy sends telemetry to Jetson every 100ms (10Hz)

---

## Hardware Gaps / Open Questions

| ID | Question | Priority |
|----|----------|----------|
| RB-1 | What Garmin GPS model is installed? | MEDIUM |
| RB-2 | What IMU is installed (model, interface)? | HIGH |
| RB-3 | What pump system is this (fire-fighting, ballast)? | MEDIUM |
| RB-4 | What servo positions/purposes exist? | MEDIUM |
| RB-5 | What is the hull design? Custom or off-shelf? | LOW |
| RB-6 | What ESC model is used with the T500 thrusters? | MEDIUM |

---

## Related Files

- [RoboBoat ROS Interface](ros_interface.md)
- [RoboBoat Operations](operations.md)
- [RoboBoat Missions](missions.md)
- [WAM-V 2 Hardware](../wamv2/hardware.md) — comparison (Torqeedo vs T500, different GPS)
