# WAM-V 2 ROS Interface

**Purpose:** Complete ROS 2 interface documentation for WAM-V 2 — packages, nodes, topics, custom messages, and launch files.
**Last Updated:** 2026-06-05
**Source Repo:** [LowLevel_Wamv2](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2)
**Confidence:** HIGH — derived from launch files, package.xml files, and message definitions

---

## ROS Environment

| Property | Value |
|----------|-------|
| ROS Version | ROS 2 Humble |
| OS | Ubuntu 22.04 |
| DDS | UNKNOWN — CycloneDDS or FastDDS (open question INF-1) |
| Workspace | COLCON workspace at COLCON_CURRENT_PREFIX |
| Simulation | VRX (Virtual RobotX) via OSRF |

---

## Packages

| Package | Language | Purpose | Source Path |
|---------|----------|---------|-------------|
| `fau` | C++ | Main navigation, mission control, propulsion | [`src/fau2/`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2/tree/main/src/fau2) |
| `fau_msgs` | — | Custom message definitions | [`src/fau_msgs/`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2/tree/main/src/fau_msgs) |
| `lidar_msgs` | — | LIDAR custom messages | [`src/lidar_msgs/`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2/tree/main/src/lidar_msgs) |
| `nsga2` | — | NSGA-2 multi-objective optimizer | [`src/nsga2/`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2/tree/main/src/nsga2) |
| `path_planner2` | — | Path planning | [`src/path_planner2/`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2/tree/main/src/path_planner2) |
| `rl_msgs` | — | Reinforcement learning messages | [`src/rl_msgs/`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2/tree/main/src/rl_msgs) |
| `rl_py` | Python | Reinforcement learning controller | [`src/rl_py/`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2/tree/main/src/rl_py) |
| `ros2serial` | — | Serial bridge to Teensy PCU | [`src/ros2serial/`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2/tree/main/src/ros2serial) |
| `teensyRos2` | C++ (Arduino) | Teensy 4.1 PCU firmware | [`src/teensyRos2/`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2/tree/main/src/teensyRos2) |
| `vehicle_state` | C++ | GPS/INS state estimation, coordinate transforms | [`src/vehicle_state2/`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2/tree/main/src/vehicle_state2) |
| `vision_py` | Python | Vision processing | [`src/vision_py/`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2/tree/main/src/vision_py) |
| `yolov9_msgs` | — | YOLOv9 detection messages | [`src/yolov9_msgs/`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2/tree/main/src/yolov9_msgs) |

---

## Launch Files

| File | Purpose | Use Case |
|------|---------|----------|
| [`fau_outdoor.launch.py`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2/blob/main/src/fau2/launch/fau_outdoor.launch.py) | Launch full outdoor field stack | **Real-world field ops** |
| [`fau_vrx2.launch.py`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2/blob/main/src/fau2/launch/fau_vrx2.launch.py) | Launch simulation stack | VRX simulation |
| [`vehicle_state.launch.py`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2/blob/main/src/vehicle_state2/launch/vehicle_state.launch.py) | Launch vehicle state node only | State estimation only |

---

## Nodes (Outdoor / Field Launch)

From `fau_outdoor.launch.py`:

| Node Name | Package | Executable | Key Parameters |
|-----------|---------|-----------|----------------|
| `mission_ctrl_node` | `fau` | `mission_control` | `MC: 1` |
| `nav_array_node` | `fau` | `navigation_array` | — |
| `path_plan_node` | `path_planner2` | `path_planner2` | — |
| `prop_system_node` | `fau` | `propulsion_system_bow` | PID gains: kp_xy=1.0, kp_psi=2.5, ki_xy=0.0, ki_psi=0.0, kd_xy=1.0, kd_psi=1.0, kp_v=1.25, ki_v=0.05, kd_v=1.5 |
| `vehicle_state_node` | `vehicle_state` | `vehicle_state` | latRef=26.055513176834214, lonRef=-80.11413939041087 (FAU campus), Simulation=False |

> **Note (BUG-3):** `vehicle_state.launch.py` has hardcoded VRX Sydney coordinates (latRef=-33.72, lonRef=150.67). Use `fau_outdoor.launch.py` for FAU field ops — it has correct FAU coordinates.

---

## Custom Message Types (fau_msgs)

Source: [`src/fau_msgs/msg/`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2/tree/main/src/fau_msgs/msg)

| Message | Purpose |
|---------|---------|
| `AnchorCmds.msg` | Anchor/station-keeping commands |
| `DroneCmds.msg` | Drone commands |
| `DroneStatus.msg` | Drone status |
| `LightTower.msg` | Light tower state commands |
| `MotorCmds.msg` | Motor commands |
| `NedAcoustic.msg` | Acoustic sensor data in NED frame |
| `NedObjects.msg` | Detected objects in NED frame |
| `ObjectInfo.msg` | Single object info |
| `ObjectPositionArray.msg` | Array of object positions |
| `OccupancyGridInfo.msg` | Occupancy grid for path planning |
| `PepCmds.msg` | PEP (Propulsion) commands |
| `PepStatus.msg` | PEP status |
| `PidGains.msg` | PID gain parameters |
| `PropulsionSystem.msg` | Propulsion system state |
| `PtoStatus.msg` | PTO status |
| `State.msg` | Vehicle state (position, velocity, heading) |
| `TaskInfo.msg` | Mission task information |
| `TeensyStatus.msg` | Teensy PCU status (sensors, mode flags) |
| `Vision.msg` | Vision system output |
| `YoloDetection.msg` | YOLOv9 object detection result |

---

## GPS Reference Coordinates

| Location | latRef | lonRef | Used In |
|----------|--------|--------|---------|
| FAU Campus (field ops) | 26.055513176834214 | -80.11413939041087 | `fau_outdoor.launch.py` |
| VRX Sydney (simulation) | -33.722765423824434 | 150.67399169544902 | `vehicle_state.launch.py` (BUG — wrong for FAU) |

---

## ROS Topics

> **Note:** Explicit topic names are not remapped in launch files. The following are INFERRED from message types and node names. Field verification required.

| Topic (INFERRED) | Message Type | Direction | Publisher | Subscriber |
|------------------|-------------|-----------|-----------|------------|
| `/state` or `/vehicle_state` | `fau_msgs/State` | — | `vehicle_state` | `navigation_array`, `path_planner2` |
| `/motor_cmds` | `fau_msgs/MotorCmds` | — | `propulsion_system_bow` | `ros2serial` |
| `/teensy_status` | `fau_msgs/TeensyStatus` | — | `ros2serial` | `mission_control` |
| `/light_tower` | `fau_msgs/LightTower` | — | `mission_control` | `ros2serial` | 
| `/yolo_detection` | `fau_msgs/YoloDetection` | — | `vision_py` | `navigation_array` |
| `/ned_objects` | `fau_msgs/NedObjects` | — | `navigation_array` | `path_planner2` |

> **IMPORTANT:** All topic names above are INFERRED. Actual topic names require running `ros2 topic list` on the vehicle.

---

## Related Files

- [WAM-V 2 Firmware](firmware.md)
- [WAM-V 2 Hardware](hardware.md)
- [WAM-V 2 Operations](operations.md)
- [RoboBoat ROS Interface](../roboboat/ros_interface.md) — newer codebase comparison
