# WAM-V 1 Hardware

**Purpose:** Hardware component inventory for WAM-V 1 — differences from WAM-V 2 highlighted.
**Last Updated:** 2026-06-05
**Source Repos:** [LowLevel_Wamv1](https://github.com/Marine-Robotics-Club/LowLevel_Wamv1), firmware inspection
**Confidence:** MEDIUM-HIGH — inferred from firmware; no formal hardware schematic found

> See [WAM-V 2 Hardware](../wamv2/hardware.md) for the comparison baseline. This document focuses on WAM-V 1 specific additions and differences.

---

## Platform

| Property | Value |
|----------|-------|
| Platform | Wave Adaptive Modular Vessel (WAM-V) 16 |
| Control Board | WAMV-16 Main Control Board (same board as WAM-V 2) |
| Vessel Config | **Has bow thrusters** (WAM-V 1 exclusive) |
| Primary Role | Autonomous surface vehicle, RoboBoat/VRX competition, research platform |

---

## Compute Hardware

| Component | Model | Role | Confidence |
|-----------|-------|------|------------|
| Companion computer | NVIDIA Jetson (model UNKNOWN) | ROS 2 Humble host | INFERRED |
| Microcontroller | Teensy 4.1 | PCU firmware | HIGH |

---

## Propulsion (vs WAM-V 2)

| Component | WAM-V 1 | WAM-V 2 |
|-----------|---------|----------|
| Port/Stbd main thrusters | Torqeedo | Torqeedo |
| Azimuth servos | YES | YES |
| Motor controllers | RoboteQ MDC1460 x2 | RoboteQ MDC1460 x2 |
| **Bow thrusters** | **YES — port + stbd** | **NO** |
| Torqeedo PCU | YES | YES |

### Bow Thruster Control Pins

| Pin | Purpose |
|-----|---------|
| 10 (`CT_PW_CH5`) | Port bow thruster |
| 11 (`CT_PW_CH6`) | Spare / alternate |
| 12 (`CT_PORT_BOW_THRUST`) | Port bow thruster direct |
| 13 (`CT_STBD_BOW_THRUST`) | Starboard bow thruster direct |

---

## Sensors

| Sensor | Model | Status vs WAM-V 2 |
|--------|-------|-------------------|
| Weather station | AIRMAR 110WX | Same (Serial2) |
| Flow sensor | AIRMAR DT800 | Same (Serial5) |
| Depth/sonar | BlueRobotics PING Sonar | Same (Serial8) |
| LIDAR | UNKNOWN | INFERRED — same as WAM-V 2 |
| GPS/INS | UNKNOWN | INFERRED — same as WAM-V 2 |
| Cameras | UNKNOWN | INFERRED — same as WAM-V 2 |

---

## Power System

Identical to WAM-V 2. See [WAM-V 2 Hardware — Power System](../wamv2/hardware.md#power-system).

---

## Communications

Identical to WAM-V 2. See [WAM-V 2 Hardware — Communications](../wamv2/hardware.md#communications).

---

## Hardware vs WAM-V 2 Summary

| Feature | WAM-V 1 | WAM-V 2 |
|---------|---------|----------|
| Bow thrusters | YES | NO |
| Weather station | YES | YES |
| Depth sonar | YES | YES |
| LIDAR | INFERRED YES | INFERRED YES |
| RF Modem | YES (XT09-SI) | YES (XT09-SI) |
| Sub_System_ROS2 | Present (UNKNOWN purpose) | Not present |
| Repo last update | ~2 years ago | ~1-2 years ago |

---

## Open Questions

| ID | Question | Priority |
|----|----------|----------|
| WV1-2 | What bow thruster model is installed? | MEDIUM |
| WV1-3 | Is WAM-V 1 still actively used or retired? | MEDIUM |
| WV1-4 | What is in Sub_System_ROS2? | LOW |

---

## Related Files

- [WAM-V 1 Firmware](firmware.md)
- [WAM-V 1 ROS Interface](ros_interface.md)
- [WAM-V 1 Operations](operations.md)
- [WAM-V 2 Hardware](../wamv2/hardware.md)
