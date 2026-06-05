# WAM-V 2 Hardware

**Purpose:** Hardware component inventory for WAM-V 2 — compute, sensors, thrusters, power, and communication systems.
**Last Updated:** 2026-06-05
**Source Repos:** [LowLevel_Wamv2](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2), firmware inspection, README
**Confidence:** MEDIUM-HIGH — inferred from firmware serial port comments and code; no formal hardware schematic found

---

## Platform

| Property | Value |
|----------|-------|
| Platform | Wave Adaptive Modular Vessel (WAM-V) 16 |
| Control Board | WAMV-16 Main Control Board 200-405 |
| Vessel Config | No bow thrusters (WAM-V 2 specific; WAM-V 1 has bow thrusters) |
| Primary Role | Tow vehicle for TowFish; RoboBoat competition vessel |

---

## Compute Hardware

| Component | Model | Role | Connection | Confidence |
|-----------|-------|------|-----------|------------|
| Companion computer | NVIDIA Jetson (model UNKNOWN) | ROS 2 Humble host, navigation, vision | USB to Teensy, Ethernet UNKNOWN | INFERRED from code |
| Microcontroller | Teensy 4.1 | PCU firmware, sensor I/O, thruster control | USB serial to Jetson | HIGH |
| Vision computer | Separate vision system (Private repo) | Object detection, YOLOv9 | UNKNOWN | INFERRED from yolov9_msgs package |

---

## Propulsion

| Component | Model | Qty | Notes |
|-----------|-------|-----|-------|
| Main thrusters | Torqeedo electric thrusters | 2 | Port + Starboard; azimuth-steerable |
| Thruster controller | Torqeedo PCU | 1 | Connected via Serial1 |
| Azimuth servos | UNKNOWN model | 2 | RC PWM control, pins 4 & 5 |
| Motor controllers | RoboteQ MDC1460 | 2 | Port (Serial3) + Starboard (Serial4) |
| Bow thrusters | None | 0 | WAM-V 2 does NOT have bow thrusters (WAM-V 1 does) |

---

## Sensors

| Sensor | Model | Purpose | Interface | Confidence |
|--------|-------|---------|-----------|------------|
| Weather station | AIRMAR 110WX | Wind speed/direction, air temperature, barometric pressure | Serial2, NMEA-0183 | HIGH |
| Flow sensor | AIRMAR DT800 | Water speed, depth | Serial5, NMEA-0183 (RX only) | HIGH |
| Depth/sonar | BlueRobotics PING Sonar | Water depth | Serial8, Ping Protocol | HIGH |
| LIDAR | UNKNOWN (VLP-16 inferred from lidar_msgs package) | Obstacle detection, navigation | UNKNOWN ROS interface | INFERRED |
| Cameras | UNKNOWN | Vision/object detection | UNKNOWN | INFERRED from vision_py/yolov9 |
| IMU/GPS | UNKNOWN | Vehicle state estimation | UNKNOWN | INFERRED from vehicle_state2 package |

> **Note:** No GPS/INS hardware explicitly named in firmware. The `vehicle_state2` package requires `latRef`/`lonRef` parameters suggesting external GPS input. Hardware identity is UNKNOWN — open question INF-1.

---

## Power System

| Rail | Control Pin | Sense Pin | Notes |
|------|------------|-----------|-------|
| Main battery | — | A13 (`MAIN_BATTERY_SENSE`) | Battery voltage monitoring |
| 24V rail | 39 (`V24_EN`) | A5 (`V24_SENSE`) | Propulsion power |
| 12V rail | 40 (`V12_EN`) | A4 (`V12_SENSE`) | Electronics power |
| CPU power | 41 (`CPU_PWR_EN`) | — | Jetson/computer power enable |
| Propulsion MC | 38 (`PROPULSION_MC_EN`) | — | Motor controller enable |

---

## Communications

| System | Hardware | Protocol | Purpose | Confidence |
|--------|----------|----------|---------|------------|
| RC Control | Bolder Flight SBUS receiver | SBUS | Manual override, kill switch | HIGH |
| RF Modem | Digi International XT09-SI | Serial (Serial6) | Remote RF comms | HIGH |
| Jetson↔Teensy | USB cable | Custom ASCII serial @ 9600 baud | Command + telemetry | HIGH |
| ROS DDS | UNKNOWN config | ROS 2 DDS (CycloneDDS or FastDDS) | Inter-node comms | UNKNOWN — open question INF-1 |
| TowFish tether | UNKNOWN | UNKNOWN | TowFish data/power | UNKNOWN — open question TF-9 |

---

## Light Tower

| Color | Pin | Use |
|-------|-----|-----|
| Red | 37 | Status/alert |
| Yellow | 33 | Status/caution |
| Green | 36 | Status/OK |
| White (Anchor) | 31 | Anchor light |
| Nav lights | 32 | Navigation lights |

> **Note:** ROS subscription for light tower not yet implemented in firmware (BUG-4 / open TODO in code).

---

## Hardware Gaps / Open Questions

| ID | Question | Priority |
|----|----------|----------|
| INF-1 | What ROS 2 DDS config connects WAM-V 2 to TowFish? | HIGH |
| INF-2 | What network configuration (IPs, router, WiFi) is used at FAU? | HIGH |
| WV2-1 | What is the exact Jetson model (Xavier NX, Orin, etc.)? | MEDIUM |
| WV2-2 | What GPS/INS hardware is installed? | MEDIUM |
| WV2-3 | Is there a hardware schematic or wiring diagram? | HIGH |

---

## Related Files

- [WAM-V 2 Firmware](firmware.md)
- [WAM-V 2 ROS Interface](ros_interface.md)
- [WAM-V 2 Operations](operations.md)
- [WAM-V 1 Hardware](../wamv1/hardware.md) — comparison (has bow thrusters + weather station)
