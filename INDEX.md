# MRC Context Repository — Master Index

**Purpose:** Complete index of all documentation files in this context repository, organized by system and topic.
**Last Updated:** 2025-07
**Confidence:** N/A — index file.

> **Start here.** If you are new to this codebase, read [README.md](./README.md) first, then [SYSTEM_MAP.md](./SYSTEM_MAP.md) for the big picture, then dive into the system folder of interest.

---

## Root Files

| File | Purpose |
|------|---------|
| [README.md](./README.md) | Repository introduction and how to use this context repo |
| [INDEX.md](./INDEX.md) | This file — master index |
| [SYSTEM_MAP.md](./SYSTEM_MAP.md) | High-level architecture map of all FAU MRC systems |
| [REPOSITORY_MAP.md](./REPOSITORY_MAP.md) | Catalogue of all 38 GitHub repositories, mapped to systems |
| [open_questions.md](./open_questions.md) | Known unknowns, bugs, and items requiring field verification |

---

## TowFish System

**Location:** `systems/towfish/`

| File | Purpose | Status |
|------|---------|--------|
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
|------|---------|--------|
| [README.md](./systems/wamv2/README.md) | System overview: hardware, ROS packages, control modes, pin map | Done |

**TODO:** `hardware.md`, `ros_interface.md`, `operations.md` — not yet written.

**Source Repos:** [LowLevel_Wamv2](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2)

---

## WAM-V 1 System

**Location:** `systems/wamv1/`

| File | Purpose | Status |
|------|---------|--------|
| [README.md](./systems/wamv1/README.md) | System overview: hardware (with bow thrusters + weather station), ROS packages, differences from WAM-V 2 | Done |

**TODO:** `hardware.md`, `ros_interface.md`, `operations.md` — not yet written.

**Source Repos:** [LowLevel_Wamv1](https://github.com/Marine-Robotics-Club/LowLevel_Wamv1), [Vision](https://github.com/Marine-Robotics-Club/Vision), [highlevel](https://github.com/Marine-Robotics-Club/highlevel), [Mission_Control](https://github.com/Marine-Robotics-Club/Mission_Control)

---

## RoboBoat System

**Location:** `systems/roboboat/`

| File | Purpose | Status |
|------|---------|--------|
| [README.md](./systems/roboboat/README.md) | System overview: ROS packages (asv_*), APF controller, relationship to WAM-V | Done |

**TODO:** Hardware doc, `ros_interface.md`, `operations.md` — not yet written.

**Source Repos:** [roboboat_usv](https://github.com/Marine-Robotics-Club/roboboat_usv), [roboboat_station](https://github.com/Marine-Robotics-Club/roboboat_station)

---

## Planned / Not Yet Written

| Planned File | System | Priority |
|-------------|--------|----------|
| `systems/wamv2/ros_interface.md` | WAM-V 2 | HIGH — covers all ROS 2 topics, nodes |
| `systems/wamv2/hardware.md` | WAM-V 2 | HIGH — detailed pin maps, serial ports |
| `systems/wamv2/operations.md` | WAM-V 2 | HIGH — startup procedure, launch commands |
| `systems/wamv1/ros_interface.md` | WAM-V 1 | HIGH — covers weather station topics |
| `systems/wamv1/operations.md` | WAM-V 1 | MEDIUM |
| `systems/roboboat/ros_interface.md` | RoboBoat | HIGH — all asv_* topics and nodes |
| `systems/roboboat/hardware.md` | RoboBoat | HIGH — hardware platform unknown |
| `systems/roboboat/operations.md` | RoboBoat | MEDIUM |
| `integration/towfish_wamv2.md` | TowFish+WAM-V2 | HIGH — how TowFish integrates with WAM-V 2 |
| `integration/ros_network.md` | All | HIGH — ROS 2 DDS config, IPs, namespaces |

---

## Quick Lookup Table

### "I need to know about..."

| Topic | Go to |
|-------|-------|
| TowFish Teensy firmware commands | [systems/towfish/firmware.md](./systems/towfish/firmware.md) |
| TowFish CSV data columns | [systems/towfish/data_format.md](./systems/towfish/data_format.md) |
| TowFish startup procedure | [systems/towfish/operations.md](./systems/towfish/operations.md) |
| TowFish ROS topics | [systems/towfish/ros_interface.md](./systems/towfish/ros_interface.md) |
| TowFish hardware / pin map | [systems/towfish/hardware.md](./systems/towfish/hardware.md) |
| WAM-V 2 thruster/servo pin map | [systems/wamv2/README.md](./systems/wamv2/README.md#teensy-pcu-pin-map) |
| WAM-V 2 ROS packages | [systems/wamv2/README.md](./systems/wamv2/README.md#ros-2-packages) |
| WAM-V 1 vs WAM-V 2 differences | [systems/wamv1/README.md](./systems/wamv1/README.md#key-differences-from-wam-v-2) |
| WAM-V 1 bow thrusters | [systems/wamv1/README.md](./systems/wamv1/README.md) |
| WAM-V 1 weather station NMEA | [systems/wamv1/README.md](./systems/wamv1/README.md#weather-station-data-airmar-110wx) |
| RoboBoat 2026 packages | [systems/roboboat/README.md](./systems/roboboat/README.md#ros-2-packages) |
| APF controller location | [systems/roboboat/README.md](./systems/roboboat/README.md#sub-packages-in-asv_control) |
| All GitHub repos | [REPOSITORY_MAP.md](./REPOSITORY_MAP.md) |
| Known unknowns | [open_questions.md](./open_questions.md) |

---

## Confidence Legend

| Level | Meaning |
|-------|---------|
| HIGH | Extracted directly from source code or config files |
| MEDIUM-HIGH | Derived from source code with minor inferences |
| MEDIUM | Derived from repo structure/naming; code not fully inspected |
| LOW | Inferred from indirect evidence; needs verification |
| UNKNOWN | Not found in any inspected source |
| INFERRED | Logical deduction, not directly confirmed |
| CONFLICT | Two sources disagree — noted explicitly |
