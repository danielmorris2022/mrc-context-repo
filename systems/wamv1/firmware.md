# WAM-V 1 Firmware

**Purpose:** Teensy 4.1 PCU firmware documentation for WAM-V 1 — pin assignments, serial port mapping, bow thruster control, weather station parsing, and Jetson interface.
**Last Updated:** 2026-06-05
**Source Repo:** [LowLevel_Wamv1](https://github.com/Marine-Robotics-Club/LowLevel_Wamv1) — `src/teensyRos2/WAMV_PCU_ROS2/`
**Confidence:** HIGH — derived directly from firmware source code

---

## Firmware Files

| File | Path | Purpose |
|------|------|---------|
| `WAMV_PCU_ROS2.ino` | [`src/teensyRos2/WAMV_PCU_ROS2/WAMV_PCU_ROS2.ino`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv1/blob/main/src/teensyRos2/WAMV_PCU_ROS2/WAMV_PCU_ROS2.ino) | Main Arduino sketch for Teensy 4.1 PCU |
| `PCU.cpp` | `src/teensyRos2/WAMV_PCU_ROS2/PCU.cpp` | Torqeedo PCU control implementation |
| `PCU.h` | `src/teensyRos2/WAMV_PCU_ROS2/PCU.h` | Torqeedo PCU control header |
| `Light.h` | `src/teensyRos2/WAMV_PCU_ROS2/Light.h` | Light tower control |

> **Also:** `src/teensyRos2/Sub_System_ROS2/` — purpose UNKNOWN; not present in WAM-V 2.

---

## Hardware Target

- **MCU:** Teensy 4.1
- **Board:** WAMV-16 Main Control Board
- **Connection to Jetson:** USB serial (initialized via `Serial`)
- **Libraries:** Bolder Flight Systems SBUS v8.1.2, BlueRobotics ping1d v0.1.2, Servo.h

---

## Pin Definitions

### Main Thrusters / Servos (R/C PWM Output)

| Pin | Constant | Purpose |
|-----|----------|---------|
| 2 | `CT_PORT_THRUST` | Port main thruster |
| 3 | `CT_STBD_THRUST` | Starboard main thruster |
| 4 | `CT_PORT_AZIMUTH` | Port azimuth servo |
| 5 | `CT_STBD_AZIMUTH` | Starboard azimuth servo |

### Bow Thrusters (WAM-V 1 Exclusive — not in WAM-V 2)

| Pin | Constant | Purpose |
|-----|----------|---------|
| 10 | `CT_PW_CH5` | Port bow thruster (spare R/C PWM / WAMV1 CH5) |
| 11 | `CT_PW_CH6` | Spare R/C PWM |
| 12 | `CT_PORT_BOW_THRUST` | Port bow thruster direct |
| 13 | `CT_STBD_BOW_THRUST` | Starboard bow thruster direct |

### RC Control (Digital Input)

| Pin | Constant | Purpose |
|-----|----------|---------|
| 6 | `RC_ACTIVE_N` | LOW = MANUAL, HIGH = AUTONOMOUS |
| 9 | `RC_KILL_ACTIVE_N` | Kill switch |

### Power / Sensing

| Pin | Constant | Purpose |
|-----|----------|---------|
| A4 | (power sense) | 12V rail sense |
| A5 | (power sense) | 24V rail sense |
| 38 | `PROPULSION_MC_EN` | Propulsion motor controller enable |
| 39 | `V24_EN` | 24V rail enable |
| 40 | `V12_EN` | 12V rail enable |
| 41 | `CPU_PWR_EN` | CPU power enable |

### Light Tower (same as WAM-V 2)

| Pin | Constant | Color |
|-----|----------|-------|
| 33 | `LIGHT_TOWER_YEL` | Yellow |
| 36 | `LIGHT_TOWER_GRN` | Green |
| 37 | `LIGHT_TOWER_RED` | Red |

---

## Serial Port Mapping

| Port | Device | Protocol | Notes |
|------|--------|----------|-------|
| Serial1 | Torqeedo PCU | Proprietary | Motor controller |
| Serial2 | AIRMAR 110WX Weather Station | NMEA-0183 | Wind speed/dir, air temp, pressure |
| Serial3 | RoboteQ MDC1460 (Port motor) | RoboteQ ASCII | Port motor controller |
| Serial4 | RoboteQ MDC1460 (Stbd motor) | RoboteQ ASCII | Starboard motor controller |
| Serial5 | AIRMAR DT800 Flow Sensor | NMEA-0183 | Receive only |
| Serial6 | Digi International XT09-SI RF Modem | Serial | RF comms |
| Serial7 | SBUS RC Receiver | SBUS | R/C transmitter input |
| Serial8 | BlueRobotics PING Sonar | Ping Protocol | Depth sensor |
| USB (Serial) | NVIDIA Jetson | Custom ASCII | Command/telemetry |

> **Identical to WAM-V 2** serial port mapping.

---

## Weather Station Parsing

WAM-V 1 firmware parses NMEA sentences from AIRMAR 110WX:

| NMEA Sentence | Data Extracted |
|---------------|----------------|
| `$WIMDA` | Barometric pressure, air temperature |
| `$WIMWV` | Wind speed and direction |
| `$YXXDR` | Transducer data |

---

## Bow Thruster Control (WAM-V 1 Exclusive)

- Both ROS and SBUS commands handled via `Send_Bow_Thrusters_cmds()`
- Uses pins 10 (`CT_PW_CH5`) and 12 (`CT_PORT_BOW_THRUST`) for control
- **Transition safety:** When switching from KILLED→ACTIVE or MANUAL→AUTONOMOUS, bow thrusters are set to 1500 μs for 1 second before accepting new commands

---

## Key Differences from WAM-V 2

| Feature | WAM-V 1 | WAM-V 2 |
|---------|---------|----------|
| Bow thrusters | YES — 4 pins (10, 11, 12, 13) | NO |
| Sub_System_ROS2 | Present (purpose UNKNOWN) | Not present |
| Commit age | ~2 years old | ~1-2 years old |
| path_planner2 | Not present | Present |
| nsga2 | Not present | Present |
| rl_py | Not present | Present |

---

## Known Issues

| ID | Description | Status |
|----|-------------|--------|
| WV1-1 | `Sub_System_ROS2` purpose undocumented | Open |
| BUG-4 | Light tower ROS subscription not implemented (same TODO as WAM-V 2) | Open |

---

## Related Files

- [WAM-V 1 Hardware](hardware.md)
- [WAM-V 1 ROS Interface](ros_interface.md)
- [WAM-V 1 Operations](operations.md)
- [WAM-V 2 Firmware](../wamv2/firmware.md) — for comparison
