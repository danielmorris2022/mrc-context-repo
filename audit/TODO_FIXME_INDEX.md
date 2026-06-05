# TODO / FIXME Index

**Purpose:** Catalog of all TODO, FIXME, and code quality issues found in firmware and source code.
**Last Updated:** 2026-06-05
**Confidence:** HIGH — Extracted directly from source code

---

## WAM-V 2 PCU Firmware

**File:** [`LowLevel_Wamv2/src/teensyRos2/WAMV_PCU_ROS2/WAMV_PCU_ROS2.ino`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2/blob/main/src/teensyRos2/WAMV_PCU_ROS2/WAMV_PCU_ROS2.ino)

```cpp
// TO DO: Add ROS subscription for light tower control. Use 3 std_msgs/UInt16
```

**Impact:** Light tower control not implemented. WAM-V 2 cannot control light tower via ROS.

---

## WAM-V 1 PCU Firmware

**File:** [`LowLevel_Wamv1/src/teensyRos2/WAMV_PCU_ROS2/WAMV_PCU_ROS2.ino`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv1/blob/main/src/teensyRos2/WAMV_PCU_ROS2/WAMV_PCU_ROS2.ino)

**Code Quality Issue:**
```cpp
// definitely not accurate or working right but the ROS works lol
//getWindData(windSpeed, windDirection);
//getTemperatureAndPressure(temperature, pressure);
```

**Impact:** Weather station (Airmar 110WX) data parsing is broken but not fixed. Wind/temperature/pressure data are NOT being read correctly.

**TODO:**
```cpp
/* 
 TO DO:
 Add ROS subscription for light tower control. Use 3 std_msgs/UInt16
*/
```

---

## TowFish Firmware

**File:** [`Thomas_Towfish/src/RASP_V2/RASP_V2.ino`](https://github.com/Marine-Robotics-Club/Thomas_Towfish/blob/main/src/RASP_V2/RASP_V2.ino)

**NOT IMPLEMENTED:**
```cpp
//delete specific file - NOT IMPLEMENTED
//delete all files - NOT IMPLEMENTED
```

**Impact:** SD card file deletion via serial commands not implemented. Must manually remove SD card to clear data.

**Optimization Note:**
```cpp
//NEED TO ADD SOME DELAY AFTER n BYTES TO ALLOW RADIO SOME TIME TO TRANSMIT PACKET...
//just picked 10ms, not sure but seems to work...
```

**Impact:** Radio transmission timing is guessed, not calculated. May cause data loss at high rates.

---

## Summary

**Total TODO/FIXME items found:** 5

**Priority Ranking:**
1. **HIGH:** WAM-V 1 weather station broken ("not accurate or working right")
2. **MEDIUM:** TowFish file deletion not implemented
3. **LOW:** Light tower control not implemented (both WAM-Vs)
4. **LOW:** TowFish radio timing optimization needed

**Recommended Next Actions:**
1. Fix WAM-V 1 weather station parsing immediately if weather data is mission-critical.
2. Implement TowFish file deletion or document manual SD card removal procedure.
3. Clarify whether light tower control is required for operations or if it can remain unimplemented.
