# MRC Context Repository — Master Index

**Purpose:** Complete index of all documentation files in this context repository, organized by system and topic.
**Last Updated:** 2025-07
**Confidence:** N/A — index file.

> **Start here.** If you are new to this codebase, read [README.md](./README.md) first, then [SYSTEM_MAP.md](./SYSTEM_MAP.md) for the big picture, then dive into the system folder of interest.

---

## Root Files

| File | Purpose |
|------|----------|
| [README.md](./README.md) | Repository introduction and how to use this context repo |
| [INDEX.md](./INDEX.md) | This file — master index |
| [SYSTEM_MAP.md](./SYSTEM_MAP.md) | High-level architecture map of all FAU MRC systems |
| [REPOSITORY_MAP.md](./REPOSITORY_MAP.md) | Catalogue of all GitHub repositories, mapped to systems |
| [open_questions.md](./open_questions.md) | Known unknowns, bugs, and items requiring field verification |

---

## TowFish System

**Location:** `systems/towfish/`

| File | Purpose | Status |
|------|----------|--------|
| [README.md](./systems/towfish/README.md) | System overview for TowFish | Done |
| [hardware.md](./systems/towfish/hardware.md) | Component inventory, pin maps, interfaces | Done |
| [firmware.md](./systems/towfish/firmware.md) | Teensy 4.1 firmware (RASP_V2) — state machine, commands, CSV format | Done |
| [ros_interface.md](./systems/towfish/ros_interface.md) | ROS 2 packages, nodes, topics, launch files | Done |
| [operations.md](./systems/towfish/operations.md) | Pre-deployment checklist, startup procedure, data retrieval | Done |
| [data_format.md](./systems/towfish/data_format.md) | Full CSV column definitions, units, quality flags | Done |

**Source Repo:** [Thomas_Towfish](https://github.com/Marine-Robotics-Club/Thomas_Towfish)

---

## WAM-V 2 System

**Location:** `systems/wamv2/`

| File | Purpose | Status |
|------|----------|--------|
| [README.md](./systems/wamv2/README.md) | System overview: hardware, ROS packages, control modes, pin map | Done |
| [hardware.md](./systems/wamv2/hardware.md) | Teensy pin assignments, sensors, actuators, power | Done |
| [ros_interface.md](./systems/wamv2/ros_interface.md) | ROS 2 topics, nodes, packages, launch files | Done |
| [operations.md](./systems/wamv2/operations.md) | Startup procedure, calibration, safety checklist | Done |

**Source Repo:** [LowLevel_Wamv2](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2)

---

## WAM-V 1 System

**Location:** `systems/wamv1/`

| File | Purpose | Status |
|------|----------|--------|
| [README.md](./systems/wamv1/README.md) | System overview: hardware (bow thrusters + weather station), ROS packages, differences from WAM-V 2 | Done |
| [hardware.md](./systems/wamv1/hardware.md) | Teensy pin assignments, sensors, actuators, bow thrusters | Done |
| [ros_interface.md](./systems/wamv1/ros_interface.md) | ROS 2 topics, nodes, packages, high-level packages | Done |
| [operations.md](./systems/wamv1/operations.md) | Startup procedure, calibration, differences from WAM-V 2 ops | Done |

**Source Repos:** [LowLevel_Wamv1](https://github.com/Marine-Robotics-Club/LowLevel_Wamv1), [Vision](https://github.com/Marine-Robotics-Club/Vision), [highlevel](https://github.com/Marine-Robotics-Club/highlevel), [Mission_Control](https://github.com/Marine-Robotics-Club/Mission_Control)

---

## RoboBoat ASV System

**Location:** `systems/roboboat/`

| File | Purpose | Status |
|------|----------|--------|
| [README.md](./systems/roboboat/README.md) | System overview: ROS packages (asv_*), APF controller, relationship to WAM-V | Done |
| [hardware.md](./systems/roboboat/hardware.md) | Hardware inventory, thrusters, sensors, compute, power | Done |
| [ros_interface.md](./systems/roboboat/ros_interface.md) | ROS 2 packages, nodes, topics, launch config parameters | Done |
| [operations.md](./systems/roboboat/operations.md) | Launch procedure, E-stop, station comms, competition ops | Done |
| [missions.md](./systems/roboboat/missions.md) | Mission IDs, task pipeline, open questions on task implementation | Done |

**Source Repos:** [roboboat_usv](https://github.com/Marine-Robotics-Club/roboboat_usv), [roboboat_station](https://github.com/Marine-Robotics-Club/roboboat_station), [roboboat_ws](https://github.com/Marine-Robotics-Club/roboboat_ws)

---

## Cross-Cutting Files

| Topic | Where to Look |
|-------|---------------|
| All open unknowns | [open_questions.md](./open_questions.md) |
| Repository catalogue | [REPOSITORY_MAP.md](./REPOSITORY_MAP.md) |
| System architecture | [SYSTEM_MAP.md](./SYSTEM_MAP.md) |
| Hardware across systems | Each system's `hardware.md` |
| ROS interfaces across systems | Each system's `ros_interface.md` |

---

*Last full review: 2025-07. All four primary systems documented.*
