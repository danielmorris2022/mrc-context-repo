# Deployment Blockers - CRITICAL Issues

**Title:** Deployment Blockers - CRITICAL Issues  
**Purpose:** Track all CRITICAL priority items that block June field deployment. These MUST be resolved before any ocean testing with TowFish, WAM-V 2, WAM-V 1, or RoboBoat.  
**Last Updated:** 2026-06-05  
**Source:** [ACTION_ITEMS.md](ACTION_ITEMS.md)  
**Confidence:** HIGH (extracted from comprehensive audit)  

---

## ⚠️ DEPLOYMENT BLOCKER SUMMARY

**Total CRITICAL Blockers:** 8  
**Affected Systems:**  
- **TowFish + WAM-V 2:** 5 blockers (AC-03, AC-04, AC-05, AC-08, and shared AC-07)  
- **WAM-V 2:** 3 blockers (AC-01, AC-02, shared AC-07)  
- **WAM-V 1:** 2 blockers (AC-06, shared AC-07)  
- **Cross-System:** 1 blocker (AC-07)  

**DEPLOYMENT STATUS:** 🛑 **NOT READY** - 8 critical issues unresolved  

**Target Resolution Date:** Before June field test  
**Next Review:** Weekly team meeting  

---

## Critical Action Required

**NO SYSTEM MAY BE DEPLOYED TO THE OCEAN UNTIL ALL ITEMS BELOW ARE MARKED RESOLVED.**

Each blocker below must have:
1. ✅ **Fix implemented** in code/config  
2. ✅ **Tested** in lab or pool  
3. ✅ **Verified** by independent team member  
4. ✅ **Documented** with verification evidence  

---

## AC-01: WAM-V 2 VRX Simulation Mode Enabled [BLOCKER]

**System:** WAM-V 2  
**Impact:** ⛔ DEPLOYMENT BLOCKER - System will fail on actual hardware  
**Issue:** `vrx_simulation: true` in production config  
**Source File:** `LowLevel_Wamv2/vehicle_state2/config/state.yaml` line 3  

### Why This Blocks Deployment
WAM-V 2 will expect VRX simulation environment (Gazebo) and will not function with real sensors. GPS coordinates are set to Sydney, Australia test site instead of Florida. System will not respond correctly to real hardware inputs.

### Fix Required
1. Set `vrx_simulation: false` in state.yaml  
2. Replace Sydney GPS coordinates with Florida test site  
3. Verify all sensor inputs use real hardware (not simulated topics)  
4. Test startup on actual WAM-V 2 hardware  

### Verification Checklist
- [ ] state.yaml updated with `vrx_simulation: false`  
- [ ] GPS coordinates updated to Florida test site  
- [ ] System boots successfully on WAM-V 2 hardware  
- [ ] All sensors publish data (GPS, IMU, thrusters respond)  
- [ ] Code changes committed to repository  
- [ ] Verification test results documented  

**Owner:** TBD  
**Status:** 🔴 PENDING  
**Link:** [ACTION_ITEMS.md AC-01](ACTION_ITEMS.md#ac-01-wam-v-2-vrx-simulation-mode-enabled-in-production-config)  
**Audit Source:** [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#deployment-blockers), [HARD_CODED_VALUES.md](audit/HARD_CODED_VALUES.md#wam-v-2-vehicle_state2configstateyaml)  

---

## AC-02: WAM-V 2 GPS Coordinates Point to Australia [BLOCKER]

**System:** WAM-V 2  
**Impact:** ⛔ DEPLOYMENT BLOCKER - Autonomous navigation will fail catastrophically  
**Issue:** GPS waypoints hardcoded to Sydney (151.22° E longitude)  
**Source File:** `LowLevel_Wamv2/vehicle_state2/config/state.yaml` lines 8-13  

### Why This Blocks Deployment
WAM-V 2 autonomous waypoint following will command vehicle to navigate to Australia (~9,000 miles away). This will cause:
- Immediate navigation failure (waypoints not reachable)
- Potential loss of vehicle if failsafes don't engage
- Invalid mission planning data

### Fix Required
1. Replace ALL Sydney coordinates with Florida test site GPS  
2. Verify waypoint distances are reasonable (< 1km radius)  
3. Add sanity check: reject waypoints > 10km from launch point  
4. Test waypoint following in simulation with correct coordinates  

### Verification Checklist
- [ ] All waypoints updated to Florida coordinates  
- [ ] Waypoint distances verified < 1km from launch point  
- [ ] Simulation test with correct GPS coordinates successful  
- [ ] Sanity check code added for waypoint validation  
- [ ] Code changes committed to repository  
- [ ] Verification test results documented  

**Owner:** TBD  
**Status:** 🔴 PENDING  
**Link:** [ACTION_ITEMS.md AC-02](ACTION_ITEMS.md#ac-02-wam-v-2-gps-coordinates-hardcoded-to-sydney-australia)  
**Audit Source:** [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#deployment-blockers), [HARD_CODED_VALUES.md](audit/HARD_CODED_VALUES.md#wam-v-2-vehicle_state2configstateyaml)  

---

## AC-03: TowFish-WAM-V2 Telemetry Link Not Documented/Tested [BLOCKER]

**System:** TowFish + WAM-V 2  
**Impact:** ⛔ DEPLOYMENT BLOCKER - Cannot deploy TowFish without verified telemetry  
**Issue:** No documentation or evidence of working serial communication between TowFish and WAM-V 2  
**Source Files:** TowFish `RASP_V2.ino`, WAM-V2 `serial2arduino.cpp`  

### Why This Blocks Deployment
Primary mission is magnetic survey with TowFish. Without verified telemetry:
- No way to receive magnetometer data from TowFish  
- No GPS position data from TowFish  
- No depth sensor data from TowFish  
- Cannot verify TowFish operational status during tow  
- Data collection mission will fail completely  

### Fix Required
1. Document serial protocol: message format, baud rate, connection pins  
2. Verify physical serial connection (which WAM-V2 port connects to TowFish?)  
3. Test data flow: TowFish → Serial → WAM-V2 → ROS2 topics  
4. Implement error detection/recovery for serial link  
5. Create field checkout procedure for telemetry verification  

### Verification Checklist
- [ ] Serial protocol documented (baud, format, pins)  
- [ ] Physical connection identified and tested  
- [ ] Magnetometer data confirmed in WAM-V2 ROS2 topics  
- [ ] GPS data confirmed in WAM-V2 ROS2 topics  
- [ ] Depth data confirmed in WAM-V2 ROS2 topics  
- [ ] Serial link tested with simulated disconnection/reconnection  
- [ ] Field checkout procedure created and tested  
- [ ] Documentation added to TOWFISH_WAMV2_INTEGRATION.md  

**Owner:** TBD  
**Status:** 🔴 PENDING  
**Link:** [ACTION_ITEMS.md AC-03](ACTION_ITEMS.md#ac-03-towfish-wam-v2-serial-telemetry-integration-not-documented)  
**Audit Source:** [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#deployment-blockers), [COMPREHENSIVE_AUDIT.md](audit/COMPREHENSIVE_AUDIT.md#towfish-integration)  

---

## AC-04: TowFish udev Rules Missing - Serial Port Will Break [BLOCKER]

**System:** TowFish  
**Impact:** ⛔ DEPLOYMENT BLOCKER - Telemetry will fail after reboot  
**Issue:** TowFish uses `/dev/ttyACM0` without udev rule - port name will change  
**Source:** TowFish Raspberry Pi system setup  

### Why This Blocks Deployment
After reboot or hardware reconnect:
- Serial port may change from `/dev/ttyACM0` to `/dev/ttyACM1` or `/dev/ttyUSB0`  
- TowFish code will fail to open serial port  
- All telemetry to WAM-V 2 will stop  
- Mission data collection impossible  
- May not be discovered until at ocean (too late to fix)  

### Fix Required
1. Create udev rule: `/etc/udev/rules.d/99-towfish.rules`  
2. Map TowFish PCU to persistent name: `/dev/towfish_pcu`  
3. Update RASP_V2.ino to use `/dev/towfish_pcu` instead of `/dev/ttyACM0`  
4. Test persistence across multiple reboots  
5. Document udev setup in system documentation  

### Verification Checklist
- [ ] udev rule file created with correct USB VID/PID  
- [ ] RASP_V2.ino updated to use persistent device name  
- [ ] Test: reboot 5 times, verify port name stays consistent  
- [ ] Test: disconnect/reconnect USB, verify port name stays consistent  
- [ ] udev rule documented in NETWORK_AND_SERIAL_SETUP.md  
- [ ] Setup script created for deploying udev rule to new systems  

**Owner:** TBD  
**Status:** 🔴 PENDING  
**Link:** [ACTION_ITEMS.md AC-04](ACTION_ITEMS.md#ac-04-towfish-udev-rules-missing-for-serial-port-persistence)  
**Audit Source:** [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#deployment-blockers), [HARD_CODED_VALUES.md](audit/HARD_CODED_VALUES.md#towfish-rasp_v2ino)  

---

## AC-05: TowFish Magnetometer Data Not Reaching ROS2 [BLOCKER]

**System:** TowFish + WAM-V 2  
**Impact:** ⛔ DEPLOYMENT BLOCKER - Primary mission (magnetic survey) impossible  
**Issue:** LIS3MDL magnetometer on TowFish, but no evidence of data in WAM-V 2 ROS2  
**Source Files:** TowFish `RASP_V2.ino`, WAM-V2 ROS2 topics  

### Why This Blocks Deployment
The entire purpose of deploying TowFish is to conduct magnetic surveys:
- Magnetometer is the PRIMARY sensor for the mission  
- Without magnetometer data in ROS2, no data logging possible  
- Cannot analyze survey results  
- Mission objective completely fails  

### Fix Required
1. Verify magnetometer data appears in TowFish serial output  
2. Verify WAM-V 2 serial2arduino.cpp parses magnetometer messages  
3. Verify WAM-V 2 publishes magnetometer to ROS2 topic (e.g., `/towfish/magnetometer`)  
4. Test `ros2 topic echo /towfish/magnetometer` shows live data  
5. Verify magnetometer data is logged to ROS2 bag files  
6. Test data quality during motion (towing simulation)  

### Verification Checklist
- [ ] Magnetometer readings visible in TowFish serial monitor  
- [ ] WAM-V 2 parses magnetometer messages (verify with debug output)  
- [ ] `ros2 topic echo /towfish/magnetometer` shows live data  
- [ ] Magnetometer data included in rosbag recordings  
- [ ] Data quality verified during motion test  
- [ ] Calibration procedure tested and documented  
- [ ] Documentation added to TOWFISH_WAMV2_INTEGRATION.md  

**Owner:** TBD  
**Status:** 🔴 PENDING  
**Link:** [ACTION_ITEMS.md AC-05](ACTION_ITEMS.md#ac-05-wam-v-2-magnetometer-on-towfish-not-integrated-into-ros)  
**Audit Source:** [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#deployment-blockers), [COMPREHENSIVE_AUDIT.md](audit/COMPREHENSIVE_AUDIT.md#towfish-integration)  

---

## AC-06: WAM-V 1 Weather Station Parse Errors [BLOCKER]

**System:** WAM-V 1  
**Impact:** ⛔ DEPLOYMENT BLOCKER - Weather data unavailable for safety decisions  
**Issue:** Weather station serial parse failing ("parse error repeated" comment in code)  
**Source File:** `LowLevel_Wamv1/ros2serial/src/serial2arduino.cpp` lines 123-130  

### Why This Blocks Deployment
Weather data (wind speed, direction, pressure) is critical for:
- Safe operation decisions (abort mission in high winds)  
- Data quality assessment (correlate sensor noise with sea state)  
- Post-mission analysis  
- May be required by safety protocols  

### Fix Required
1. Debug weather station serial format (capture raw data)  
2. Fix parse logic in serial2arduino.cpp  
3. Verify `ros2 topic echo /wamv1/weather` outputs valid data  
4. Test under various weather conditions  
5. Add error handling for corrupt weather data  

### Verification Checklist
- [ ] Weather station serial format documented  
- [ ] Parse logic fixed and tested with known-good data  
- [ ] `ros2 topic echo /wamv1/weather` shows valid readings  
- [ ] Wind speed, direction, pressure all parsing correctly  
- [ ] Error handling tested with disconnected sensor  
- [ ] Field test with actual weather station in various conditions  
- [ ] Code changes committed to repository  

**Owner:** TBD  
**Status:** 🔴 PENDING  
**Link:** [ACTION_ITEMS.md AC-06](ACTION_ITEMS.md#ac-06-wam-v-1-weather-station-parse-errors)  
**Audit Source:** [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#deployment-blockers), [TODO_FIXME_INDEX.md](audit/TODO_FIXME_INDEX.md#wam-v-1)  

---

## AC-07: No udev Rules for ANY System [BLOCKER]

**System:** Cross-System (WAM-V 1, WAM-V 2, RoboBoat, TowFish)  
**Impact:** ⛔ DEPLOYMENT BLOCKER - ALL systems will fail after reboot  
**Issue:** No udev rules in any repository - all serial ports are non-persistent  
**Source:** All `serial2arduino.cpp` files use `/dev/ttyACM0`, `/dev/ttyUSB0`, etc.  

### Why This Blocks Deployment
This is a UNIVERSAL failure mode affecting ALL vehicles:
- After any reboot or hardware reconnection, serial ports may reassign  
- Arduino/PCU communication breaks for ALL systems  
- Thrusters, sensors, telemetry all fail simultaneously  
- Vehicles become uncontrollable  
- Issue may appear intermittently (only after reboot), making field debugging impossible  

### Fix Required
1. Create udev rules for EVERY system:  
   - **WAM-V 1:** `/dev/wamv1_pcu`, `/dev/wamv1_weather`  
   - **WAM-V 2:** `/dev/wamv2_pcu`, `/dev/towfish_pcu`  
   - **RoboBoat:** `/dev/roboboat_pcu`  
   - **TowFish:** `/dev/towfish_pcu` (Raspberry Pi side)  
2. Update ALL serial2arduino.cpp files with persistent names  
3. Test EVERY system after multiple reboots  
4. Document in NETWORK_AND_SERIAL_SETUP.md  
5. Create deployment script to install udev rules on all systems  

### Verification Checklist
- [ ] udev rules created for all 4 systems  
- [ ] serial2arduino.cpp updated on all systems  
- [ ] WAM-V 1 tested with 5 reboots  
- [ ] WAM-V 2 tested with 5 reboots  
- [ ] RoboBoat tested with 5 reboots  
- [ ] TowFish tested with 5 reboots  
- [ ] Disconnect/reconnect test for all systems  
- [ ] Deployment script created and tested  
- [ ] Documentation complete in NETWORK_AND_SERIAL_SETUP.md  

**Owner:** TBD  
**Status:** 🔴 PENDING  
**Link:** [ACTION_ITEMS.md AC-07](ACTION_ITEMS.md#ac-07-no-udev-rules-for-any-system-wam-v1-wam-v2-roboboat)  
**Audit Source:** [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#critical-issues), [COMPREHENSIVE_AUDIT.md](audit/COMPREHENSIVE_AUDIT.md#serial-port-management)  

---

## AC-08: TowFish GPS Not Accessible from ROS2 [BLOCKER]

**System:** TowFish + WAM-V 2  
**Impact:** ⛔ DEPLOYMENT BLOCKER - Cannot geolocate magnetometer readings  
**Issue:** TowFish UM482 GPS not confirmed to reach WAM-V 2 ROS2  
**Source File:** TowFish `RASP_V2.ino` (UM482 code present)  

### Why This Blocks Deployment
Magnetic survey data is useless without position information:
- Cannot geolocate magnetometer readings  
- Cannot create magnetic field map  
- Cannot correlate findings with physical locations  
- Survey results cannot be analyzed or used  
- Primary mission objective fails  

### Fix Required
1. Verify TowFish GPS data in serial output to WAM-V 2  
2. Verify WAM-V 2 publishes TowFish GPS to ROS2 topic (e.g., `/towfish/gps`)  
3. Test `ros2 topic echo /towfish/gps`  
4. Verify GPS coordinates are reasonable (not null/zero/invalid)  
5. Verify GPS data is synchronized with magnetometer data in logs  
6. Test GPS accuracy (compare with WAM-V 2 GPS)  

### Verification Checklist
- [ ] TowFish GPS data visible in serial monitor  
- [ ] WAM-V 2 parses TowFish GPS messages  
- [ ] `ros2 topic echo /towfish/gps` shows valid coordinates  
- [ ] GPS coordinates match expected location (not null/zero)  
- [ ] GPS timestamp synchronized with magnetometer data  
- [ ] GPS accuracy tested (compare with WAM-V 2 GPS)  
- [ ] GPS data included in rosbag recordings  
- [ ] Documentation added to TOWFISH_WAMV2_INTEGRATION.md  

**Owner:** TBD  
**Status:** 🔴 PENDING  
**Link:** [ACTION_ITEMS.md AC-08](ACTION_ITEMS.md#ac-08-towfish-gps-um482-not-accessible-from-ros2)  
**Audit Source:** [AUDIT_REPORT.md](audit/AUDIT_REPORT.md#deployment-blockers)  

---

## Resolution Workflow

For each blocker, follow this process:

### 1. Assignment
- [ ] Assign owner to blocker  
- [ ] Set target completion date  
- [ ] Add blocker to team kanban board  

### 2. Implementation
- [ ] Implement fix in code/config  
- [ ] Self-test by developer  
- [ ] Code review by second team member  
- [ ] Commit changes to repository  

### 3. Verification
- [ ] Independent verification by different team member  
- [ ] Lab/pool test if applicable  
- [ ] Document test results (photos, videos, logs)  
- [ ] Update verification checklist  

### 4. Documentation
- [ ] Update system documentation  
- [ ] Add troubleshooting notes  
- [ ] Update relevant integration guides  

### 5. Sign-Off
- [ ] Owner confirms fix complete  
- [ ] Verifier confirms testing passed  
- [ ] Team lead approves resolution  
- [ ] Update status to 🟢 RESOLVED  

---

## Pre-Deployment Final Check

Before ANY system goes to the ocean:

### Go/No-Go Checklist
- [ ] ALL 8 critical blockers marked RESOLVED  
- [ ] All fixes tested in lab/pool environment  
- [ ] All documentation updated  
- [ ] Team review meeting completed  
- [ ] Emergency procedures briefed to all team members  
- [ ] Field test plan approved  

**Deployment Decision:** ⬜ GO / ⬜ NO-GO  
**Authorized By:** _________________ Date: _______  

---

## Contact & Escalation

**Document Owner:** TBD  
**Update Frequency:** Daily during blocker resolution phase  
**Escalation Path:** If blocker cannot be resolved → Team lead → Faculty advisor  

**Status as of 2026-06-05:** 🛑 **NOT READY FOR DEPLOYMENT** - 8 critical issues pending  

---

**Next Actions:**
1. Team meeting to assign owners to each blocker  
2. Create detailed fix plan for AC-01 and AC-02 (highest risk)  
3. Set up daily standup to track blocker resolution progress  
4. Establish field test contingency date (backup plan if blockers not resolved)
