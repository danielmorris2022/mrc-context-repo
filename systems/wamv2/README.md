# WAM-V 2 System

**Purpose:** Overview of the WAM-V 2 autonomous surface vehicle — hardware, software packages, ROS 2 interfaces, and operational notes.
**Last Updated:** 2025-07
**Source Repos:** [LowLevel_Wamv2](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2), [wamv_nav](https://github.com/Marine-Robotics-Club/wamv_nav) (if accessible)
**Confidence:** MEDIUM-HIGH — derived from firmware and ROS source files. No dedicated hardware schematic found.

---

## Overview

WAM-V 2 is the primary autonomous surface vehicle (ASV) used by FAU Marine Robotics Club for competition (RoboBoat / VRX) and towing the TowFish for geomagnetic surveys. It is a Wave Adaptive Modular Vessel (WAM-V) platform equipped with Torqeedo electric thrusters, a Jetson companion computer, a Teensy 4.1 Power Control Unit (PCU), LIDAR, and cameras.

**Key roles:**
- Tow vehicle for TowFish geomagnetic survey system
- RoboBoat / VRX competition entry
- Autonomous navigation platform

---

## Repository

| Repo | Contents | URL |
|------|----------|-----|
| `LowLevel_Wamv2` | Teensy PCU firmware, ROS 2 packages, path planner, NSGA-2, RL | [Link](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2) |
| `wamv_nav` | Navigation / high-level autonomy (UNKNOWN — not inspected) | UNKNOWN |

---

## Hardware Components

| Component | Part / Model | Role | Interface |
|-----------|-------------|------|----------|
| Main PCU MCU | Teensy 4.1 | Power control, thruster/servo commands, sensor I/O | USB serial to Jetson |
| Propulsion | Torqeedo (model UNKNOWN) | Twin azimuthing electric thrusters | PCU via `Serial1` |
| RC Control | Futaba SBUS receiver | Manual override RC control | `Serial7` @ SBUS protocol |
| Depth Sensor | Blue Robotics Ping Sonar | Depth / obstacle detection | `Serial8` (ping1d library) |
| Companion Computer | Jetson (model UNKNOWN) | ROS 2 compute | USB serial (`Serial`) |
| LIDAR | UNKNOWN | Obstacle detection / mapping | ROS (package: `lidar_msgs`) |
| Camera / Vision | UNKNOWN | Object detection (YOLOv9) | ROS (package: `yolov9_msgs`) |
| Battery (12V) | UNKNOWN | System 12V power | Analog sense: A4 (`V12_SENSE`) |
| Battery (24V) | UNKNOWN | Thruster 24V power | Analog sense: A5 (`V24_SENSE`) |
| Main Battery | UNKNOWN | Primary battery | Analog sense: A13 (`MAIN_BATTERY_SENSE`) |
| Load Cell 1 | UNKNOWN | Force measurement | Analog: A8 (`LOADCELL1`) |
| Load Cell 2 | UNKNOWN | Force measurement | Analog: A9 (`LOADCELL2`) |
| Temperature Sensor | UNKNOWN | Enclosure temp | Analog: A12 (`TEMPERATURE_SENSOR`) |
| Motor Controller | RoboteQ (model UNKNOWN) | Motor drive enable | Pin 38 (`PROPULSION_MC_EN`) |
| Light Tower | Custom 3-color | Status indicator | Pins 33/36/37 (YEL/GRN/RED) |
| Nav/Anchor Lights | Custom | Maritime navigation lights | Pins 31/32 |

---

## Teensy PCU Pin Map (WAMV_PCU_ROS2)

**Source:** [WAMV_PCU_ROS2.ino](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2/blob/main/src/teensyRos2/WAMV_PCU_ROS2/WAMV_PCU_ROS2.ino)

| Pin | Define | Direction | Function |
|-----|--------|-----------|----------|
| 2 | `CT_PORT_THRUST` | OUT | Port thruster R/C PWM |
| 3 | `CT_STBD_THRUST` | OUT | Starboard thruster R/C PWM |
| 4 | `CT_PORT_AZIMUTH` | OUT | Port azimuth servo R/C PWM |
| 5 | `CT_STBD_AZIMUTH` | OUT | Starboard azimuth servo R/C PWM |
| 6 | `RC_ACTIVE_N` | IN | Manual (LOW) / Autonomous (HIGH) mode |
| 9 | `RC_KILL_ACTIVE_N` | IN | RC kill switch monitor |
| 10 | `CT_PW_CH5` | OUT | Spare R/C PWM (unused) |
| 11 | `CT_PW_CH6` | OUT | Spare R/C PWM (unused) |
| 12 | `CT_PW_CH7` | OUT | Spare R/C PWM (unused) |
| 13 | `CT_PW_CH8` | OUT | Spare R/C PWM (unused) |
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

| Serial Port | Target | Protocol | Baud |
|-------------|--------|----------|------|
| `Serial` (USB) | Jetson companion computer | Custom `<...>` packet format | UNKNOWN |
| `Serial1` | Torqeedo PCU | Torqeedo proprietary | UNKNOWN |
| `Serial7` | Futaba SBUS receiver | SBUS (Bolder Flight v8.1.2) | 100000 (SBUS standard) |
| `Serial8` | Blue Robotics Ping Sonar | ping1d protocol | UNKNOWN |

---

## ROS 2 Packages

| Package | Type | Purpose |
|---------|------|---------|
| `vehicle_state2` | C++ (ament_cmake) | GPS + IMU to NED pose conversion |
| `fau2` | C++ (ament_cmake) | Core vehicle control / autonomy |
| `fau_msgs` | C++ | Custom message types for FAU systems |
| `lidar_msgs` | C++ | Custom LIDAR message types |
| `yolov9_msgs` | C++ | Custom YOLOv9 detection message types |
| `rl_msgs` | C++ | Custom RL/planning message types |
| `path_planner2` | C++ | Path planning (NSGA-2 based, multi-objective) |
| `nsga2` | Jupyter / Python | NSGA-2 multi-objective optimization |
| `rl_py` | Python | Reinforcement learning module |
| `ros2serial` | C++ | Serial bridge between Jetson and Teensy PCU |
| `vision_py` | Python | Computer vision (YOLOv9 integration) |

---

## Control Modes

| Mode | Condition | Description |
|------|-----------|-------------|
| **Manual** | `RC_ACTIVE_N` LOW | SBUS RC controller directly drives thrusters and azimuths via Futaba channels |
| **Autonomous** | `RC_ACTIVE_N` HIGH | Jetson (ROS) sends commands via serial; Teensy translates to RC PW or PCU commands |
| **Kill** | `RC_KILL_ACTIVE_N` triggered | E-stop sent to Torqeedo PCU; all motion stopped |

---

## GPS Reference Coordinates

From `vehicle_state2/launch/vehicle_state.launch.py`:
- **Active:** lat `-33.7227654`, lon `150.6739916` — **VRX Sydney** competition venue
- This must be updated before FAU/Florida testing

---

## Known Issues / Open Questions

- Jetson model UNKNOWN (Jetson Nano / Xavier / Orin?)
- Torqeedo model UNKNOWN
- Blue Robotics Ping Sonar model (Ping1D vs Ping2D) UNKNOWN
- RoboteQ motor controller model UNKNOWN
- Whether `wamv_nav` repo is separate from `LowLevel_Wamv2` UNKNOWN
- Light tower add-on for ROS subscription: firmware has `// TO DO: Add ROS subscription for light tower control. Use 3 std_msgs/UInt16`
- IP addresses for Jetson and Teensy NOT FOUND
- Full Jetson-to-Teensy serial packet format NOT DOCUMENTED

---

## Related Files

- [WAMV_PCU_ROS2.ino](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2/blob/main/src/teensyRos2/WAMV_PCU_ROS2/WAMV_PCU_ROS2.ino)
- [vehicle_state.launch.py](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2/blob/main/src/vehicle_state2/launch/vehicle_state.launch.py)
- [LowLevel_Wamv2 repo root](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2)
- [../towfish/README.md](../towfish/README.md) — TowFish system towed by WAM-V 2
