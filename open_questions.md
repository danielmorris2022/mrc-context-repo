# Open Questions & Known Unknowns

**Purpose:** Track all unresolved questions, undocumented details, suspected bugs, and items requiring field verification across all FAU MRC systems.
**Last Updated:** 2025-07

> Add new items as they are discovered. Mark items RESOLVED and link to the relevant doc when answered.

---

## TowFish

### High Priority

| # | Question | Source of Uncertainty | Priority |
|---|----------|-----------------------|----------|
| TF-1 | What is the **pressure sensor model and wiring**? The `pressure_mbar` field exists in the data CSV but no sensor is defined in `RASP_V2.ino`. | Firmware inspection | HIGH |
| TF-2 | **Unit bug in twinleaf_logger_ros.py**: `mag_field_microtesla` is assigned to `mag_field_tesla` without converting. What unit is actually published on `/twinleaf/mag_field`? | Code inspection | HIGH |
| TF-3 | **GPS topic source**: Does the WAM-V autopilot publish on `/drone/...` topics? What autopilot (ArduPilot / PX4 / MAVLink) runs on WAM-V 2? | Software inspection | HIGH |
| TF-4 | What are the **IP addresses** of the TowFish Raspberry Pi and the WAM-V 2 Jetson? Are they on the same ROS 2 DDS domain? | Not found in any repo | HIGH |
| TF-5 | Does `data_format.md` accurately describe the actual CSV output? The firmware CSV header has only 10 columns; the full mission CSV has 36+. Which firmware version produces the full CSV? | Discrepancy | HIGH |

### Medium Priority

| # | Question | Source | Priority |
|---|----------|--------|----------|
| TF-6 | What is the **Twinleaf magnetometer model** (VMR, TFM100, other)? | Not mentioned in code | MEDIUM |
| TF-7 | What is the **XBee model / band** used for TowFish telemetry? | Not in code | MEDIUM |
| TF-8 | What happens when `RASP_%03d.csv` reaches file 099? Does the firmware wrap or stop logging? | Firmware unclear | MEDIUM |
| TF-9 | What is the **Raspberry Pi model** on the TowFish? | Not documented | MEDIUM |
| TF-10 | Are there any **MATLAB analysis scripts** in the `Thomas_Towfish` repo beyond what's in `twinleaf_data/`? | Not fully inspected | MEDIUM |

---

## WAM-V 2

### High Priority

| # | Question | Source | Priority |
|---|----------|--------|----------|
| WV2-1 | What is the **Jetson model** on WAM-V 2 (Nano / Xavier / Orin)? | Not found | HIGH |
| WV2-2 | What is the **GPS reference coordinate** for FAU/local testing? The launch file uses VRX Sydney coordinates (`-33.7227654`, `150.6739916`) — must update before local use. | Code inspection | HIGH |
| WV2-3 | What is the **Torqeedo model** and baud rate for PCU serial? | Not documented | HIGH |
| WV2-4 | What is the **full Jetson-to-Teensy serial packet format** (`<...>` format)? | Only described briefly in firmware | HIGH |
| WV2-5 | Are `highlevel` and `Mission_Control` repos deployed on WAM-V 2 or WAM-V 1? | Repo descriptions ambiguous | HIGH |

### Medium Priority

| # | Question | Source | Priority |
|---|----------|--------|----------|
| WV2-6 | What LIDAR is on WAM-V 2? The `lidar_msgs` package exists but the device is UNKNOWN. | Package name only | MEDIUM |
| WV2-7 | Are the spare R/C PW channels (pins 10–13) actively used for anything? | Defined as spare in firmware | MEDIUM |
| WV2-8 | What is the **RoboteQ motor controller model** on WAM-V 2? | Not documented | MEDIUM |

---

## WAM-V 1

### High Priority

| # | Question | Source | Priority |
|---|----------|--------|----------|
| WV1-1 | Is the **VLP-16 LiDAR** still mounted on WAM-V 1? What are its ROS topics? | Only `Vision` repo exists, not inspected | HIGH |
| WV1-2 | Are the bow thrusters **electrically separate** from the Torqeedo thrusters? What motor controller drives them? | Not documented | HIGH |
| WV1-3 | Is the **Digi RF Modem** (Serial6) currently used for any mission function? | Defined but purpose unclear | MEDIUM |

### Medium Priority

| # | Question | Source | Priority |
|---|----------|--------|----------|
| WV1-4 | Does the `highlevel` repo (Jetson Orin AGX) work with WAM-V 1, WAM-V 2, or both? | Description says "robotX wamv" | MEDIUM |
| WV1-5 | What is the **wiring harness** for AIRMAR 110WX to Teensy Serial2? | Not documented | MEDIUM |

---

## RoboBoat ASV

### High Priority

| # | Question | Source | Priority |
|---|----------|--------|----------|
| RB-1 | What is the **vehicle hull** for RoboBoat? Is it a WAM-V hull, a pontoon, or something else? | Not found in repo | HIGH |
| RB-2 | What **thruster system** does the RoboBoat ASV use? | No firmware in `roboboat_usv` | HIGH |
| RB-3 | What **sensors** are in `asv_perception`? (Camera? LIDAR? Sonar?) | Not inspected | HIGH |
| RB-4 | What is the **companion computer** (RPi 4, Jetson Nano, Jetson Orin)? | Not documented | HIGH |
| RB-5 | What is the **acoustic task system** in `asv_audio`? (Hydrophone model? Frequency?) | Not inspected | HIGH |

### Medium Priority

| # | Question | Source | Priority |
|---|----------|--------|----------|
| RB-6 | What ROS 2 topics does `ros2serial_py` bridge to the embedded MCU? | Not inspected | MEDIUM |
| RB-7 | How does `rl_msgs` relate to the RL planner in `LowLevel_Wamv2`? Are they the same? | Package name only | MEDIUM |
| RB-8 | What is in `roboboat_station` and `roboboat_ws`? | Not inspected | MEDIUM |

---

## Cross-System / Infrastructure

| # | Question | Source | Priority |
|---|----------|--------|----------|
| INF-1 | What **ROS 2 DDS configuration** (domain ID, discovery) connects TowFish to WAM-V 2? | Not found | HIGH |
| INF-2 | What **network configuration** (IP addresses, router, WiFi SSID) is used at FAU for field operations? | Not documented | HIGH |
| INF-3 | Is there a **network diagram** anywhere for the full system? | Not found | HIGH |
| INF-4 | Is there a functional `asv-system-docs` repo with more detail than what was found? | Not fully inspected | MEDIUM |
| INF-5 | What are the remaining ~20 GitHub repos in the org that weren't inspected? | Only partial org enumeration | LOW |

---

## Known Bugs

| # | Bug | System | File | Priority |
|---|-----|--------|------|----------|
| BUG-1 | `mag_field_microtesla` assigned to `mag_field_tesla` without conversion. Published unit is likely microtesla, not tesla. | TowFish | `twinleaf_logger_ros.py` | HIGH |
| BUG-2 | TowFish firmware CSV header has only 10 columns but full mission CSV has 36+ columns — version mismatch or secondary data assembly script. | TowFish | `RASP_V2.ino` | HIGH |
| BUG-3 | WAM-V 2 `vehicle_state.launch.py` references VRX Sydney GPS coordinates by default — wrong for FAU local testing. Must be updated. | WAM-V 2 | `vehicle_state.launch.py` | HIGH |
| BUG-4 | Light tower ROS subscription is not implemented in WAM-V 1/2 firmware. Comment says: `// TO DO: Add ROS subscription for light tower control.` | WAM-V 1 & 2 | `WAMV_PCU_ROS2.ino` | MEDIUM |
| BUG-5 | TowFish SD file numbering caps at `RASP_099.csv` — no rollover or warning implemented. | TowFish | `RASP_V2.ino` | LOW |

---

## Recommended Next Steps

1. **Field verify TowFish unit bug (BUG-1):** Connect Twinleaf magnetometer, log data, compare published `/twinleaf/mag_field` with the raw value. Check Twinleaf device datasheet for output format.
2. **Update WAM-V 2 GPS reference (BUG-3):** Before any Florida testing, edit `state.yaml` with correct local coordinates.
3. **Inspect `roboboat_usv/src/asv_perception`** to identify sensor pipeline (RB-3).
4. **Inspect `roboboat_usv/src/asv_audio`** to identify acoustic hardware (RB-5).
5. **Document the Jetson-to-Teensy serial packet format** for WAM-V 2 (WV2-4).
6. **Inspect `Vision` and `highlevel` repos** to complete WAM-V 1 documentation.
7. **Create `integration/towfish_wamv2.md`** to document how TowFish rides WAM-V 2 during magnetic surveys.

---

## How to Update This File

When a question is answered:
1. Mark the row as **RESOLVED** and add a link: `RESOLVED — see [file.md](./path/to/file.md)`
2. Add the answer to the appropriate system documentation file
3. Update the confidence level in that file if needed
