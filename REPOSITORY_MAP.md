# Repository Map

**Purpose:** Complete catalogue of all Marine-Robotics-Club GitHub repositories, their purpose, status, and related systems.
**Last Updated:** 2025-07
**Source:** [Marine-Robotics-Club GitHub Organization](https://github.com/Marine-Robotics-Club)
**Confidence:** HIGH — enumerated directly from the GitHub organization page (38 total repos).

---

## Active / Primary Repositories

| Repository | Visibility | Language | System | Purpose | Last Updated |
|------------|-----------|---------|--------|---------|-------------|
| [Thomas_Towfish](https://github.com/Marine-Robotics-Club/Thomas_Towfish) | Private | Python | TowFish | Teensy firmware + ROS 2 for towed magnetometry system | Apr 2025 |
| [LowLevel_Wamv2](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2) | Private | Jupyter/C++ | WAM-V 2 | Teensy PCU firmware + full ROS 2 stack (NSGA-2, RL, path planner) | Apr 2025 |
| [roboboat_usv](https://github.com/Marine-Robotics-Club/roboboat_usv) | Private | C++/Python | RoboBoat | RoboBoat 2026 ASV (APF controller, perception, audio) | Apr 2025 |
| [LowLevel_Wamv1](https://github.com/Marine-Robotics-Club/LowLevel_Wamv1) | Private | C++/Makefile | WAM-V 1 | Teensy PCU firmware + ROS 2 (bow thrusters, weather station) | Oct 2024 |
| [Vision](https://github.com/Marine-Robotics-Club/Vision) | Private | Makefile | WAM-V 1 | VLP-16 LiDAR and vision processing | Mar 2025 |
| [highlevel](https://github.com/Marine-Robotics-Club/highlevel) | Private | — | WAM-V (1 or 2) | ROS 2 for Jetson Orin AGX on RobotX WAM-V | Apr 2024 |
| [Mission_Control](https://github.com/Marine-Robotics-Club/Mission_Control) | Private | C++ | WAM-V (1 or 2) | Low-level controllers, Torqeedo thrusters, path planning | Dec 2023 |
| [roboboat_station](https://github.com/Marine-Robotics-Club/roboboat_station) | Private | Python | RoboBoat | Base station / ground control for RoboBoat | Feb 2025 |
| [roboboat_ws](https://github.com/Marine-Robotics-Club/roboboat_ws) | Private | — | RoboBoat | Base station sensor data viewer | Feb 2025 |
| [wamv_nav](https://github.com/Marine-Robotics-Club/wamv_nav) | UNKNOWN | UNKNOWN | WAM-V | Navigation / autonomy (not confirmed accessible) | UNKNOWN |
| [asv-system-docs](https://github.com/Marine-Robotics-Club/asv-system-docs) | UNKNOWN | Markdown | All | Documentation for ASV systems | UNKNOWN |

---

## Infrastructure / Tooling Repositories

| Repository | Visibility | Purpose |
|------------|-----------|--------|
| [ros2_humble_install](https://github.com/Marine-Robotics-Club/ros2_humble_install) | Public | Shell scripts for installing ROS 2 Humble |
| [ros2_humble_velodyne_install](https://github.com/Marine-Robotics-Club/ros2_humble_velodyne_install) | Public | Shell scripts for Velodyne LiDAR driver |
| [shell_scripts](https://github.com/Marine-Robotics-Club/shell_scripts) | UNKNOWN | System setup shell scripts |
| [GitPractice](https://github.com/Marine-Robotics-Club/GitPractice) | Public | Git training / practice repository |
| [wamv_documentation](https://github.com/Marine-Robotics-Club/wamv_documentation) | Public | WAM-V documentation (points to wiki, largely empty) |

---

## Archived / Legacy Repositories

| Repository | Visibility | Purpose | Notes |
|------------|-----------|---------|-------|
| [wamv](https://github.com/Marine-Robotics-Club/wamv) | Private | C++ WAM-V code | 12 commits, Nov 2022. Likely early codebase. |
| [wamv_archive](https://github.com/Marine-Robotics-Club/wamv_archive) | Private | C++ WAM-V archive | Sep 2022. Archived predecessor. |

---

## Total Repository Count

38 repositories total in the organization (as of 2025-07). The following have not been individually inspected or categorized:

- Remaining ~20 repositories are not individually named above. They should be enumerated by visiting the [org repositories page](https://github.com/orgs/Marine-Robotics-Club/repositories).

---

## Repository to System Mapping

| System | Primary Repos | Supporting Repos |
|--------|--------------|------------------|
| **TowFish** | `Thomas_Towfish` | — |
| **WAM-V 1** | `LowLevel_Wamv1` | `Vision`, `highlevel`, `Mission_Control`, `wamv` |
| **WAM-V 2** | `LowLevel_Wamv2` | `wamv_nav`, `highlevel` |
| **RoboBoat** | `roboboat_usv` | `roboboat_station`, `roboboat_ws` |
| **Infrastructure** | `ros2_humble_install`, `shell_scripts` | `GitPractice` |

---

## Key Package Name Cross-Reference

| Package | Repo | System |
|---------|------|--------|
| `RASP_V2.ino` (Teensy firmware) | `Thomas_Towfish` | TowFish |
| `twinleaf` | `Thomas_Towfish` | TowFish |
| `vehicle_state` | `Thomas_Towfish` | TowFish |
| `fau_gui` | `Thomas_Towfish` | TowFish |
| `WAMV_PCU_ROS2.ino` (Teensy firmware) | `LowLevel_Wamv1`, `LowLevel_Wamv2` | WAM-V 1 & 2 |
| `vehicle_state` / `vehicle_state2` | `LowLevel_Wamv1/2` | WAM-V 1/2 |
| `fau` / `fau2` | `LowLevel_Wamv1/2` | WAM-V 1/2 |
| `path_planner2` | `LowLevel_Wamv2` | WAM-V 2 |
| `ros2serial` | `LowLevel_Wamv1/2` | WAM-V 1/2 |
| `asv_control`, `asv_launch`, etc. | `roboboat_usv` | RoboBoat |
| `ros2serial_py` | `roboboat_usv` | RoboBoat |

---

## Related Files

- [SYSTEM_MAP.md](./SYSTEM_MAP.md)
- [INDEX.md](./INDEX.md)
- [systems/towfish/README.md](./systems/towfish/README.md)
- [systems/wamv1/README.md](./systems/wamv1/README.md)
- [systems/wamv2/README.md](./systems/wamv2/README.md)
- [systems/roboboat/README.md](./systems/roboboat/README.md)
