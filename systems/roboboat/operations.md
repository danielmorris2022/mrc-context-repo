# RoboBoat Operations

**Purpose:** Field operation procedures, startup checklist, E-Stop, mode switching, and troubleshooting for RoboBoat 2026 ASV.
**Last Updated:** 2026-06-05
**Source Repos:** [roboboat_usv](https://github.com/Marine-Robotics-Club/roboboat_usv), RB26.h firmware
**Confidence:** MEDIUM — INFERRED from firmware and launch files. No formal SOP found in any repo.

---

## Startup Procedure (INFERRED)

### 1. Pre-Power Checks
- [ ] Verify battery voltage (monitor BATT_V on pin A3)
- [ ] Confirm E-Stop input (ESTOP_SENSE A15) is in normal/run state
- [ ] Verify Garmin GPS antenna has clear sky view
- [ ] Check IMU is mounted securely (IMU failure causes firmware halt)
- [ ] Confirm pump connections to Serial3
- [ ] RC transmitter on and in KILL mode

### 2. Power On Sequence
- [ ] Apply battery power
- [ ] Wait 2 seconds (firmware delay on startup)
- [ ] If IMU not detected, firmware halts — check IMU before continuing
- [ ] Verify light tower initializes (Orange/Green/Red)
- [ ] Confirm Jetson receives Teensy telemetry (10Hz @ 100ms period)

### 3. ROS Stack Startup
```bash
# Source workspace (path UNKNOWN — verify with operator)
source /home/[UNKNOWN]/install/setup.bash

# Launch RoboBoat field stack
ros2 launch asv_launch roboboat.launch.py asv:=[ASV_CONFIG] mission_id:=[MISSION_ID]
```

> **Note:** `asv` and `mission_id` are required LaunchConfiguration parameters. Their valid values are UNKNOWN — open questions RB-7 and RB-8.

### 4. Mode Switching

| Mode | RC Channel | Behavior |
|------|-----------|----------|
| KILL | Kill switch active | Thrusters to neutral (1500 μs) |
| MANUAL | RC active, not auto | RC commands directly to thrusters |
| AUTONOMOUS | `isAutoFlag = true` | ROS/Jetson commands to thrusters |

---

## E-Stop System

- **Hardware E-Stop:** `ESTOP_SENSE` on pin A15
- **Software Kill:** `isKilledFlag` in firmware
- **Safety behavior:** On kill, all PWM outputs go to neutral
- **Competition requirement:** RoboBoat competition requires hardware E-stop — confirm ESTOP_SENSE wiring before each run

---

## Safety Startup Check

Firmware code:
```cpp
if (!RB26_imu_init()) {
    Jetson.println("IMU not detected!");
    while (1) {}
}
```
**If IMU fails to initialize, firmware halts in infinite loop.** Power cycle required.

---

## Pump System Operations

> Task-specific (fire-fighting or water jet task in RoboBoat competition).

- Pump initialized via `RB26_pump_init(9600)` on Serial3
- Pump activation: controlled via `pump_cmd` sent to Serial3
- Pre-run check: Verify pump turns on/off with test command before entering water

---

## Known Issues

| ID | Severity | Description |
|----|----------|-------------|
| RB-2 | HIGH | IMU model undocumented; if wrong library, IMU init will fail causing firmware halt |
| RB-7 | HIGH | `asv` launch parameter valid values not documented |
| RB-8 | HIGH | `mission_id` valid values not documented |

---

## Troubleshooting

| Symptom | Likely Cause | Action |
|---------|-------------|--------|
| Firmware hangs at startup | IMU not detected | Check IMU wiring and I2C address |
| Thrusters not responding | Kill switch active, or T500_EN disabled | Check ESTOP_SENSE and RC mode |
| GPS not locking | Garmin GPS antenna blocked or Serial6 not connected | Check Serial6 connection at 38400 baud |
| No Jetson telemetry | USB Serial connection failed | Check USB cable, 9600 baud |
| ROS launch fails | asv or mission_id parameter wrong | Verify valid parameter values with team |

---

## Competition-Specific Notes

- **Competition:** AUVSI / Robonation RoboBoat (held in Sarasota, FL area)
- **E-stop:** Hardware E-stop mandatory at competition — must be wireless
- **Autonomy flag:** Vehicle must be autonomous during scored runs
- **Audio:** `asv_audio` system used — for task-specific audio cues

---

## Open Questions

| ID | Question | Priority |
|----|----------|----------|
| RB-7 | What does the `asv` launch parameter select? | HIGH |
| RB-8 | What mission_id values are supported? | HIGH |
| OPS-6 | What is the competition field GPS reference lat/lon? | HIGH |
| OPS-7 | What are the pump activation/deactivation commands? | MEDIUM |

---

## Related Files

- [RoboBoat Hardware](hardware.md)
- [RoboBoat ROS Interface](ros_interface.md)
- [RoboBoat Missions](missions.md)
