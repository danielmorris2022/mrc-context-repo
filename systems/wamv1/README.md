# WAM-V 1 System

**Purpose:** Overview of the WAM-V 1 autonomous surface vehicle — hardware, software, and key differences from WAM-V 2.
**Last Updated:** 2025-07
**Source Repos:** [LowLevel_Wamv1](https://github.com/Marine-Robotics-Club/LowLevel_Wamv1)
**Confidence:** MEDIUM-HIGH — derived from firmware source code. No dedicated hardware schematic found.

---

## Overview

WAM-V 1 is the first-generation WAM-V at FAU Marine Robotics Club. It is architecturally similar to WAM-V 2 but has key hardware additions: **bow thrusters** (for stationkeeping) and a **weather station** (AIRMAR 110WX + DT800). It runs the same ROS 2 Humble / Teensy 4.1 PCU stack as WAM-V 2, adapted for WAMV-16 Main Control Board 200-405.

**Key roles:**
- Autonomous surface vehicle for RoboBoat / VRX competition
- Research platform with weather station capability
- Precursor development platform for WAM-V 2 features

---

## Repositories

| Repo | Contents | URL |
|------|----------|-----|
| `LowLevel_Wamv1` | Teensy PCU firmware, ROS 2 packages | [Link](https://github.com/Marine-Robotics-Club/LowLevel_Wamv1) |
| `Vision` | VLP-16 LiDAR + vision processing | [Link](https://github.com/Marine-Robotics-Club/Vision) (Private) |
| `highlevel` | ROS 2 on Jetson Orin AGX | [Link](https://github.com/Marine-Robotics-Club/highlevel) (Private) |
| `Mission_Control` | Low-level controllers, Torqeedo, path planning | [Link](https://github.com/Marine-Robotics-Club/Mission_Control) (Private) |
| `wamv` | C++ WAM-V code | [Link](https://github.com/Marine-Robotics-Club/wamv) (Private) |

---

## Key Differences from WAM-V 2

| Feature | WAM-V 1 | WAM-V 2 |
|---------|---------|---------|
| Bow Thrusters | YES — Port/Stbd bow thrusters | NO |
| Weather Station | YES — AIRMAR 110WX (wind, temp, pressure) + DT800 (flow) | NO |
| Motor Controllers | Port MC (Serial3), Stbd MC (Serial4) — separate RoboteQ serial | UNKNOWN |
| RF Modem | Digi RF Modem (Serial6) | UNKNOWN |
| Path Planner | `fau` package (vs `fau2` + `path_planner2` + NSGA-2 on WAM-V 2) | NSGA-2, RL |
| Vision | VLP-16 LiDAR (per `Vision` repo description) | UNKNOWN LiDAR |
| High-Level Compute | Jetson Orin AGX (per `highlevel` repo description) | UNKNOWN |

---

## Hardware Components

| Component | Part / Model | Role | Interface |
|-----------|-------------|------|----------|
| Main PCU MCU | Teensy 4.1 | Power control, thruster/servo commands, sensor I/O | USB serial to Jetson |
| Propulsion | Torqeedo (model UNKNOWN) | Twin azimuthing electric thrusters | PCU via `Serial1` |
| Bow Thrusters | UNKNOWN | Port + Starboard bow thrusters | PWM out pins 10/12 |
| Motor Controllers | RoboteQ (Port + Stbd) | Drive motors | Serial3, Serial4 |
| Weather Station | AIRMAR 110WX | Wind, temperature, pressure (NMEA) | Serial2 |
| Flow Sensor | AIRMAR DT800 | Water flow / speed through water | Serial5 (receive only) |
| RC Control | Futaba SBUS receiver | Manual override RC | Serial7 (SBUS) |
| Depth Sensor | Blue Robotics Ping Sonar | Depth | Serial8 |
| RF Modem | Digi RF Modem | Wireless comms | Serial6 |
| Companion Computer | Jetson Orin AGX | ROS 2 high-level compute | USB serial |
| LIDAR | Velodyne VLP-16 | 3D obstacle detection | ROS (per `Vision` repo) |
| Batteries | 12V + 24V + Main | Power distribution | Analog A4, A5, A13 |
| Load Cells | UNKNOWN x2 | Force measurement | Analog A8, A9 |
| Temperature Sensor | UNKNOWN | Enclosure temp | Analog A12 |

---

## Teensy PCU Pin Map (WAMV1 WAMV_PCU_ROS2)

**Source:** [WAMV_PCU_ROS2.ino](https://github.com/Marine-Robotics-Club/LowLevel_Wamv1/blob/main/src/teensyRos2/WAMV_PCU_ROS2/WAMV_PCU_ROS2.ino)

| Pin | Define | Direction | Function |
|-----|--------|-----------|----------|
| 2 | `CT_PORT_THRUST` | OUT | Port thruster R/C PWM |
| 3 | `CT_STBD_THRUST` | OUT | Starboard thruster R/C PWM |
| 4 | `CT_PORT_AZIMUTH` | OUT | Port azimuth servo R/C PWM |
| 5 | `CT_STBD_AZIMUTH` | OUT | Starboard azimuth servo R/C PWM |
| 6 | `RC_ACTIVE_N` | IN | Manual / Autonomous mode select |
| 9 | `RC_KILL_ACTIVE_N` | IN | RC kill switch |
| 10 | `CT_PW_CH5` | OUT | Port Bow Thruster (dual use) |
| 12 | `CT_PORT_BOW_THRUST` | OUT | Port bow thruster R/C PWM |
| 13 | `CT_STBD_BOW_THRUST` | OUT | Starboard bow thruster R/C PWM (spare) |
| 31 | `ANCHOR_LIGHT` | OUT | Anchor light |
| 32 | `NAV_LIGHTS` | OUT | Navigation lights |
| 33 | `LIGHT_TOWER_YEL` | OUT | Light tower yellow |
| 36 | `LIGHT_TOWER_GRN` | OUT | Light tower green |
| 37 | `LIGHT_TOWER_RED` | OUT | Light tower red |
| 38 | `PROPULSION_MC_EN` | OUT | Enable RoboteQ motor controller |
| 39 | `V24_EN` | OUT | Enable 24V rail |
| 40 | `V12_EN` | OUT | Enable 12V rail |
| 41 | `CPU_PWR_EN` | OUT | Enable CPU power |
| A4 | `V12_SENSE` | IN (ADC) | 12V rail sense |
| A5 | `V24_SENSE` | IN (ADC) | 24V rail sense |
| A8 | `LOADCELL1` | IN (ADC) | Load cell 1 |
| A9 | `LOADCELL2` | IN (ADC) | Load cell 2 |
| A12 | `TEMPERATURE_SENSOR` | IN (ADC) | Temperature |
| A13 | `MAIN_BATTERY_SENSE` | IN (ADC) | Main battery voltage |

---

## Serial Interfaces (Teensy)

| Serial Port | Define | Target | Protocol |
|-------------|--------|--------|----------|
| Serial (USB) | `Jetson` | Jetson companion | Custom `<...>` packet format |
| Serial1 | `PCU` | Torqeedo PCU | Torqeedo proprietary |
| Serial2 | `WS` | AIRMAR 110WX weather station | NMEA (WIMDA, WIMWV, YXXDR) |
| Serial3 | `PORT_MC` | Port motor controller | RoboteQ serial |
| Serial4 | `STBD_MC` | Starboard motor controller | RoboteQ serial |
| Serial5 | `FLOW` | AIRMAR DT800 flow sensor | Receive only |
| Serial6 | `RF_MODEM` | Digi RF modem | RF serial |
| Serial7 | (SBUS) | Futaba SBUS receiver | SBUS (Bolder Flight v8.1.2) |
| Serial8 | `DEPTH` | Blue Robotics Ping Sonar | ping1d |

---

## Weather Station Data (AIRMAR 110WX)

Parsed NMEA sentence types:

| NMEA Sentence | Data |
|--------------|------|
| `$WIMDA` | Atmospheric pressure, air temperature |
| `$WIMWV` | Wind speed and angle (relative and true) |
| `$YXXDR` | Transducer data (temperature, humidity) |

---

## ROS 2 Packages

| Package | Purpose |
|---------|---------|
| `fau` | Core vehicle control / autonomy |
| `fau_msgs` | Custom message types |
| `ros2serial` | Serial bridge (Jetson to Teensy PCU) |
| `vehicle_state` | GPS + IMU to NED pose |
| `vision_py` | Computer vision |

---

## Open Questions

- Jetson Orin AGX IP address NOT FOUND
- RoboteQ model for Port/Stbd motor controllers UNKNOWN
- Full AIRMAR 110WX wiring harness UNKNOWN
- Whether `highlevel` and `Mission_Control` repos are active on WAM-V 1 or WAM-V 2 UNKNOWN
- VLP-16 LiDAR mounting position and ROS topic names UNKNOWN (see `Vision` repo)

---

## Related Files

- [WAMV_PCU_ROS2.ino](https://github.com/Marine-Robotics-Club/LowLevel_Wamv1/blob/main/src/teensyRos2/WAMV_PCU_ROS2/WAMV_PCU_ROS2.ino)
- [LowLevel_Wamv1 repo](https://github.com/Marine-Robotics-Club/LowLevel_Wamv1)
- [../wamv2/README.md](../wamv2/README.md) — WAM-V 2 for comparison
