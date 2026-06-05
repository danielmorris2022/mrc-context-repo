# RoboBoat ASV System

**Purpose:** Overview of the RoboBoat competition ASV — the newest autonomous surface vehicle platform at FAU Marine Robotics Club.
**Last Updated:** 2025-07
**Source Repos:** [roboboat_usv](https://github.com/Marine-Robotics-Club/roboboat_usv), [roboboat_station](https://github.com/Marine-Robotics-Club/roboboat_station), [roboboat_ws](https://github.com/Marine-Robotics-Club/roboboat_ws)
**Confidence:** MEDIUM — derived from repo structure and package names; source code not deeply inspected.

---

## Overview

The RoboBoat ASV (`roboboat_usv`) is the active competition platform being developed for RoboBoat 2026. It is a separate, more modular codebase from the WAM-V systems, using ASV-namespaced ROS 2 packages. It incorporates:
- **APF (Artificial Potential Field) controller** for obstacle avoidance
- **PID heading-speed (pid_hs) controller**
- **Acoustic audio system** (`asv_audio`)
- **Perception pipeline** (`asv_perception`)
- **Mission planner** (`asv_planner`)
- **Python-based serial bridge** (`ros2serial_py`)

---

## Repositories

| Repo | Contents | Status | URL |
|------|----------|--------|-----|
| `roboboat_usv` | Main ASV ROS 2 workspace | Active (updated Apr 2025) | [Link](https://github.com/Marine-Robotics-Club/roboboat_usv) (Private) |
| `roboboat_station` | Base station / ground control | Active (updated Feb 2025) | [Link](https://github.com/Marine-Robotics-Club/roboboat_station) (Private) |
| `roboboat_ws` | Base station sensor viewer | Active (updated Feb 2025) | [Link](https://github.com/Marine-Robotics-Club/roboboat_ws) (Private) |

---

## ROS 2 Packages

| Package | Type | Purpose |
|---------|------|---------|
| `asv_allocation` | UNKNOWN | Task/resource allocation |
| `asv_audio` | UNKNOWN | Acoustic system (hydrophones / speakers for RoboBoat tasks) |
| `asv_control` | C++ | Vehicle control; contains `apf_grid_controller` and `pid_hs` sub-packages |
| `asv_launch` | Python | Launch files for the full system |
| `asv_perception` | UNKNOWN | Sensor perception pipeline |
| `asv_planner` | UNKNOWN | Mission / path planner |
| `asv_state` | UNKNOWN | Vehicle state estimation |
| `fau_msgs` | C++ | Custom FAU message types |
| `rl_msgs` | UNKNOWN | Reinforcement learning messages |
| `ros2serial_py` | Python | Python serial bridge to embedded hardware |

---

## Sub-packages in `asv_control`

| Sub-package | Purpose |
|------------|----------|
| `apf_grid_controller` | Artificial Potential Field (APF) grid-based obstacle avoidance controller |
| `pid_hs` | PID heading and speed controller |

---

## Notable Branches / Snapshots

| Branch/Folder | Description |
|--------------|-------------|
| `RB26` | RoboBoat 2026 snapshot (4 months ago) |
| `main` | Active development branch (APF controller added 2 months ago) |

---

## Relationship to WAM-V Systems

The `roboboat_usv` is a newer, cleaner re-architecture compared to the `LowLevel_Wamv1/2` repos. Key architectural differences:

| Aspect | WAM-V 1/2 | RoboBoat USV |
|--------|-----------|-------------|
| Package naming | `fau`, `fau2` | `asv_*` |
| Serial bridge | C++ `ros2serial` | Python `ros2serial_py` |
| Controllers | `path_planner2`, NSGA-2 | `apf_grid_controller`, `pid_hs` |
| Acoustic system | NOT PRESENT | `asv_audio` |
| Base station | NOT FOUND | `roboboat_station`, `roboboat_ws` |

---

## Hardware (INFERRED)

> No hardware-specific firmware found in this repo. The `ros2serial_py` bridge suggests a Python-compatible single-board computer (e.g., Raspberry Pi or Jetson) talking to an embedded microcontroller.

- Companion computer: UNKNOWN (RPi or Jetson)
- Thruster system: UNKNOWN
- Sensors: UNKNOWN (perception pipeline in `asv_perception`)
- Acoustic system: UNKNOWN hardware behind `asv_audio`

---

## Open Questions

- Hardware platform (vehicle hull, thruster model) UNKNOWN
- Whether `roboboat_usv` runs on a WAM-V hull or different vessel UNKNOWN
- IP addresses / network config UNKNOWN
- `roboboat_station` and `roboboat_ws` content NOT INSPECTED
- Relationship between `rl_msgs` and the RL planner from WAM-V 2 UNKNOWN
- Acoustic task (buoy?) implementation details UNKNOWN

---

## Related Files

- [roboboat_usv/src](https://github.com/Marine-Robotics-Club/roboboat_usv/tree/main/src)
- [asv_control/apf_grid_controller](https://github.com/Marine-Robotics-Club/roboboat_usv/tree/main/src/asv_control/apf_grid_controller)
- [asv_launch](https://github.com/Marine-Robotics-Club/roboboat_usv/tree/main/src/asv_launch)
- [../wamv2/README.md](../wamv2/README.md)
- [../wamv1/README.md](../wamv1/README.md)
