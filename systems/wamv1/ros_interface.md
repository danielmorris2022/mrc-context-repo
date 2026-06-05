# WAM-V 1 ROS Interface

**Purpose:** ROS 2 interface documentation for WAM-V 1 — differences from WAM-V 2 highlighted.
**Last Updated:** 2026-06-05
**Source Repo:** [LowLevel_Wamv1](https://github.com/Marine-Robotics-Club/LowLevel_Wamv1)
**Confidence:** HIGH — derived from package structure and firmware

> WAM-V 1 uses the same base ROS 2 architecture as WAM-V 2 but with fewer packages. See [WAM-V 2 ROS Interface](../wamv2/ros_interface.md) for the full reference. This document covers WAM-V 1 specific differences.

---

## ROS Environment

| Property | Value |
|----------|-------|
| ROS Version | ROS 2 Humble (INFERRED — same as WAM-V 2) |
| OS | Ubuntu 22.04 (INFERRED) |
| Workspace | COLCON workspace (INFERRED) |

---

## Packages (vs WAM-V 2)

| Package | WAM-V 1 | WAM-V 2 | Notes |
|---------|---------|---------|-------|
| `fau` | YES | YES | Main nav/mission/propulsion |
| `fau_msgs` | YES | YES | Custom messages |
| `ros2serial` | YES | YES | Teensy bridge |
| `teensyRos2` | YES | YES | PCU firmware |
| `vehicle_state` | YES | YES | GPS state estimation |
| `vision_py` | YES | YES | Vision processing |
| `path_planner2` | **NO** | YES | Path planning not in WV1 |
| `nsga2` | **NO** | YES | Multi-objective optimizer not in WV1 |
| `rl_py` / `rl_msgs` | **NO** | YES | RL controller not in WV1 |
| `lidar_msgs` | **NO** | YES | LIDAR msgs not in WV1 |
| `yolov9_msgs` | **NO** | YES | YOLOv9 not in WV1 |
| `Sub_System_ROS2` | **YES** | NO | Purpose UNKNOWN — unique to WV1 |

### Source Paths

| Package | Path |
|---------|------|
| `fau` | [`src/fau/`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv1/tree/main/src/fau) |
| `fau_msgs` | [`src/fau_msgs/`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv1/tree/main/src/fau_msgs) |
| `ros2serial` | [`src/ros2serial/`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv1/tree/main/src/ros2serial) |
| `teensyRos2` | [`src/teensyRos2/`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv1/tree/main/src/teensyRos2) |
| `vehicle_state` | [`src/vehicle_state/`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv1/tree/main/src/vehicle_state) |
| `vision_py` | [`src/vision_py/`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv1/tree/main/src/vision_py) |

---

## Launch Files

Launch files are UNKNOWN for WAM-V 1 — not inspected in detail.
Expected location: `src/fau/launch/` (INFERRED by analogy with WAM-V 2).

> **Open Question WV1-5:** What launch file is used for WAM-V 1 field operations?

---

## Nodes (INFERRED from WAM-V 2 equivalents)

| Node | Package | Notes |
|------|---------|-------|
| `mission_control` | `fau` | Same as WAM-V 2 |
| `navigation_array` | `fau` | Same as WAM-V 2 |
| `propulsion_system_bow` | `fau` | Bow thruster support included in WV1 |
| `vehicle_state` | `vehicle_state` | Same as WAM-V 2 |

> **No `path_planner2` node** in WAM-V 1.

---

## Custom Messages

WAM-V 1 uses the same `fau_msgs` package as WAM-V 2 but may have an older version. See [WAM-V 2 ROS Interface — Custom Messages](../wamv2/ros_interface.md#custom-message-types-fau_msgs) for full list.

Note: `fau_msgs/package.xml` commit message says "Topics changed with wamv2" — suggesting message types were updated when WAM-V 2 was introduced.

---

## Open Questions

| ID | Question | Priority |
|----|----------|----------|
| WV1-5 | What launch file is used for WAM-V 1 field ops? | HIGH |
| WV1-6 | What is Sub_System_ROS2? | MEDIUM |
| WV1-7 | Are topic names/types different from WAM-V 2? | MEDIUM |

---

## Related Files

- [WAM-V 1 Firmware](firmware.md)
- [WAM-V 1 Hardware](hardware.md)
- [WAM-V 1 Operations](operations.md)
- [WAM-V 2 ROS Interface](../wamv2/ros_interface.md) — primary ROS reference
