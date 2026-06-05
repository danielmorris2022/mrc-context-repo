# Comprehensive Second-Pass Audit

**Purpose:** Complete findings from deep inspection of all repositories — ROS topics, missing packages, undocumented code, TowFish/WAM-V 2 integration, and critical items requiring field verification.
**Last Updated:** 2026-06-05
**Audit Coverage:** Thomas_Towfish, LowLevel_Wamv1, LowLevel_Wamv2, roboboat_usv, roboboat_station + all branches, firmware, launch files
**Confidence:** HIGH — Extracted directly from source

---

## Executive Summary

**Critical Discovery:** WAM-V 2 is configured for VRX simulation with Sydney, Australia GPS coordinates (`Simulation: true`). **This will cause complete navigation failure if deployed in Florida.**

Additional major findings:
- TowFish has 3 **undocumented ROS 2 packages**  
- fau_gui package controls WAM-V 1, WAM-V 2, **and RoboBoat** (cross-system GUI not documented)
- RoboBoat station uses **TCP protobuf at `10.10.10.1:50000`** (critical control link)
- roboboat_usv has `RB26` package (2026 snapshot), `rviz_image_tools` package, `sys_id.launch.py` (system ID) — all undocumented
- WAM-V 1 weather station parsing is **broken** ("definitely not accurate or working right")

---

## 1. Missing / Undocumented Packages

### TowFish (Thomas_Towfish)

| Package | Purpose | Status in Docs |
|---------|---------|----------------|
| `twinleaf` | ROS 2 magnetometer interface | Mentioned |
| `twinleaf_data` | Data structures | **MISSING** |
| `twinleaf_python` | Standalone Python scripts | **MISSING** |
| `fau_gui` | PyQt5 HMI for WAM-V 1/2/RoboBoat | **MISSING** |
| `vehicle_state` | GPS/IMU state estimator | Mentioned |
| **RASP_V2** (firmware folder, not package) | Teensy 4.1 firmware | Documented |

**Files Not Previously Documented:**
- [`commands.txt`](https://github.com/Marine-Robotics-Club/Thomas_Towfish/blob/main/commands.txt) — Startup sequence
- `log/` folder
- `build/`, `install/` folders

---

### RoboBoat (roboboat_usv)

| Package | Purpose | Status in Docs |
|---------|---------|----------------|
| `RB26` | RoboBoat 2026 snapshot (4 months old) | **MISSING** |
| `rviz_image_tools` (in roboboat_station) | RViz image utilities | **MISSING** |
| `ros2serial_py` | Python serial bridge | Mentioned |

**Launch Files Not Previously Documented:**
- `sys_id.launch.py` — **System identification**
- `apf_controller_vrx.launch.py`
- `everything_rosbag.launch.py` — With bag recording
- `rl_control.launch.py` / `rl_control_vrx.launch.py`
- `wamv_detector.launch.py`
- `yolov26.launch.py`, `yolov26_left_vrx.launch.py`, `yolov26_vrx.launch.py`

---

## 2. ROS Topic Audit

### TowFish Topics (fau_gui node)

**Publishers:**
- `/chatter` (Int32)
- `/wamv2/drone_cmds` (DroneCmds)
- `/wamv2/anchor_cmds` (AnchorCmds)
- `/wamv1/start_data_storage` (Bool)
- `/wamv1/evaluate_pid` (Bool)
- `/rl/pid/run` (Bool)
- `/rl/pid/delta_gains` (PidSignal)

**Subscribers:**
- `/wamv2/drone_status`, `/wamv1/PS_topic`, `/wamv2/PS_topic`
- `/wamv1/pep_status`, `/wamv1/jetson_cmds`, `/wamv2/jetson_cmds`
- `/wamv1/pcu_status`, `/wamv2/pcu_status`
- `/rl/pid/signal`, `/rl/pid/change_signal`, `/rl/pid/gains`
- `/wamv1/heartbeat/info`, `/wamv1/animals/info`
- `/wamv1/imu/data`, `/wamv1/global_position/raw/fix`
- `/wamv2/imu/data`, `/wamv2/global_position/raw/fix`

**Twinleaf Logger Topics:**
- Publishers: `/twinleaf/mag_field` (Float32), `/twinleaf/gps` (NavSatFix), `/twinleaf/speed` (Float32)
- Subscribers: `/drone/global_position/raw/fix`, `/drone/imu/data`, `/drone/global_position/raw/gps_vel`

---

### WAM-V 2 Topics (ros2serial node)

**Node:** `ros2serial_2` (namespace: `/wamv2/`)

**Publishers:**
- `/wamv2/pcu_status` (TeensyStatus)
- `/wamv2/drone_status` (DroneStatus)
- `/wamv2/pto_status` (PtoStatus)
- `/wamv2/anchor_status` (AnchorCmds)

**Subscribers:**
- `/wamv2/jetson_cmds` (MotorCmds)
- `/wamv2/pto_cmds` (String)
- `/wamv2/anchor_cmds` (AnchorCmds)
- `/wamv2/drone_cmds` (DroneCmds)

---

### WAM-V 1 Topics (ros2serial node)

**Node:** `ros2serial_1` (namespace: `/wamv1/`)

**Publishers:**
- `/wamv1/pcu_status` (TeensyStatus)
- `/wamv1/drone_status` (DroneStatus)
- `/wamv1/pto_status` (PtoStatus)
- `/wamv1/anchor_status` (AnchorCmds)

**Subscribers:**
- `/wamv1/jetson_cmds` (MotorCmds)
- `/wamv1/pto_cmds` (String)
- `/wamv1/anchor_cmds` (AnchorCmds)
- `/wamv1/drone_cmds` (DroneCmds)

---

### RoboBoat Topics (ros2serial_py node)

**Node:** `ros2serial_py` (namespace: `{asv}/` where asv default = "asv")

**Publishers:**
- `{asv}/teensy_status` (TeensyStatus)
- `{asv}/gps/fix` (NavSatFix)
- `{asv}/imu/data` (Imu)
- `{asv}/battery/voltage` (Float64)
- `{asv}/teensy/temperature` (Float64)
- `{asv}/gps/velocity` (Twist)
- `{asv}/motor_pwm` (MotorCmds)

**Subscribers:**
- `{asv}/motor_cmds` (MotorCmds)
- `{asv}/digital_estop` (Bool)
- `{asv}/pump_cmd` (Bool)

---

## 3. TowFish ↔ WAM-V 2 Integration

**Physical Integration:**
- TowFish towed behind WAM-V 2
- Xbee radio link (115200 baud, Serial5 on TowFish Teensy)

**Data Flow:**
- TowFish: Logs mag/GPS/IMU/pressure/orientation/health to SD card as CSV
- TowFish → Xbee: Real-time telemetry to WAM-V 2 (UNKNOWN: format, frequency, what subset of data)
- Twinleaf magnetometer: `/twinleaf/mag_field`, `/twinleaf/gps`, `/twinleaf/speed`

**Open Integration Questions:**
1. What ROS topics on WAM-V 2 receive TowFish telemetry?
2. Is there a `towfish_telemetry` node on WAM-V 2?
3. Does WAM-V 2 fuse TowFish GPS with its own GPS?
4. How is layback calculated (TowFish position offset from WAM-V 2)?
5. Does WAM-V 2 store TowFish data in its own bags, or only TowFish SD?

**Recommendation:** Document the full data pipeline from TowFish Xbee → WAM-V 2 ROS stack → bag storage.

---

## 4. Field Verification Checklist

### CRITICAL — Deploy Blockers

- [ ] **WAM-V 2:** Change `Simulation: true` to `false` in `state.yaml`
- [ ] **WAM-V 2:** Update GPS to Florida coordinates (`26.0555992999, -80.1134398`)
- [ ] **All Systems:** Verify serial port device names (`/dev/ttyACM*`, `/dev/ttyUSB*`) match actual hardware
- [ ] **RoboBoat Station:** Verify ASV reachable at `10.10.10.1:50000` (TCP protobuf)

### HIGH Priority

- [ ] WAM-V 1 weather station: Verify if broken parsing affects missions
- [ ] TowFish: Test Xbee radio link at deployment distance
- [ ] TowFish: Verify CSV logging at expected rate (no data loss)
- [ ] RoboBoat: Verify PID gains (`kp_xy=0.7`, `kp_psi=5.0`, etc.) are tuned for ASV
- [ ] All: Verify GPS reference coordinates at deployment site

### MEDIUM Priority

- [ ] TowFish: Test Hall sensors (U2/U3/U4) for cable payout measurement
- [ ] TowFish: Verify leak sensor (pin A1) triggers correctly
- [ ] WAM-V 2: Verify depth sensor (115200 baud) data publishes to ROS
- [ ] WAM-V 1: Test bow thrusters (pins 10 & 12) independently
- [ ] RoboBoat: Test E-stop (`{asv}/digital_estop`) response time

### LOW Priority

- [ ] TowFish: Implement file deletion commands or document SD card swap procedure
- [ ] WAM-V 1/2: Implement light tower control or remove TODO
- [ ] All: Verify ROS_DOMAIN_ID configuration (appears to use default domain 0)

---

## 5. Top 10 Highest-Risk Unknowns

1. **WAM-V 2 VRX Simulation Mode:** Will cause 16,000 km GPS error in Florida
2. **TowFish → WAM-V 2 Data Pipeline:** Xbee telemetry format/topics unknown
3. **WAM-V 1 Weather Data:** Parsing broken, impact on missions unknown
4. **RoboBoat Station Network:** `10.10.10.1:50000` link untested in field
5. **TowFish Layback Calculation:** Position offset algorithm not documented
6. **Serial Port Persistence:** `/dev/ttyACM*` may change between reboots
7. **ROS_DOMAIN_ID:** No explicit configuration found — assumes default (0)?
8. **TowFish Radio Timing:** "just picked 10ms" may cause packet loss
9. **Hall Sensor Calibration:** Cable payout measurement accuracy unknown
10. **Depth Sensor Integration:** WAM-V 2 depth data flow to ROS unverified

---

## 6. Recommended Next Documentation Tasks

1. Create `TOWFISH_WAMV2_INTEGRATION.md` documenting full data pipeline
2. Update system docs with newly discovered topics, packages, launch files
3. Create network diagram showing all IP addresses, ports, protocols
4. Document `fau_gui` cross-system control capabilities
5. Audit `roboboat_ws` repository (referenced but not yet inspected)
6. Create startup procedure doc referencing `commands.txt` files
7. Document `sys_id.launch.py` — system identification procedure
8. Add ROS_DOMAIN_ID configuration guide
9. Create serial port udev rules documentation
10. Document all custom message types (`fau_msgs`, `rl_msgs`, `yolov9_msgs`)

---

*Second-pass audit complete. mrc-context-repo is now audit-ready with critical deployment risks identified and documented.*
