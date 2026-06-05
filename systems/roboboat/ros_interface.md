# RoboBoat ROS Interface

**Purpose:** Complete ROS 2 interface documentation for RoboBoat 2026 — packages, nodes, topics, launch files, and message types.
**Last Updated:** 2026-06-05
**Source Repo:** [roboboat_usv](https://github.com/Marine-Robotics-Club/roboboat_usv)
**Confidence:** HIGH — derived from launch files and package structure; individual node topics INFERRED

---

## ROS Environment

| Property | Value |
|----------|-------|
| ROS Version | ROS 2 Humble (INFERRED) |
| OS | Ubuntu 22.04 (INFERRED) |
| Architecture | ASV-namespaced packages (more modular than WAM-V) |

---

## Packages

| Package | Language | Purpose | Source Path |
|---------|----------|---------|-------------|
| `RB26` | C++ (Arduino) | Teensy 4.1 firmware — GPS, IMU, thrusters, pump | [`src/RB26/`](https://github.com/Marine-Robotics-Club/roboboat_usv/tree/main/src/RB26) |
| `asv_allocation` | — | Thrust allocation (differential drive) | [`src/asv_allocation/`](https://github.com/Marine-Robotics-Club/roboboat_usv/tree/main/src/asv_allocation) |
| `asv_audio` | — | Audio system (tones, alarms) | [`src/asv_audio/`](https://github.com/Marine-Robotics-Club/roboboat_usv/tree/main/src/asv_audio) |
| `asv_control` | — | PID heading-speed (pid_hs) controller | [`src/asv_control/`](https://github.com/Marine-Robotics-Club/roboboat_usv/tree/main/src/asv_control) |
| `asv_launch` | — | Launch files for all configurations | [`src/asv_launch/`](https://github.com/Marine-Robotics-Club/roboboat_usv/tree/main/src/asv_launch) |
| `asv_perception` | — | Vision and obstacle detection | [`src/asv_perception/`](https://github.com/Marine-Robotics-Club/roboboat_usv/tree/main/src/asv_perception) |
| `asv_planner` | — | Mission planner and task planner | [`src/asv_planner/`](https://github.com/Marine-Robotics-Club/roboboat_usv/tree/main/src/asv_planner) |
| `asv_state` | — | Vehicle state estimation (GPS + IMU) | [`src/asv_state/`](https://github.com/Marine-Robotics-Club/roboboat_usv/tree/main/src/asv_state) |
| `fau_msgs` | — | Shared custom messages (same as WAM-V) | [`src/fau_msgs/`](https://github.com/Marine-Robotics-Club/roboboat_usv/tree/main/src/fau_msgs) |
| `rl_msgs` | — | RL messages | [`src/rl_msgs/`](https://github.com/Marine-Robotics-Club/roboboat_usv/tree/main/src/rl_msgs) |
| `ros2serial_py` | Python | Python serial bridge to Teensy | [`src/ros2serial_py/`](https://github.com/Marine-Robotics-Club/roboboat_usv/tree/main/src/ros2serial_py) |

> **Note:** RoboBoat uses `ros2serial_py` (Python) vs WAM-V's `ros2serial` (C++). Also uses `asv_*` package naming convention vs WAM-V's `fau` package.

---

## Launch Files

All launch files in [`src/asv_launch/launch/`](https://github.com/Marine-Robotics-Club/roboboat_usv/tree/main/src/asv_launch/launch):

| File | Purpose |
|------|---------|
| `roboboat.launch.py` | **Main field launch** |
| `state.launch.py` | Vehicle state only |
| `control.launch.py` | Controller only |
| `allocation.launch.py` | Thrust allocation only |
| `planner.launch.py` | Mission planner only |
| `perception.launch.py` | Perception only |
| `sim.launch.py` | Simulation launch |

---

## Nodes (from roboboat.launch.py)

| Node | Package | Executable | Key Parameters |
|------|---------|-----------|----------------|
| `pid_hs_node` | `pid_hs` | `pid_hs_node` | kp_xy=0.7, kp_psi=5.0, ki_xy=0.0, ki_psi=1.0, kd_xy=1.0, kd_psi=10.0, kp_v=1.0, kd_v=1.0, kp_psi_H=10.0 |
| `usv_allocation_diff` | `asv_allocation` | `usv_allocation_diff` | `asv` (LaunchConfig) |
| `planner_node` | `asv_planner` | `planner_node` | use_sim_time=False, `mission_id` (LaunchConfig) |
| `mission_planner` | `asv_planner` | `mission_planner` | `mission_id` (LaunchConfig) |

> **Note:** `ros2serial_py` and `asv_state` nodes are INFERRED to be launched but not confirmed from the partial launch file inspection.

---

## PID Controller Parameters

From `roboboat.launch.py` pid_hs_node:

| Parameter | Value | Purpose |
|-----------|-------|---------|
| `kp_xy` | 0.7 | Position XY proportional gain |
| `ki_xy` | 0.0 | Position XY integral gain |
| `kd_xy` | 1.0 | Position XY derivative gain |
| `kp_psi` | 5.0 | Heading proportional gain |
| `ki_psi` | 1.0 | Heading integral gain |
| `kd_psi` | 10.0 | Heading derivative gain |
| `kp_v` | 1.0 | Velocity proportional gain |
| `ki_v` | 0.0 | Velocity integral |
| `kd_v` | 1.0 | Velocity derivative |
| `kp_psi_H` | 10.0 | High-speed heading proportional |
| `kd_psi_H` | 2.0 | High-speed heading derivative |
| `ki_psi_H` | 0.1 | High-speed heading integral |

---

## Topics (INFERRED)

> All topic names below are INFERRED from package purpose and message types. Verify with `ros2 topic list` on vehicle.

| Topic (INFERRED) | Message Type | Publisher | Subscriber |
|------------------|-------------|-----------|------------|
| `/asv/state` | `fau_msgs/State` | `asv_state` | `pid_hs`, `planner_node` |
| `/asv/motor_cmds` | `fau_msgs/MotorCmds` | `usv_allocation_diff` | `ros2serial_py` |
| `/asv/mission` | `fau_msgs/TaskInfo` | `mission_planner` | `planner_node` |
| `/asv/audio` | UNKNOWN | UNKNOWN | `asv_audio` |
| `/asv/perception` | UNKNOWN | `asv_perception` | `planner_node` |

---

## ASV Configuration

The `roboboat.launch.py` uses a `asv` LaunchConfiguration parameter that selects the vehicle configuration. Value meaning is UNKNOWN — likely selects between different ASV hull/thruster configs.

---

## Open Questions

| ID | Question | Priority |
|----|----------|----------|
| RB-7 | What does the `asv` LaunchConfiguration parameter select? | HIGH |
| RB-8 | What mission_id values are supported? | HIGH |
| RB-9 | What are the actual ROS 2 topic names? | HIGH |
| RB-10 | Does ros2serial_py get launched in roboboat.launch.py? | MEDIUM |

---

## Related Files

- [RoboBoat Hardware](hardware.md)
- [RoboBoat Operations](operations.md)
- [RoboBoat Missions](missions.md)
- [WAM-V 2 ROS Interface](../wamv2/ros_interface.md) — older codebase comparison
