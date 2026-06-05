# RoboBoat Missions

**Purpose:** Mission and task documentation for RoboBoat 2026 competition — mission_id values, task descriptions, software components, and planner architecture.
**Last Updated:** 2026-06-05
**Source Repo:** [roboboat_usv](https://github.com/Marine-Robotics-Club/roboboat_usv) — `src/asv_planner/`, `src/asv_launch/launch/`
**Confidence:** MEDIUM — derived from package names and launch file parameters; individual task logic not fully inspected

---

## Competition Overview

| Property | Value |
|----------|-------|
| Competition | RoboBoat (AUVSI / Robonation) |
| Year | 2026 (firmware commit: "RoboBoat 2026 snapshot") |
| Venue | Sarasota, FL area (INFERRED from past RoboBoat events) |
| Scoring | Task-based autonomous missions |

---

## Mission Architecture

The RoboBoat system uses two planner nodes from `asv_planner`:

| Node | Executable | Role |
|------|-----------|------|
| `mission_planner` | `mission_planner` | High-level mission sequencer — selects and sequences tasks by `mission_id` |
| `planner_node` | `planner_node` | Low-level task executor — generates waypoints and control goals |

---

## mission_id Parameter

The `mission_id` LaunchConfiguration parameter selects which mission to run.

> **UNKNOWN:** Valid `mission_id` values and their task mappings are not documented in any source file inspected. This is open question RB-8.

Expected values (INFERRED from competition tasks):

| mission_id (INFERRED) | Likely Task |
|-----------------------|-------------|
| 1 | Navigation channel |
| 2 | Station keeping |
| 3 | Follow the path |
| 4 | Speed gate |
| 5 | Semantic buoy |
| 6 | Scan the code |
| 7 | Water shooter (pump task) |

> **ALL mission_id values above are INFERRED from typical RoboBoat competition tasks. Actual values require inspection of `asv_planner` source code.**

---

## RoboBoat 2026 Competition Tasks

Typical RoboBoat tasks (INFERRED from Robonation rulebook patterns):

| Task | Hardware Used | Software Package | Notes |
|------|--------------|-----------------|-------|
| Navigation channel | GPS, thrusters | `asv_planner`, `pid_hs` | Navigate between buoy pairs |
| Station keeping | GPS, IMU | `pid_hs` | Hold position for 30-60 seconds |
| Follow the path | GPS, IMU | `planner_node` | Follow sequence of waypoints |
| Speed gate | GPS, thrusters | `pid_hs` | Pass through timed gates |
| Semantic buoy | Cameras, perception | `asv_perception`, `planner_node` | Identify and approach colored buoys |
| Water shooter | Pump, cameras | `asv_perception`, pump on Serial3 | Aim pump at target |
| Acoustic survey | Audio, `asv_audio` | `asv_audio` | Follow acoustic signals |

> **ALL tasks above are INFERRED from competition context. Actual task implementations must be verified in `asv_planner/` and `asv_perception/` source code.**

---

## Planner Node Parameters

From `roboboat.launch.py`:

| Parameter | Value | Description |
|-----------|-------|-------------|
| `use_sim_time` | False | Real-time operation (not simulation) |
| `asv` | LaunchConfiguration | Vehicle configuration selector |
| `mission_id` | LaunchConfiguration | Mission/task selector |

---

## Simulation Support

A `sim.launch.py` file exists in `asv_launch/launch/` suggesting simulation capability.
Simulation environment: UNKNOWN — likely Gazebo or VRX (given WAM-V VRX background).

---

## Files to Inspect for Mission Logic

| File | What to Look For |
|------|------------------|
| [`src/asv_planner/`](https://github.com/Marine-Robotics-Club/roboboat_usv/tree/main/src/asv_planner) | mission_id values, task state machines |
| [`src/asv_perception/`](https://github.com/Marine-Robotics-Club/roboboat_usv/tree/main/src/asv_perception) | Object detection, buoy classification |
| [`src/asv_launch/launch/planner.launch.py`](https://github.com/Marine-Robotics-Club/roboboat_usv/tree/main/src/asv_launch/launch) | Additional planner parameters |

---

## Open Questions

| ID | Question | Priority |
|----|----------|----------|
| RB-8 | What mission_id values are supported? | HIGH |
| RB-11 | What RoboBoat 2026 tasks are actually implemented? | HIGH |
| RB-12 | What perception pipeline is used for buoy detection? | HIGH |
| RB-13 | What is the pump fire target task implementation? | MEDIUM |
| RB-14 | Is there a field waypoint configuration file? | HIGH |

---

## Related Files

- [RoboBoat Hardware](hardware.md)
- [RoboBoat ROS Interface](ros_interface.md)
- [RoboBoat Operations](operations.md)
