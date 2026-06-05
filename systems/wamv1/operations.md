# WAM-V 1 Operations

**Purpose:** Field operation procedures for WAM-V 1, with differences from WAM-V 2 noted.
**Last Updated:** 2026-06-05
**Source Repos:** [LowLevel_Wamv1](https://github.com/Marine-Robotics-Club/LowLevel_Wamv1)
**Confidence:** MEDIUM — INFERRED from firmware. No formal SOP found in any repo.

> For the baseline WAM-V operations procedure, see [WAM-V 2 Operations](../wamv2/operations.md). This document covers WAM-V 1 specific differences only.

---

## Key Operational Differences from WAM-V 2

| Aspect | WAM-V 1 | WAM-V 2 |
|--------|---------|----------|
| Bow thrusters | Requires bow thruster trim before ops | No bow thrusters |
| Bow thruster safety | 1-second transition neutral (1500 μs) on mode change | N/A |
| Launch file | UNKNOWN | `fau_outdoor.launch.py` |
| Advanced autonomy | No path_planner2, no RL | path_planner2 + RL available |

---

## Startup Procedure (INFERRED)

Follow the same startup procedure as [WAM-V 2 Operations](../wamv2/operations.md), with these additions:

### Bow Thruster Pre-Check
- [ ] Verify bow thruster connections (pins 10-13)
- [ ] In MANUAL mode, verify bow thrusters respond to RC inputs
- [ ] Check transition safety: verify 1-second neutral delay on mode switch before bow thrusters accept commands

### ROS Stack Startup
```bash
# Source workspace (path UNKNOWN for WAM-V 1 — verify with operator)
source /home/[UNKNOWN]/install/setup.bash

# Launch WAM-V 1 field stack
# Launch file name: UNKNOWN
# ros2 launch fau [UNKNOWN_launch_file].launch.py
```

> **Open Question WV1-5:** What is the correct launch file for WAM-V 1 field ops?

---

## Bow Thruster Transition Safety

The firmware implements a safety timer to prevent thruster kick:

| Transition | Bow Thruster Behavior |
|-----------|----------------------|
| KILLED → ACTIVE | Set to neutral (1500 μs) for 1 second |
| MANUAL → AUTONOMOUS | Set to neutral (1500 μs) for 1 second |
| Normal operation | Commands accepted immediately |

---

## Mode Switching

Identical to WAM-V 2 — see [WAM-V 2 Operations — Mode Switching](../wamv2/operations.md).

---

## Known Issues

| ID | Severity | Description | Workaround |
|----|----------|-------------|------------|
| WV1-1 | LOW | `Sub_System_ROS2` purpose undocumented | Avoid modifying until documented |
| BUG-4 | MEDIUM | Light tower not ROS-controllable | Manual only |
| WV1-5 | HIGH | Field launch file not documented | Ask team members |

---

## Open Questions

| ID | Question | Priority |
|----|----------|----------|
| WV1-5 | What launch file is used for WAM-V 1 field ops? | HIGH |
| WV1-3 | Is WAM-V 1 still actively used or effectively retired? | HIGH |
| OPS-5 | What is the bow thruster startup check procedure? | MEDIUM |

---

## Related Files

- [WAM-V 1 Firmware](firmware.md)
- [WAM-V 1 Hardware](hardware.md)
- [WAM-V 1 ROS Interface](ros_interface.md)
- [WAM-V 2 Operations](../wamv2/operations.md) — baseline procedures
