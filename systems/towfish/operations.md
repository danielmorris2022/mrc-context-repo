# TowFish Operations Guide

**Purpose:** Step-by-step startup, mission, and shutdown procedures for the TowFish system.
**Last Updated:** 2025-07
**Source Repos:** [Thomas_Towfish](https://github.com/Marine-Robotics-Club/Thomas_Towfish)
**Confidence:** MEDIUM — derived from `commands.txt`, firmware source, and README. No formal SOP document found in repo.

---

## Quick Reference — Run Commands

From [commands.txt](https://github.com/Marine-Robotics-Club/Thomas_Towfish/blob/main/commands.txt):

```bash
# Start MAVRos (drone/autopilot bridge)
mavros

# Launch vehicle state node
ros2 launch vehicle_state vehicle_state.launch.py

# Run GUI
ros2 run fau_gui tow_fish_node

# Run Twinleaf magnetometer logger (ROS node)
ros2 run fau_gui twinleaf_logger
```

> **NOTE:** The `fau_gui` package entry point `tow_fish_node` and `twinleaf_logger` are run via `ros2 run fau_gui ...`. This is consistent with the fau_gui being a Python package that exposes multiple console scripts.

---

## Pre-Deployment Checklist

> INFERRED from hardware knowledge and firmware behavior. Verify against actual team procedures.

- [ ] **Battery check:** Confirm `battery_v > 20.0 V` via serial monitor or telemetry before deployment
- [ ] **Leak sensor:** Confirm no pre-existing leak (check via serial output or `leak_ok` flag)
- [ ] **IMU calibration:** Power on TowFish and verify BNO055 IMU calibration status (Adafruit BNO055 shows calibration 0–3 for each axis; full calibration = 3,3,3,3)
- [ ] **SD card:** Confirm SD card is inserted and `SD.begin()` succeeds (firmware indicates success via serial)
- [ ] **XBee link:** Confirm XBee radio link is live (surface station can receive data)
- [ ] **Twinleaf magnetometer:** Power on Twinleaf device; confirm it appears on `/dev/ttyUSB0` on RPi
- [ ] **ROS workspace built:** Confirm `colcon build` has been run and `install/setup.bash` sourced
- [ ] **MAVRos running:** Start `mavros` before launching `vehicle_state` node
- [ ] **GPS lock:** Confirm `/drone/global_position/raw/fix` topic is publishing with valid coordinates
- [ ] **TowFish on tow cable:** Attach tow cable to WAM-V at ~10 m standoff; confirm mechanical connection

---

## Software Startup Procedure

### 1. Build & Source Workspace
```bash
cd ~/Thomas_TowFish
colcon build
source install/setup.bash
```

### 2. Start MAVRos
```bash
mavros
```
This bridges ArduPilot/PX4 autopilot (on WAM-V) to ROS 2 topics like `/drone/imu/data`, `/drone/global_position/raw/fix`.

### 3. Launch Vehicle State Node
```bash
ros2 launch vehicle_state vehicle_state.launch.py
```
This starts the `state_to_ned_1` node. GPS reference: lat `26.0561334`, lon `-80.1417659` (FAU Boca Raton). Edit `state.yaml` to change reference location.

### 4. Start GUI (Optional)
```bash
ros2 run fau_gui tow_fish_node
```
Opens PyQt5 operator dashboard.

### 5. Start Twinleaf Logger
```bash
ros2 run fau_gui twinleaf_logger
```
This starts the `twinleaf_gps_logger` node. Reads Twinleaf magnetometer on `/dev/ttyUSB0` at 115200 baud and logs to CSV.

---

## Starting Data Recording (Teensy Firmware)

The Teensy firmware receives single-character commands over XBee or USB serial:

| To do this... | Send command |
|---------------|--------------|
| Start saving to SD | `S` |
| Stop saving | `q` |
| Stream data live | `p` |
| Send one sample | `u` |
| List SD files | `l` |
| Dump a file | `D` (first select with `=`) |
| Select file to dump | `=` then file number |
| Display selected file name | `d` |
| See help menu | any other key |

**Physical Hall Sensor Shortcuts (no serial needed):**
- Wave magnet near **U2 (pin 32)** → starts saving
- Wave magnet near **U4 (pin 0)** → stops saving
- Hold magnet near **U3 (pin 25) for 3 seconds** → power off

---

## Stopping / Shutdown

1. Send `q` command to Teensy (via XBee or USB serial) to stop logging
2. Shutdown ROS nodes: `Ctrl+C` on each terminal
3. Power off TowFish using U3 hall sensor (hold 3 s) or disconnect power
4. Retrieve TowFish from water; inspect for leaks
5. Remove SD card; transfer CSV files to analysis computer

---

## Data Retrieval

### From SD Card
1. Remove SD card from Teensy 4.1
2. Read files named `RASP_001.csv` through `RASP_099.csv`
3. Use [data_format.md](./data_format.md) for column definitions

### Via Serial Dump (XBee)
1. Send `l` to list available files
2. Send `=` followed by file number to select
3. Send `D` to dump the selected file over XBee serial

### Twinleaf CSV
- Files are saved by the ROS node as `Twinleaf_data_YYYYMMDD_HHMMSS.csv` in the working directory where the node was launched

---

## Standalone Twinleaf Logger (No ROS)

For bench testing without ROS:
```bash
python3 ~/Thomas_TowFish/src/twinleaf_python/twinleaf_logger.py
```
Enter the COM port when prompted (e.g., `/dev/ttyUSB0` on Linux or `COM3` on Windows).
Outputs: `Twinleaf_data_YYYYMMDD_HHMMSS.csv` with columns: `Timestamp, Iteration, Magnetic Field, Status`.

---

## Data Analysis

- **MATLAB:** TowFish data analysis scripts are referenced in `data_format.md` (located in `twinleaf_data/` folder)
- **Python plotting:** Use `src/twinleaf_python/plot_twinleaf.py` for quick magnetic field plots

---

## Changing GPS Reference Location

Edit `~/Thomas_TowFish/src/vehicle_state/config/state.yaml`:
```yaml
state:
  ros__parameters:
    latRef: 26.0561334   # Change to your location
    lonRef: -80.1417659  # Change to your location
```
Pre-defined locations in the YAML (commented out): Fort Lauderdale, VRX Sydney, Sydney, Hawaii.

---

## Simulation Mode

To test with VRX Gazebo simulator instead of real hardware:
1. Set `Simulation: true` parameter in `vehicle_state` node
2. In simulation mode, the node subscribes to `/wamv/sensors/imu/imu/data` and `/wamv/sensors/gps/gps/fix` instead of `/drone/` topics

---

## Related Files

- [commands.txt](https://github.com/Marine-Robotics-Club/Thomas_Towfish/blob/main/commands.txt) — operator quick-start commands
- [firmware.md](./firmware.md)
- [hardware.md](./hardware.md)
- [ros_interface.md](./ros_interface.md)
- [data_format.md](./data_format.md)
