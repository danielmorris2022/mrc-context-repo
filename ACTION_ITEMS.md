# Action Items - Post-Audit Tracker

**Title:** Action Items - Post-Audit Tracker  
**Purpose:** Track all actionable items identified in the mrc-context-repo audit (AUDIT_REPORT.md, COMPREHENSIVE_AUDIT.md, HARD_CODED_VALUES.md, TODO_FIXME_INDEX.md). Convert audit findings into prioritized, assignable tasks with verification steps.  
**Last Updated:** 2026-06-05  
**Source Repos:**  
- [mrc-context-repo audit/](https://github.com/danielmorris2022/mrc-context-repo/tree/main/audit)  
- [Thomas_Towfish](https://github.com/Marine-Robotics-Club/Thomas_Towfish)  
- [LowLevel_Wamv2](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2)  
- [LowLevel_Wamv1](https://github.com/Marine-Robotics-Club/LowLevel_Wamv1)  
- [roboboat_usv](https://github.com/Marine-Robotics-Club/roboboat_usv)  
**Confidence:** HIGH (built from direct audit of source repositories)  

---

## Overview

This document converts audit findings into structured action items. Each item includes:  
- **Priority:** CRITICAL / HIGH / MEDIUM / LOW  
- **System:** TowFish / WAM-V 2 / WAM-V 1 / RoboBoat / Cross-System  
- **Source File:** Path to the file with the issue  
- **Issue:** Brief description of the problem  
- **Impact:** What breaks if not fixed  
- **Verification:** How to verify the fix  
- **Owner:** TBD (assign to team members)  
- **Status:** PENDING / IN PROGRESS / RESOLVED  
- **Link to Source:** GitHub link and audit reference  

**Total Actions:** 47  
**Critical:** 8  
**High:** 15  
**Medium:** 18  
**Low:** 6  

---

## CRITICAL Priority Actions

### AC-01: WAM-V 2 VRX Simulation Mode Enabled in Production Config

**Priority:** CRITICAL  
**System:** WAM-V 2  
**Source File:** `LowLevel_Wamv2/vehicle_state2/config/state.yaml` line 3  
**Issue:** `vrx_simulation: true` is set in production config  
**Impact:** DEPLOYMENT BLOCKER - System will expect simulation environment and fail on actual hardware. GPS waypoints will use Sydney test coordinates instead of real Florida mission data.  
**Verification:**  
1. Set `vrx_simulation: false` in state.yaml  
2. Verify GPS coordinates match Florida test site  
3. Test on actual WAM-V 2 hardware before field deployment  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- File: https://github.com/Marine-Robotics-Club/LowLevel_Wamv2/blob/main/vehicle_state2/config/state.yaml#L3  
- Audit: [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#deployment-blockers), [HARD_CODED_VALUES.md](audit/HARD_CODED_VALUES.md#wam-v-2-vehicle_state2configstateyaml)  

---

### AC-02: WAM-V 2 GPS Coordinates Hardcoded to Sydney, Australia

**Priority:** CRITICAL  
**System:** WAM-V 2  
**Source File:** `LowLevel_Wamv2/vehicle_state2/config/state.yaml` lines 8-13  
**Issue:** GPS waypoints hardcoded to Sydney coordinates (151.22° E) instead of Florida test site  
**Impact:** DEPLOYMENT BLOCKER - Autonomous waypoint navigation will fail. System will attempt to navigate to Australia.  
**Verification:**  
1. Replace Sydney coordinates with Florida test site GPS  
2. Verify all waypoints are within expected local area (< 1km radius)  
3. Test waypoint following in simulation with correct coordinates  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- File: https://github.com/Marine-Robotics-Club/LowLevel_Wamv2/blob/main/vehicle_state2/config/state.yaml#L8-L13  
- Audit: [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#deployment-blockers), [HARD_CODED_VALUES.md](audit/HARD_CODED_VALUES.md#wam-v-2-vehicle_state2configstateyaml)  

---

### AC-03: TowFish-WAM-V2 Serial Telemetry Integration Not Documented

**Priority:** CRITICAL  
**System:** TowFish + WAM-V 2  
**Source File:** Multiple (TowFish RASP_V2.ino, WAM-V2 serial2arduino.cpp)  
**Issue:** No documentation for TowFish-WAM-V2 serial communication protocol. UNKNOWN if telemetry link is functional.  
**Impact:** DEPLOYMENT BLOCKER - Cannot deploy TowFish with WAM-V 2 without verified telemetry. No way to receive magnetometer/depth data from TowFish during towed operations.  
**Verification:**  
1. Document serial protocol between TowFish and WAM-V 2  
2. Test physical serial connection (which port? baud rate?)  
3. Verify magnetometer/GPS data flows from TowFish to WAM-V 2  
4. Create field checkout procedure  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- TowFish: https://github.com/Marine-Robotics-Club/Thomas_Towfish/blob/main/RASP_V2.ino  
- WAM-V2: https://github.com/Marine-Robotics-Club/LowLevel_Wamv2/blob/main/ros2serial/src/serial2arduino.cpp  
- Audit: [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#deployment-blockers), [COMPREHENSIVE_AUDIT.md](audit/COMPREHENSIVE_AUDIT.md#towfish-integration)  

---

### AC-04: TowFish udev Rules Missing for Serial Port Persistence

**Priority:** CRITICAL  
**System:** TowFish  
**Source File:** TowFish system setup (no udev file found)  
**Issue:** TowFish Raspberry Pi uses `/dev/ttyACM0` but no udev rule ensures port stays consistent across reboots  
**Impact:** DEPLOYMENT BLOCKER - Serial port for TowFish-PCU link may change after reboot, breaking all telemetry  
**Verification:**  
1. Create udev rule: `/etc/udev/rules.d/99-towfish.rules`  
2. Map TowFish PCU to persistent name: `/dev/towfish_pcu`  
3. Update RASP_V2.ino to use persistent device name  
4. Test after multiple reboots  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- Audit: [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#deployment-blockers), [HARD_CODED_VALUES.md](audit/HARD_CODED_VALUES.md#towfish-rasp_v2ino)  

---

### AC-05: WAM-V 2 Magnetometer on TowFish Not Integrated into ROS

**Priority:** CRITICAL  
**System:** TowFish + WAM-V 2  
**Source File:** TowFish RASP_V2.ino, WAM-V2 ROS2 topics  
**Issue:** TowFish has LIS3MDL magnetometer, but no evidence of data being published to WAM-V 2 ROS2 topics  
**Impact:** DEPLOYMENT BLOCKER - Primary mission (magnetic survey) cannot be completed without magnetometer data reaching ROS2 for logging/analysis  
**Verification:**  
1. Verify magnetometer data in TowFish serial output  
2. Confirm WAM-V 2 parses and publishes magnetometer data to ROS2 topic  
3. Test `ros2 topic echo /towfish/magnetometer` (or equivalent)  
4. Verify data logging during towed run  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- TowFish: https://github.com/Marine-Robotics-Club/Thomas_Towfish/blob/main/RASP_V2.ino  
- Audit: [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#deployment-blockers), [COMPREHENSIVE_AUDIT.md](audit/COMPREHENSIVE_AUDIT.md#towfish-integration)  

---

### AC-06: WAM-V 1 Weather Station Parse Errors

**Priority:** CRITICAL  
**System:** WAM-V 1  
**Source File:** `LowLevel_Wamv1/ros2serial/src/serial2arduino.cpp` lines 123-130  
**Issue:** Weather station serial parse marked with "parse error repeated" comment  
**Impact:** DEPLOYMENT BLOCKER - Weather data (wind, pressure) unavailable. May be required for safe operations decisions.  
**Verification:**  
1. Debug weather station serial format  
2. Fix parse logic in serial2arduino.cpp  
3. Verify `ros2 topic echo /wamv1/weather` outputs valid data  
4. Test in field conditions  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- File: https://github.com/Marine-Robotics-Club/LowLevel_Wamv1/blob/main/ros2serial/src/serial2arduino.cpp#L123-L130  
- Audit: [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#deployment-blockers), [TODO_FIXME_INDEX.md](audit/TODO_FIXME_INDEX.md#wam-v-1)  

---

### AC-07: No udev Rules for Any System (WAM-V1, WAM-V2, RoboBoat)

**Priority:** CRITICAL  
**System:** Cross-System (WAM-V 1, WAM-V 2, RoboBoat)  
**Source File:** All serial2arduino.cpp files use `/dev/ttyACM0`, `/dev/ttyUSB0`, etc.  
**Issue:** No udev rules found in any repository. Serial ports are non-persistent.  
**Impact:** DEPLOYMENT BLOCKER - After reboot or hardware reconnect, serial ports may reassign, breaking all Arduino/sensor communication  
**Verification:**  
1. Create udev rules for all systems:  
   - WAM-V 1: `/dev/wamv1_pcu`, `/dev/wamv1_weather`  
   - WAM-V 2: `/dev/wamv2_pcu`  
   - RoboBoat: `/dev/roboboat_pcu`  
   - TowFish: `/dev/towfish_pcu`  
2. Document in NETWORK_AND_SERIAL_SETUP.md  
3. Test on all systems after reboot  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- Audit: [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#critical-issues), [COMPREHENSIVE_AUDIT.md](audit/COMPREHENSIVE_AUDIT.md#serial-port-management)  

---

### AC-08: TowFish GPS (UM482) Not Accessible from ROS2

**Priority:** CRITICAL  
**System:** TowFish + WAM-V 2  
**Source File:** TowFish RASP_V2.ino (UM482 read code present)  
**Issue:** TowFish has dedicated UM482 GPS, but UNKNOWN if GPS data is sent to WAM-V 2 or only logged locally  
**Impact:** DEPLOYMENT BLOCKER - Cannot geolocate TowFish magnetometer readings without TowFish GPS position in ROS2 logs  
**Verification:**  
1. Verify TowFish GPS data in serial output to WAM-V 2  
2. Confirm WAM-V 2 publishes TowFish GPS to ROS2 topic (e.g., `/towfish/gps`)  
3. Test `ros2 topic echo /towfish/gps`  
4. Verify GPS coordinates are reasonable (not null/zero)  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- TowFish: https://github.com/Marine-Robotics-Club/Thomas_Towfish/blob/main/RASP_V2.ino  
- Audit: [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#deployment-blockers)  

---

## HIGH Priority Actions

### AH-01: RoboBoat GPS Hardcoded to Wrong Coordinates

**Priority:** HIGH  
**System:** RoboBoat  
**Source File:** `roboboat_usv/roboboat_bringup/config/sensors.yaml` line 15  
**Issue:** GPS coordinates hardcoded to 26.1942°N, 80.1024°W (likely old test site)  
**Impact:** Autonomous navigation will use wrong origin. Station link may expect different coordinates.  
**Verification:**  
1. Update coordinates to current competition/test site  
2. Verify station shows correct RoboBoat position  
3. Test waypoint navigation  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- File: https://github.com/Marine-Robotics-Club/roboboat_usv/blob/main/roboboat_bringup/config/sensors.yaml#L15  
- Audit: [HARD_CODED_VALUES.md](audit/HARD_CODED_VALUES.md#roboboat)  

---

### AH-02: RoboBoat Serial Port Non-Persistent

**Priority:** HIGH  
**System:** RoboBoat  
**Source File:** `roboboat_usv/ros2serial/src/serial2arduino.cpp` line 8  
**Issue:** Uses `/dev/ttyACM0` without udev rule  
**Impact:** After reboot, serial port may change, breaking motor control  
**Verification:**  
1. Create udev rule for `/dev/roboboat_pcu`  
2. Update serial2arduino.cpp  
3. Test after reboot  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- File: https://github.com/Marine-Robotics-Club/roboboat_usv/blob/main/ros2serial/src/serial2arduino.cpp#L8  
- Audit: [HARD_CODED_VALUES.md](audit/HARD_CODED_VALUES.md#roboboat)  

---

### AH-03: WAM-V 1 GPS Hardcoded Coordinates

**Priority:** HIGH  
**System:** WAM-V 1  
**Source File:** `LowLevel_Wamv1/vehicle_state/config/state.yaml` lines 10-15  
**Issue:** GPS waypoints hardcoded (may be outdated)  
**Impact:** Waypoint navigation uses wrong coordinates  
**Verification:**  
1. Update GPS waypoints to current test site  
2. Test waypoint following  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- File: https://github.com/Marine-Robotics-Club/LowLevel_Wamv1/blob/main/vehicle_state/config/state.yaml#L10-L15  
- Audit: [HARD_CODED_VALUES.md](audit/HARD_CODED_VALUES.md#wam-v-1)  

---

### AH-04: WAM-V 1 Serial Port Non-Persistent

**Priority:** HIGH  
**System:** WAM-V 1  
**Source File:** `LowLevel_Wamv1/ros2serial/src/serial2arduino.cpp` line 10  
**Issue:** Uses `/dev/ttyACM0` without udev rule  
**Impact:** After reboot, PCU communication may break  
**Verification:**  
1. Create udev rule for `/dev/wamv1_pcu`  
2. Update serial2arduino.cpp  
3. Test after reboot  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- File: https://github.com/Marine-Robotics-Club/LowLevel_Wamv1/blob/main/ros2serial/src/serial2arduino.cpp#L10  
- Audit: [HARD_CODED_VALUES.md](audit/HARD_CODED_VALUES.md#wam-v-1)  

---

### AH-05: WAM-V 2 Serial Port Non-Persistent

**Priority:** HIGH  
**System:** WAM-V 2  
**Source File:** `LowLevel_Wamv2/ros2serial/src/serial2arduino.cpp` line 8  
**Issue:** Uses `/dev/ttyACM0` without udev rule  
**Impact:** After reboot, PCU and TowFish communication may break  
**Verification:**  
1. Create udev rule for `/dev/wamv2_pcu` and `/dev/towfish_pcu`  
2. Update serial2arduino.cpp  
3. Test after reboot  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- File: https://github.com/Marine-Robotics-Club/LowLevel_Wamv2/blob/main/ros2serial/src/serial2arduino.cpp#L8  
- Audit: [HARD_CODED_VALUES.md](audit/HARD_CODED_VALUES.md#wam-v-2)  

---

### AH-06: TowFish Baud Rate Mismatch Risk

**Priority:** HIGH  
**System:** TowFish  
**Source File:** RASP_V2.ino line 45 (115200 baud)  
**Issue:** TowFish serial at 115200, but UNKNOWN if WAM-V2 serial2arduino.cpp matches  
**Impact:** Telemetry data corruption or loss  
**Verification:**  
1. Verify WAM-V2 serial2arduino.cpp uses 115200 baud for TowFish port  
2. Test serial communication with oscilloscope/logic analyzer if needed  
3. Document in NETWORK_AND_SERIAL_SETUP.md  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- TowFish: https://github.com/Marine-Robotics-Club/Thomas_Towfish/blob/main/RASP_V2.ino#L45  
- Audit: [COMPREHENSIVE_AUDIT.md](audit/COMPREHENSIVE_AUDIT.md#serial-communication)  

---

### AH-07: TowFish Depth Sensor Not Validated

**Priority:** HIGH  
**System:** TowFish  
**Source File:** RASP_V2.ino (MS5837 depth sensor)  
**Issue:** Depth sensor code present, but UNKNOWN if calibrated or tested in ocean conditions  
**Impact:** Cannot verify TowFish tow depth during survey  
**Verification:**  
1. Test depth sensor in known depths (pool, shallow water)  
2. Verify depth readings in serial output  
3. Confirm depth data reaches WAM-V 2 ROS2  
4. Document calibration procedure  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- TowFish: https://github.com/Marine-Robotics-Club/Thomas_Towfish/blob/main/RASP_V2.ino  
- Audit: [COMPREHENSIVE_AUDIT.md](audit/COMPREHENSIVE_AUDIT.md#towfish-sensors)  

---

### AH-08: RoboBoat Station TCP Link Not Documented

**Priority:** HIGH  
**System:** RoboBoat + RoboBoat Station  
**Source File:** `roboboat_station/heartbeat.py` line 23  
**Issue:** Station connects to 10.10.10.1:50000 (TCP protobuf), but protocol not documented  
**Impact:** Cannot troubleshoot station link failures. New team members cannot understand system.  
**Verification:**  
1. Document TCP protobuf message format  
2. Add to NETWORK_AND_SERIAL_SETUP.md  
3. Create troubleshooting guide for station link  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- File: https://github.com/Marine-Robotics-Club/roboboat_station/blob/main/heartbeat.py#L23  
- Audit: [COMPREHENSIVE_AUDIT.md](audit/COMPREHENSIVE_AUDIT.md#roboboat-station)  

---

### AH-09: RoboBoat TODO: Add IMU Support

**Priority:** HIGH  
**System:** RoboBoat  
**Source File:** `roboboat_usv/ros2serial/src/serial2arduino.cpp` line 87  
**Issue:** Comment "TODO: Add IMU support" indicates missing IMU integration  
**Impact:** Degraded autonomy without IMU data (heading, angular velocity)  
**Verification:**  
1. Implement IMU parsing in serial2arduino.cpp  
2. Verify IMU data in ROS2 topic  
3. Test autonomous navigation with IMU  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- File: https://github.com/Marine-Robotics-Club/roboboat_usv/blob/main/ros2serial/src/serial2arduino.cpp#L87  
- Audit: [TODO_FIXME_INDEX.md](audit/TODO_FIXME_INDEX.md#roboboat)  

---

### AH-10: WAM-V 1 FIXME: Thruster Mapping Incorrect

**Priority:** HIGH  
**System:** WAM-V 1  
**Source File:** `LowLevel_Wamv1/WAMV_PCU_ROS2/WAMV_PCU_ROS2.ino` line 145  
**Issue:** Comment "FIXME: thruster 2 and 3 reversed?"  
**Impact:** Incorrect thruster control, vehicle goes wrong direction  
**Verification:**  
1. Test thruster directions with manual control  
2. Fix mapping in Arduino code  
3. Verify autonomous waypoint following  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- File: https://github.com/Marine-Robotics-Club/LowLevel_Wamv1/blob/main/WAMV_PCU_ROS2/WAMV_PCU_ROS2.ino#L145  
- Audit: [TODO_FIXME_INDEX.md](audit/TODO_FIXME_INDEX.md#wam-v-1)  

---

### AH-11: WAM-V 2 TODO: Test TowFish Cable Length Limits

**Priority:** HIGH  
**System:** WAM-V 2 + TowFish  
**Source File:** WAM-V2 mission planning docs (TODO noted in audit)  
**Issue:** No testing of maximum TowFish tow cable length and effects on stability  
**Impact:** May exceed safe cable length in field, causing TowFish loss or data corruption  
**Verification:**  
1. Test incrementally longer cable in controlled environment  
2. Measure data quality vs cable length  
3. Document maximum safe length  
4. Add to FIELD_VERIFICATION_PLAN.md  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- Audit: [TODO_FIXME_INDEX.md](audit/TODO_FIXME_INDEX.md#towfish-wam-v-2-integration)  

---

### AH-12: TowFish TODO: Add Battery Monitoring

**Priority:** HIGH  
**System:** TowFish  
**Source File:** RASP_V2.ino (no battery monitoring code found)  
**Issue:** No voltage monitoring for TowFish battery  
**Impact:** Cannot detect low battery, risk of data loss during survey  
**Verification:**  
1. Add voltage divider circuit for battery monitoring  
2. Implement battery voltage reading in RASP_V2.ino  
3. Send battery voltage in telemetry to WAM-V 2  
4. Set low voltage alarm threshold  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- Audit: [TODO_FIXME_INDEX.md](audit/TODO_FIXME_INDEX.md#towfish)  

---

### AH-13: No Documentation for New TowFish Packages

**Priority:** HIGH  
**System:** TowFish  
**Source File:** Multiple new Python packages in Thomas_Towfish repo  
**Issue:** New packages identified in audit: `towfish_viz`, `towfish_analysis`, `mag_calibration` - no documentation found  
**Impact:** Cannot use TowFish analysis tools. New students cannot learn system.  
**Verification:**  
1. Document each package purpose in systems/TowFish/README.md  
2. Add usage examples  
3. Link from NEW_STUDENT_START_HERE.md  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- Audit: [COMPREHENSIVE_AUDIT.md](audit/COMPREHENSIVE_AUDIT.md#undocumented-packages)  

---

### AH-14: WAM-V 1 IP Address Conflicts Possible

**Priority:** HIGH  
**System:** WAM-V 1  
**Source File:** Network config (inferred from code)  
**Issue:** No static IP documentation. Risk of IP conflicts between vehicles.  
**Impact:** Loss of control/telemetry during multi-vehicle operations  
**Verification:**  
1. Document static IP addresses for WAM-V 1, WAM-V 2, RoboBoat  
2. Add to NETWORK_AND_SERIAL_SETUP.md  
3. Verify no conflicts in field test  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- Audit: [COMPREHENSIVE_AUDIT.md](audit/COMPREHENSIVE_AUDIT.md#network-setup)  

---

### AH-15: Missing Field Emergency Procedures

**Priority:** HIGH  
**System:** Cross-System  
**Source File:** N/A (missing documentation)  
**Issue:** No emergency shutdown procedures found in any repository  
**Impact:** Safety risk during field operations  
**Verification:**  
1. Create emergency procedures document  
2. Include RC override, kill switch, and manual recovery  
3. Add to operations/ folder  
4. Brief all team members before field test  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- Audit: [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#high-priority)  

---

## MEDIUM Priority Actions

### AM-01: Document ROS2 Topic Structure

**Priority:** MEDIUM  
**System:** Cross-System  
**Source File:** Multiple launch files  
**Issue:** ROS2 topics are inferred from code but not centrally documented  
**Impact:** Difficult to debug or add new features  
**Verification:**  
1. Create ROS_TOPIC_REFERENCE.md  
2. List all topics for each system  
3. Include message types and sample data  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- Audit: [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#documentation-gaps)  

---

### AM-02: Create New Student Onboarding Guide

**Priority:** MEDIUM  
**System:** Cross-System  
**Source File:** N/A (missing)  
**Issue:** No single document for new students to understand full system  
**Impact:** Slow onboarding, repeated questions  
**Verification:**  
1. Create NEW_STUDENT_START_HERE.md  
2. Include system overview, quick start, and links to detailed docs  
3. Test with new team member  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- Audit: [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#documentation-gaps)  

---

### AM-03: Add Magnetometer Calibration Procedure

**Priority:** MEDIUM  
**System:** TowFish  
**Source File:** mag_calibration package (found but not documented)  
**Issue:** Magnetometer calibration process not documented  
**Impact:** Poor magnetic survey data quality  
**Verification:**  
1. Document calibration procedure in systems/TowFish/  
2. Include step-by-step instructions  
3. Test before field deployment  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- Audit: [COMPREHENSIVE_AUDIT.md](audit/COMPREHENSIVE_AUDIT.md#towfish-calibration)  

---

### AM-04: Document TowFish Data Logging Format

**Priority:** MEDIUM  
**System:** TowFish + WAM-V 2  
**Source File:** Data logging code (if exists)  
**Issue:** Data logging format and storage location not documented  
**Impact:** Difficulty analyzing survey results  
**Verification:**  
1. Document data log format (CSV? ROS bag?)  
2. Document where logs are stored  
3. Add to TOWFISH_WAMV2_INTEGRATION.md  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- Audit: [COMPREHENSIVE_AUDIT.md](audit/COMPREHENSIVE_AUDIT.md#data-logging)  

---

### AM-05: Create Pre-Field Checklist for TowFish+WAM-V2

**Priority:** MEDIUM  
**System:** TowFish + WAM-V 2  
**Source File:** N/A (missing)  
**Issue:** No checklist for pre-field inspection of TowFish-WAM-V2 integrated system  
**Impact:** Risk of field failures due to missed checks  
**Verification:**  
1. Create operations/TOWFISH_WAMV2_FIELD_CHECKLIST.md  
2. Include hardware checks, software checks, and communication tests  
3. Use in June field test  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- Audit: [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#field-operations)  

---

### AM-06: Document WAM-V 1 Bow Thruster Characteristics

**Priority:** MEDIUM  
**System:** WAM-V 1  
**Source File:** systems/WAM-V_1/ docs  
**Issue:** Bow thruster performance characteristics not documented  
**Impact:** Suboptimal mission planning  
**Verification:**  
1. Test bow thruster thrust curves  
2. Document in systems/WAM-V_1/hardware.md  
3. Add operating limits  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- Audit: [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#documentation-gaps)  

---

### AM-07: Add Battery Specs for All Systems

**Priority:** MEDIUM  
**System:** Cross-System  
**Source File:** Hardware docs  
**Issue:** Battery voltage, capacity, chemistry not documented  
**Impact:** Cannot plan mission duration or battery replacement  
**Verification:**  
1. Document battery specs for TowFish, WAM-V 1, WAM-V 2, RoboBoat  
2. Add to hardware.md for each system  
3. Include charging procedures  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- Audit: [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#documentation-gaps)  

---

### AM-08: Create GitHub Issue Templates

**Priority:** MEDIUM  
**System:** Repository Management  
**Source File:** .github/ISSUE_TEMPLATE/  
**Issue:** No issue templates in source repositories  
**Impact:** Inconsistent issue reporting  
**Verification:**  
1. Create issue templates in each source repo  
2. Include bug report, feature request, documentation templates  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- Audit: [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#repository-management)  

---

### AM-09: Add CI/CD for Firmware Builds

**Priority:** MEDIUM  
**System:** Cross-System  
**Source File:** Arduino .ino files  
**Issue:** No automated build testing for Arduino firmware  
**Impact:** Firmware errors not caught until field deployment  
**Verification:**  
1. Set up GitHub Actions to compile Arduino code  
2. Add to all repos with .ino files  
3. Verify PRs trigger builds  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- Audit: [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#repository-management)  

---

### AM-10: Document RoboBoat Competition Rule Changes

**Priority:** MEDIUM  
**System:** RoboBoat  
**Source File:** systems/RoboBoat/missions.md  
**Issue:** Mission docs may be outdated vs current competition rules  
**Impact:** System not compliant with current rules  
**Verification:**  
1. Review latest RoboBoat competition rules  
2. Update missions.md  
3. Identify any needed code changes  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- Audit: [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#competition-readiness)  

---

### AM-11: Add Power Consumption Measurements

**Priority:** MEDIUM  
**System:** Cross-System  
**Source File:** Hardware docs  
**Issue:** Power consumption not measured or documented  
**Impact:** Cannot accurately predict mission duration  
**Verification:**  
1. Measure current draw for each system under load  
2. Document in hardware.md  
3. Calculate mission endurance  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- Audit: [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#performance-metrics)  

---

### AM-12: Create Troubleshooting Flowcharts

**Priority:** MEDIUM  
**System:** Cross-System  
**Source File:** N/A (missing)  
**Issue:** No systematic troubleshooting guides  
**Impact:** Long debug times in field  
**Verification:**  
1. Create troubleshooting flowcharts for common issues  
2. Add to each system's docs  
3. Test in field scenario  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- Audit: [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#field-operations)  

---

### AM-13: Add Version Tags to Source Repos

**Priority:** MEDIUM  
**System:** Repository Management  
**Source File:** All source repos  
**Issue:** No version tags or releases  
**Impact:** Cannot track which code version was used in field tests  
**Verification:**  
1. Add git tags for major milestones  
2. Create GitHub releases  
3. Document versioning scheme  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- Audit: [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#repository-management)  

---

### AM-14: Document WiFi Network Setup

**Priority:** MEDIUM  
**System:** Cross-System  
**Source File:** NETWORK_AND_SERIAL_SETUP.md (to be created)  
**Issue:** WiFi setup for vehicle-to-shore link not documented  
**Impact:** Communication failures during operations  
**Verification:**  
1. Document WiFi SSID, channels, frequencies  
2. Add to NETWORK_AND_SERIAL_SETUP.md  
3. Test range in field  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- Audit: [COMPREHENSIVE_AUDIT.md](audit/COMPREHENSIVE_AUDIT.md#network-setup)  

---

### AM-15: Create Sensor Validation Test Suite

**Priority:** MEDIUM  
**System:** Cross-System  
**Source File:** Test scripts (to be created)  
**Issue:** No automated tests for sensor data validity  
**Impact:** Bad sensor data not detected until field deployment  
**Verification:**  
1. Create ROS2 test nodes to validate sensor data ranges  
2. Add to launch files  
3. Run before field operations  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- Audit: [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#software-quality)  

---

### AM-16: Add Code Comments for Complex Algorithms

**Priority:** MEDIUM  
**System:** Cross-System  
**Source File:** Multiple files with low comment density  
**Issue:** Some complex code sections lack comments  
**Impact:** Difficult for new developers to understand  
**Verification:**  
1. Review code for complex sections  
2. Add explanatory comments  
3. Focus on waypoint following and thruster control logic  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- Audit: [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#code-quality)  

---

### AM-17: Document Firmware Upload Procedures

**Priority:** MEDIUM  
**System:** Cross-System  
**Source File:** operations/ (to be created)  
**Issue:** No documented procedure for uploading new firmware to Arduinos  
**Impact:** Risk of bricking hardware during updates  
**Verification:**  
1. Document firmware upload steps for each PCU  
2. Include backup/rollback procedures  
3. Add to operations documentation  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- Audit: [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#operations-procedures)  

---

### AM-18: Add LICENSE Files to All Repos

**Priority:** MEDIUM  
**System:** Repository Management  
**Source File:** All source repos  
**Issue:** No LICENSE files found in source repositories  
**Impact:** Unclear legal status for code sharing/reuse  
**Verification:**  
1. Choose appropriate open source license (MIT? GPLv3?)  
2. Add LICENSE file to each repo  
3. Update README with license info  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- Audit: [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#repository-management)  

---

## LOW Priority Actions

### AL-01: Standardize Code Formatting

**Priority:** LOW  
**System:** Cross-System  
**Source File:** All code files  
**Issue:** Inconsistent code formatting across files  
**Impact:** Minor readability issues  
**Verification:**  
1. Choose style guide (Google C++, PEP 8 for Python)  
2. Add .clang-format and .pylintrc  
3. Run formatters  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- Audit: [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#code-quality)  

---

### AL-02: Add Doxygen Comments for C++ Code

**Priority:** LOW  
**System:** Cross-System  
**Source File:** C++ files  
**Issue:** No Doxygen-style documentation comments  
**Impact:** Auto-generated documentation not possible  
**Verification:**  
1. Add Doxygen comments to public functions  
2. Set up Doxygen build  
3. Generate HTML documentation  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- Audit: [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#code-quality)  

---

### AL-03: Create System Architecture Diagrams

**Priority:** LOW  
**System:** Cross-System  
**Source File:** systems/ docs  
**Issue:** No visual architecture diagrams  
**Impact:** Harder to understand system overview  
**Verification:**  
1. Create architecture diagrams (draw.io, PlantUML)  
2. Add to system docs  
3. Include in NEW_STUDENT_START_HERE.md  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- Audit: [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#documentation-quality)  

---

### AL-04: Add Unit Tests for ROS2 Nodes

**Priority:** LOW  
**System:** Cross-System  
**Source File:** ROS2 packages  
**Issue:** No unit tests found  
**Impact:** Regressions not caught automatically  
**Verification:**  
1. Add pytest or gtest unit tests  
2. Set up in CI/CD  
3. Aim for >50% code coverage  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- Audit: [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#software-quality)  

---

### AL-05: Improve README Formatting

**Priority:** LOW  
**System:** Repository Management  
**Source File:** All README.md files  
**Issue:** Some READMEs lack structure or are outdated  
**Impact:** Poor first impression for new contributors  
**Verification:**  
1. Review and reformat README files  
2. Add badges (build status, license)  
3. Ensure consistent structure  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- Audit: [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#documentation-quality)  

---

### AL-06: Add .gitignore for Build Artifacts

**Priority:** LOW  
**System:** Repository Management  
**Source File:** All source repos  
**Issue:** Some repos may not have comprehensive .gitignore  
**Impact:** Build artifacts accidentally committed  
**Verification:**  
1. Review .gitignore files  
2. Add ROS2 build/, install/, log/ patterns  
3. Add Arduino build patterns  
**Owner:** TBD  
**Status:** PENDING  
**Link to Source:**  
- Audit: [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#repository-management)  

---

## Summary by System

### TowFish
- Critical: 5 items (AC-03, AC-04, AC-05, AC-08, AH-12)  
- High: 3 items (AH-06, AH-07, AH-13)  
- Medium: 2 items (AM-03, AM-04)  
- Total: 10 items  

### WAM-V 2
- Critical: 7 items (AC-01, AC-02, AC-03, AC-05, AC-07, AC-08, AH-11)  
- High: 2 items (AH-05, AH-06)  
- Medium: 1 item (AM-05)  
- Total: 10 items  

### WAM-V 1
- Critical: 2 items (AC-06, AC-07)  
- High: 4 items (AH-03, AH-04, AH-10, AH-14)  
- Medium: 1 item (AM-06)  
- Total: 7 items  

### RoboBoat
- Critical: 1 item (AC-07)  
- High: 3 items (AH-01, AH-02, AH-09)  
- Medium: 2 items (AM-08, AM-10)  
- Total: 6 items  

### Cross-System
- Critical: 1 item (AC-07)  
- High: 1 item (AH-15)  
- Medium: 12 items (AM-01, AM-02, AM-07, AM-08, AM-09, AM-11, AM-12, AM-13, AM-14, AM-15, AM-16, AM-17, AM-18)  
- Low: 6 items (AL-01 through AL-06)  
- Total: 20 items  

---

## Next Steps

1. **Review and Prioritize:** Team review this document and adjust priorities based on June field test timeline  
2. **Assign Owners:** Assign each action item to a team member  
3. **Create Issues:** Convert CRITICAL and HIGH items to GitHub issues (use ISSUE_DRAFTS.md template)  
4. **Create Blockers Doc:** Copy all CRITICAL items to DEPLOYMENT_BLOCKERS.md  
5. **Create Field Plan:** Copy relevant items to FIELD_VERIFICATION_PLAN.md  
6. **Track Progress:** Update this document weekly with status changes  
7. **Pre-Field Meeting:** Review all CRITICAL items must be RESOLVED before June field test  

---

**Document Status:** COMPLETE - Ready for team review  
**Action Item Tracking:** All 47 items catalogued and linked to audit sources  
**Field Test Readiness:** 8 CRITICAL blockers identified - MUST resolve before deployment
