# Hardcoded Values — Comprehensive Audit

**Purpose:** Complete catalog of all hardcoded values discovered in firmware, launch files, and source code that may need field verification or modification for deployment.
**Last Updated:** 2026-06-05
**Audit Scope:** Thomas_Towfish, LowLevel_Wamv1, LowLevel_Wamv2, roboboat_usv, roboboat_station
**Confidence:** HIGH — Extracted directly from source code

---

## Critical GPS Coordinates (MUST VERIFY)

### **WAM-V 2: WRONG COORDINATES! VRX Simulation Mode Active**
**RISK LEVEL: CRITICAL**

File: [`LowLevel_Wamv2/src/vehicle_state2/config/state.yaml`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2/blob/main/src/vehicle_state2/config/state.yaml)

**Active Setting:**
- `Simulation: true`
- `latRef: -33.7227653423824434`
- `lonRef: 150.67399169544902`
- **Location:** Sydney, Australia (VRX Competition Simulation)

**Commented out (Florida):**
- `latRef: 26.0555992999`
- `lonRef: -80.1134398`

**Impact:** If deployed in Florida with Sydney coordinates, GPS reference frame will be ~16,000 km off. All navigation will fail.

---

### TowFish GPS Coordinates

File: [`Thomas_Towfish/src/vehicle_state/config/state.yaml`](https://github.com/Marine-Robotics-Club/Thomas_Towfish/blob/main/src/vehicle_state/config/state.yaml)

**Active Setting (TowFish Tests):**
- `latRef: 26.0561334`
- `lonRef: -80.1417659`

**Commented Locations:**
- Fort Lauderdale: `26.0555992999, -80.1134398`
- VRX Sydney: `-33.7227653423824434, 150.67399169544902`
- Sydney: `-33.724223, 150.679736`
- Hawaii: `21.30996, -157.890100`

---

### RoboBoat GPS Coordinates

File: [`roboboat_usv/src/asv_launch/launch/sensors.launch.py`](https://github.com/Marine-Robotics-Club/roboboat_usv/blob/main/src/asv_launch/launch/sensors.launch.py)

**Active Setting:**
- `latRef: 26.0555452`
- `lonRef: -80.1132476`
- `Simulation: False`
- **Location:** Fort Lauderdale / FAU area

---

## Serial Ports & Baud Rates

### TowFish (Teensy 4.1 Firmware)
**File:** [`RASP_V2.ino`](https://github.com/Marine-Robotics-Club/Thomas_Towfish/blob/main/src/RASP_V2/RASP_V2.ino)
- Xbee: `Serial5`, `115200` baud

### TowFish (Twinleaf Logger ROS)
**File:** [`twinleaf_logger_ros.py`](https://github.com/Marine-Robotics-Club/Thomas_Towfish/blob/main/src/twinleaf/twinleaf/twinleaf_logger_ros.py)
- COM Port: `/dev/ttyUSB0`
- Baud Rate: `115200`

### WAM-V 2 (PCU Firmware)
**File:** [`WAMV_PCU_ROS2.ino`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2/blob/main/src/teensyRos2/WAMV_PCU_ROS2/WAMV_PCU_ROS2.ino)
- Weather Station (Serial2): `4800` baud
- Port Motor Controller: `115200` baud
- Starboard Motor Controller: `115200` baud
- Jetson: `9600` baud
- Depth Sensor: `115200` baud

### WAM-V 2 (ROS2 Serial Bridge)
**File:** [`serial2arduino.cpp`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2/blob/main/src/ros2serial/src/serial2arduino.cpp)
- ControlTeensy: `/dev/ttyACM1`, `115200` baud
- subSystemTeensy: `/dev/ttyACM0`, `115200` baud

### WAM-V 1 (PCU Firmware)
**File:** [`WAMV_PCU_ROS2.ino`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv1/blob/main/src/teensyRos2/WAMV_PCU_ROS2/WAMV_PCU_ROS2.ino)
- Weather Station (Serial2): `4800` baud (Airmar 110WX)

### WAM-V 1 (ROS2 Serial Bridge)
**File:** [`serial2arduino.cpp`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv1/blob/main/src/ros2serial/src/serial2arduino.cpp)
- ControlTeensy: `/dev/ttyACM1`, `115200` baud
- subSystemTeensy: `/dev/ttyACM0`, `115200` baud

### RoboBoat (ROS2 Serial Bridge - Python)
**File:** [`ros2serial.py`](https://github.com/Marine-Robotics-Club/roboboat_usv/blob/main/src/ros2serial_py/ros2serial_py/ros2serial.py)
- Default Port: `/dev/ttyACM0`
- Default Baud: `9600`

---

## Network / IP Addresses

### RoboBoat Station → ASV Communication
**CRITICAL: Primary control channel**

**File:** [`roboboat_station/src/heartbeat/heartbeat/client.py`](https://github.com/Marine-Robotics-Club/roboboat_station/blob/main/src/heartbeat/heartbeat/client.py)
- **ASV IP:** `10.10.10.1`
- **Port:** `50000` (TCP)
- **Protocol:** Protobuf (gRPC) using `robocommand.roboboat.v1.report_pb2`

### WAM-V 1 Network (GUI)
**File:** [`gui_ros_node.py`](https://github.com/Marine-Robotics-Club/Thomas_Towfish/blob/main/src/fau_gui/fau_gui/gui_ros_node.py)
- WAM-V1 Low Level: `192.168.1.110`
- WAM-V1 High Level: `192.168.1.120`

---

## Pin Definitions

### TowFish (Teensy 4.1)
**File:** [`RASP_V2.ino`](https://github.com/Marine-Robotics-Club/Thomas_Towfish/blob/main/src/RASP_V2/RASP_V2.ino)
- STAY_ON: `1`
- HALL1 (U2): `32`
- HALL2 (U3): `25`
- HALL3 (U4): `0`
- VBAT: `A0`
- LEAK: `A1`
- LED: `13`

### WAM-V 2 (Teensy 4.1 PCU)
**File:** [`WAMV_PCU_ROS2.ino`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2/blob/main/src/teensyRos2/WAMV_PCU_ROS2/WAMV_PCU_ROS2.ino)
- CT_PORT_THRUST: `2`
- CT_STBD_THRUST: `3`
- CT_PORT_AZIMUTH: `4`
- CT_STBD_AZIMUTH: `5`
- RC_ACTIVE_N: `6`
- RC_KILL_ACTIVE_N: `9`
- **CT_PW_CHS: `10` (spare, not used)**

### WAM-V 1 (Teensy 4.1 PCU — KEY DIFFERENCES)
**File:** [`WAMV_PCU_ROS2.ino`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv1/blob/main/src/teensyRos2/WAMV_PCU_ROS2/WAMV_PCU_ROS2.ino)
- **CT_PW_CH5: `10` (Port Bow Thruster) ← WAM-V 1 ONLY**
- **CT_PORT_BOW_THRUST: `12` (Stbd Bow Thruster) ← WAM-V 1 ONLY**
- All other pins same as WAM-V 2

---

## PID / Controller Gains

### RoboBoat APF Controller
**File:** [`roboboat.launch.py`](https://github.com/Marine-Robotics-Club/roboboat_usv/blob/main/src/asv_launch/launch/roboboat.launch.py)

**Hardcoded in launch file:**
- `kp_xy: 0.7`
- `kp_psi: 5.0`
- `ki_xy: 0.0`
- `ki_psi: 1.0`
- `kd_xy: 1.0`
- `kd_psi: 10.0`
- `kp_v: 1.0`
- `ki_v: 0.0`

---

## LiDAR / Camera Offsets (WAM-V 2)

**File:** [`state.yaml`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2/blob/main/src/vehicle_state2/config/state.yaml)

**LiDAR Transform:**
- `lidarXOffset: 0.75`
- `lidarYOffset: 0.0`
- `lidarZOffset: -1.5`

**Camera Transform:**
- `cameraXOffset: 0.75`
- `cameraYOffset: -0.108`
- `cameraZOffset: -1.411`

---

## File Paths (Hardcoded)

### TowFish Launch File
**File:** [`vehicle_state.launch.py`](https://github.com/Marine-Robotics-Club/Thomas_Towfish/blob/main/src/vehicle_state/launch/vehicle_state.launch.py)
- Config: `~/Thomas_TowFish/src/vehicle_state/config/state.yaml`

### RoboBoat Launch Files
**File:** [`roboboat.launch.py`](https://github.com/Marine-Robotics-Club/roboboat_usv/blob/main/src/asv_launch/launch/roboboat.launch.py)
- Workspace root comment: `/home/highlevel/wamv_nav`

### GUI ROS Node
**File:** [`gui_ros_node.py`](https://github.com/Marine-Robotics-Club/Thomas_Towfish/blob/main/src/fau_gui/fau_gui/gui_ros_node.py)
- Bag file: `/home/xavi/vrx/vrx_0.db3`
- Bag folder: `/home/xavi/`

---

## Calibration Constants

### TowFish Battery Voltage
**File:** [`RASP_V2.ino`](https://github.com/Marine-Robotics-Club/Thomas_Towfish/blob/main/src/RASP_V2/RASP_V2.ino)
- VBAT calibration factor: `3.842`

---

## Interval Timings (Firmware)

### TowFish Teensy
**File:** [`RASP_V2.ino`](https://github.com/Marine-Robotics-Club/Thomas_Towfish/blob/main/src/RASP_V2/RASP_V2.ino)
- HEALTH_INTERVAL: `100` ms
- DEBOUNCE_INTERVAL: `10` ms
- PRINT_INTERVAL: `50` ms
- MOTION_READ_INTERVAL: `20` ms
- Hall sensor debounce: `16` consecutive samples required

---

## Recommended Actions

1. **IMMEDIATE:** Change WAM-V 2 `Simulation: true` to `false` and update GPS coordinates to Florida before field deployment.
2. **Verify:** All GPS reference coordinates against actual deployment location.
3. **Document:** Create config file template with environment variables instead of hardcoded paths.
4. **Test:** Verify serial port device names (`/dev/ttyACM*`, `/dev/ttyUSB*`) match actual hardware on each platform.
5. **Parameterize:** Move PID gains to parameter files instead of launch file literals.
6. **Audit:** ROS_DOMAIN_ID not found anywhere — verify if systems use default domain (0) or require specific configuration.
