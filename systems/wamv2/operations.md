# WAM-V 2 Operations

**Purpose:** Field operation procedures, pre-deployment checklist, known issues, and troubleshooting for WAM-V 2.
**Last Updated:** 2026-06-05
**Source Repos:** [LowLevel_Wamv2](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2), firmware inspection
**Confidence:** MEDIUM — based on firmware logic and code comments; actual SOP not found in any repo

> **Note:** No formal SOP document was found in any source repository. Procedures below are INFERRED from firmware, launch files, and code comments. Field personnel should verify before use.

---

## Startup Procedure (INFERRED)

### 1. Pre-Power Checks
- [ ] Verify battery connections and levels (MAIN_BATTERY_SENSE must read nominal)
- [ ] Check all serial device connections (weather station, sonar, RC receiver, Jetson)
- [ ] Ensure kill switch is in KILL position before powering
- [ ] Confirm RC transmitter is on and in MANUAL mode

### 2. Power On Sequence
- [ ] Apply main battery power
- [ ] Power on Jetson (CPU_PWR_EN will be asserted by Teensy)
- [ ] Wait for Teensy to boot and begin serial communications
- [ ] Verify light tower responds (indicates Teensy firmware running)

### 3. ROS Stack Startup
```bash
# Source workspace
source /home/highlevel/wamv_nav/install/setup.bash

# Launch outdoor field stack (FAU field ops)
ros2 launch fau fau_outdoor.launch.py
```

> **WARNING:** Do NOT use `fau_vrx2.launch.py` for field ops — it uses VRX simulation GPS coordinates.
> **WARNING (BUG-3):** Do NOT use `vehicle_state.launch.py` standalone — it has hardcoded VRX Sydney coords.

### 4. Mode Switching

| Action | RC Transmitter | Result |
|--------|---------------|--------|
| Enable Manual | Set RC ACTIVE switch LOW | Teensy uses SBUS input directly |
| Enable Autonomous | Set RC ACTIVE switch HIGH | Teensy forwards Jetson commands |
| Kill | Activate kill switch | All thrusters to neutral (1500 μs) |

---

## Rosbag Recording

A rosbag directory exists in the source tree:
```
src/fau2/launch/rosbag2_2024_06_05_12_07_52/
```
This suggests rosbag recording is part of the field ops workflow. Recording command:
```bash
ros2 bag record -a -o /path/to/output_dir
```
Recording all topics is typical for post-mission analysis. Specific topics to record: UNKNOWN — open question OPS-1.

---

## GPS Reference Verification

Before each field op, verify `vehicle_state_node` is using correct coordinates:

| Parameter | Expected (FAU Field) | Source |
|-----------|---------------------|--------|
| latRef | 26.055513176834214 | `fau_outdoor.launch.py` |
| lonRef | -80.11413939041087 | `fau_outdoor.launch.py` |

---

## Known Issues / Bugs

| ID | Severity | Description | Workaround |
|----|----------|-------------|------------|
| BUG-3 | HIGH | `vehicle_state.launch.py` has hardcoded VRX Sydney coordinates | Always use `fau_outdoor.launch.py` for field ops |
| BUG-4 | MEDIUM | Light tower ROS subscription not implemented in firmware | Light tower not ROS-controllable — manual only |
| WV2-4 | LOW | Teensy telemetry packet format not documented | Inspect `send_jetson()` function in firmware |

---

## TowFish Operations

WAM-V 2 serves as the tow vehicle for the TowFish magnetometer system. Additional procedures:

- [ ] Deploy TowFish tether before vehicle launch
- [ ] Verify TowFish ROS topics are receiving data
- [ ] Confirm WAM-V 2 speed is appropriate for TowFish survey (UNKNOWN — open question TF-2)
- [ ] See [TowFish Operations](../towfish/operations.md) for TowFish-specific procedures

---

## Troubleshooting

| Symptom | Likely Cause | Action |
|---------|-------------|--------|
| Thrusters not responding | Kill switch active or RC in KILL mode | Check RC_KILL_ACTIVE_N signal |
| No Jetson telemetry | Jetson-Teensy USB connection failed | Check USB cable, restart ros2serial node |
| Wrong position estimate | Wrong GPS reference coordinates | Verify latRef/lonRef in launch params |
| Weather data missing | Serial2 AIRMAR 110WX disconnected | Check Serial2 cable and baud rate |
| Depth sensor not reading | Ping sonar Serial8 disconnected | Check Serial8 connection, ping1d library |

---

## Open Questions

| ID | Question | Priority |
|----|----------|----------|
| OPS-1 | Which ROS 2 topics should be recorded in rosbag during field ops? | HIGH |
| OPS-2 | What is the power-on / power-off sequence? Is there a soft shutdown procedure? | HIGH |
| OPS-3 | What network setup is used at field (WiFi SSID, IP addresses)? | HIGH |
| OPS-4 | Are there any post-mission maintenance procedures? | MEDIUM |

---

## Related Files

- [WAM-V 2 Firmware](firmware.md)
- [WAM-V 2 Hardware](hardware.md)
- [WAM-V 2 ROS Interface](ros_interface.md)
- [TowFish Operations](../towfish/operations.md)
