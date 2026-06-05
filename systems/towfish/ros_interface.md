# TowFish ROS 2 Interface Reference

**Purpose:** Document all ROS 2 nodes, topics, packages, and launch files for the Thomas TowFish system.
**Last Updated:** 2025-07
**Source Repos:** [Thomas_Towfish](https://github.com/Marine-Robotics-Club/Thomas_Towfish)
**Confidence:** HIGH — extracted directly from source files.

---

## ROS Version

- **ROS 2 Humble** on Ubuntu 22.04
- Build system: `colcon`
- Depends on: `osrf/vrx` simulation package (for VRX topics in sim mode)

---

## Packages

| Package | Type | Location | Purpose |
|---------|------|----------|---------|
| `vehicle_state` | C++ (ament_cmake) | `src/vehicle_state/` | Converts GPS + IMU to NED pose; republishes vehicle state |
| `twinleaf` | Python (ament_python) | `src/twinleaf/` | ROS 2 node that reads Twinleaf magnetometer via serial and publishes to ROS topics |
| `fau_gui` | Python (ament_python) | `src/fau_gui/` | PyQt5-based HMI (simple_gui demo for ROS 2) |

---

## Nodes

### `state_to_ned_1` (package: `vehicle_state`)

**Source:** [state_to_ned.cpp](https://github.com/Marine-Robotics-Club/Thomas_Towfish/blob/main/src/vehicle_state/src/state_to_ned.cpp)

**Purpose:** Assembles xsense (IMU) + GPS data and outputs NED-frame vehicle state. Also republishes goal waypoints for `wamv1`.

**Parameter:** `Simulation` (bool, default: `false`) — switches topic sources between sim (VRX) and real hardware.

#### Subscriptions

| Topic | Message Type | Mode | Notes |
|-------|-------------|------|-------|
| `/drone/imu/data` | `sensor_msgs/Imu` | Real HW | Quaternion / IMU from drone/autopilot |
| `/drone/global_position/raw/fix` | `sensor_msgs/NavSatFix` | Real HW | GPS fix from drone autopilot |
| `/wamv/sensors/imu/imu/data` | `sensor_msgs/Imu` | Simulation | IMU from VRX sim WAM-V |
| `/wamv/sensors/gps/gps/fix` | `sensor_msgs/NavSatFix` | Simulation | GPS from VRX sim WAM-V |
| `/vrx/stationkeeping/goal` | `geometry_msgs/PoseStamped` | Simulation | Stationkeeping goal from VRX |
| `/vrx/wayfinding/waypoints` | `geometry_msgs/PoseArray` | Simulation | Wayfinding waypoints from VRX |

#### Publications

| Topic | Message Type | Notes |
|-------|-------------|-------|
| `/towfish/vehicle_pose` | `geometry_msgs/Pose2D` | 2D NED pose of TowFish |
| `/towfish/p3d_ned` | `nav_msgs/Odometry` | Full odometry in NED frame |
| `/towfish/bfvelo_ned` | `geometry_msgs/Vector3` | Body-frame velocity in NED |
| `/wamv1/vehicle_goal_pose` | `geometry_msgs/Pose2D` | Goal pose for WAM-V 1 |
| `/wamv1/vehicle_goal_waypoints` | `geometry_msgs/PoseArray` | Waypoints for WAM-V 1 |

---

### `twinleaf_gps_logger` (package: `twinleaf`)

**Source:** [twinleaf_logger_ros.py](https://github.com/Marine-Robotics-Club/Thomas_Towfish/blob/main/src/twinleaf/twinleaf/twinleaf_logger_ros.py)

**Purpose:** Reads Twinleaf magnetometer data over serial, fuses with GPS/IMU from ROS, and publishes to topics + logs to CSV.

**Serial Config:** `/dev/ttyUSB0`, baud 115200, timeout 1 s, 10 Hz timer

#### Subscriptions

| Topic | Message Type | Notes |
|-------|-------------|-------|
| `/drone/global_position/raw/fix` | `sensor_msgs/NavSatFix` | GPS position |
| `/drone/imu/data` | `sensor_msgs/Imu` | Orientation (quaternion → heading) |
| `/drone/global_position/raw/gps_vel` | `geometry_msgs/TwistStamped` | GPS velocity |

#### Publications

| Topic | Message Type | QoS | Notes |
|-------|-------------|-----|-------|
| `/twinleaf/mag_field` | `std_msgs/Float32` | Best Effort, depth 10 | Magnetic field value (unit: INFERRED microtesla, published as-is) |
| `/twinleaf/gps` | `sensor_msgs/NavSatFix` | Best Effort, depth 10 | GPS fix re-published from `/drone/global_position/raw/fix` |
| `/twinleaf/speed` | `std_msgs/Float32` | Best Effort, depth 10 | Speed from GPS velocity |

**CSV Output:** `Twinleaf_data_YYYYMMDD_HHMMSS.csv`

| Column | Type | Notes |
|--------|------|-------|
| Timestamp | string | datetime |
| Iteration | int | From magnetometer packet |
| Magnetic Field | float | In tesla (variable stored as microtesla but not converted — INFERRED BUG) |
| Status | int | Status code from magnetometer |
| Extra | float | Extra value from magnetometer packet |
| Latitude | float | From GPS |
| Longitude | float | From GPS |
| psiNED | float | Heading in radians (NED) |
| psiNED_degrees | float | Heading in degrees (NED) |
| Speed | float | From GPS velocity |

---

### `fau_gui` (package: `fau_gui`)

**Source:** [src/fau_gui/fau_gui/](https://github.com/Marine-Robotics-Club/Thomas_Towfish/tree/main/src/fau_gui/fau_gui)

**Purpose:** PyQt5 HMI for ROS 2. Provides an operator dashboard. Based on `simple_gui` demo (upstream: `sampreets3/simple_gui`).

**Dependencies:** `pyqt5`, ROS 2 Humble

**Run command:** `ros2 run simple_gui ros2_hmi_node` (per upstream README — actual node name in FAU fork may differ)

---

## Launch Files

| File | Package | Nodes Launched | Key Parameters |
|------|---------|---------------|----------------|
| [vehicle_state.launch.py](https://github.com/Marine-Robotics-Club/Thomas_Towfish/blob/main/src/vehicle_state/launch/vehicle_state.launch.py) | `vehicle_state` | `vehicle_state` | `latRef: 26.0561334`, `lonRef: -80.1417659`; `params_file`: `~/Thomas_TowFish/src/vehicle_state/config/state.yaml` |

---

## Topic Map Summary

```
Drone/Autopilot                      TowFish ROS
  /drone/imu/data              -->   state_to_ned_1
  /drone/global_position/raw/fix --> state_to_ned_1
                                     twinleaf_gps_logger
  /drone/global_position/raw/gps_vel --> twinleaf_gps_logger

state_to_ned_1 publishes:
  /towfish/vehicle_pose
  /towfish/p3d_ned
  /towfish/bfvelo_ned
  /wamv1/vehicle_goal_pose
  /wamv1/vehicle_goal_waypoints

twinleaf_gps_logger publishes:
  /twinleaf/mag_field
  /twinleaf/gps
  /twinleaf/speed
```

---

## Notes & Known Issues

1. **Unit ambiguity in twinleaf_logger_ros.py:** The variable `mag_field_microtesla` is read from serial and assigned directly to `mag_field_tesla` without unit conversion. The published value and CSV value may be in microtesla despite the name. This should be verified against Twinleaf device documentation.
2. **GPS topic naming:** Topics prefixed with `/drone/` suggest the TowFish ROS code is designed to be co-deployed with a drone/ArduPilot or PX4 companion — the WAM-V autopilot may publish under these topic names. Confirm with actual deployment setup.
3. **Simulation vs. Real HW:** The `Simulation` parameter in `state_to_ned_1` switches between `/wamv/sensors/...` (VRX) and `/drone/...` (real HW) topics. The twinleaf node always uses `/drone/...` topics.

---

## Related Files

- [hardware.md](./hardware.md)
- [data_format.md](./data_format.md)
- [firmware.md](./firmware.md)
- [state_to_ned.cpp](https://github.com/Marine-Robotics-Club/Thomas_Towfish/blob/main/src/vehicle_state/src/state_to_ned.cpp)
- [twinleaf_logger_ros.py](https://github.com/Marine-Robotics-Club/Thomas_Towfish/blob/main/src/twinleaf/twinleaf/twinleaf_logger_ros.py)
