# MRC Context Repository

**FAU Marine Robotics Club — Systems Knowledge Base**

> Last Updated: 2026-06-05
> Confidence: HIGH (built from direct repository inspection)
> Sources: Marine-Robotics-Club GitHub org (38 repos), danielmorris2022/asv-system-docs, xvrobotics/wamv_nav

## Purpose

This repository is a permanent, organized context base for the FAU Marine Robotics Club's marine robotic systems. It is intended to save the next student, researcher, or engineer 100+ hours by documenting everything known about:

- **TowFish** — towed electromagnetic/geomagnetic sensing platform
- **WAM-V 1** — WAM-V 16 with bow thrusters (general autonomy)
- **WAM-V 2** — WAM-V 16 without bow thrusters, research/survey platform
- **RoboBoat** — competition autonomous surface vessel
- **AUV / REMUS 100 / QuSpin magnetometer** — geomagnetic survey context

## Repository Structure

```
mrc-context-repo/
  README.md                    <- this file
  INDEX.md                     <- full file index
  SYSTEM_MAP.md                <- system architecture overview
  REPOSITORY_MAP.md            <- all known repositories
  OPEN_QUESTIONS.md            <- unanswered questions
  USEFUL_LINKS.md              <- master link index
  glossary.md                  <- terms and abbreviations

  systems/
    towfish/                   <- TowFish sensor platform docs
    wamv-1/                    <- WAM-V 1 docs
    wamv-2/                    <- WAM-V 2 docs
    roboboat/                  <- RoboBoat docs
    auv-remus/                 <- AUV/REMUS/magnetometer context

  repositories/                <- per-repo deep dives
  code_maps/                   <- code inventory by type
  ros/                         <- ROS 2 topic/node/package inventory
  electrical/                  <- PCB, wiring, power
  data/                        <- data formats and post-processing
  operations/                  <- field ops checklists
  links/                       <- link indexes
  raw_notes/                   <- scan logs and open questions
```

## What a New Student Should Read First

1. `SYSTEM_MAP.md` — understand what systems exist and how they relate
2. `systems/towfish/overview.md` — understand the TowFish mission
3. `systems/wamv-2/overview.md` — understand WAM-V 2 (primary tow vessel)
4. `systems/towfish/electronics.md` — understand the hardware
5. `systems/towfish/data_format.md` — understand the data
6. `operations/pre_deployment_checklists.md` — before going to the field
7. `OPEN_QUESTIONS.md` — know what is still unknown

## Key Repositories

| Repository | Purpose | Status |
|---|---|---|
| [Marine-Robotics-Club/Thomas_Towfish](https://github.com/Marine-Robotics-Club/Thomas_Towfish) | TowFish ROS 2 workspace + Teensy firmware | Active |
| [Marine-Robotics-Club/LowLevel_Wamv2](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2) | WAM-V 2 low-level ROS 2 control | Active |
| [danielmorris2022/asv-system-docs](https://github.com/danielmorris2022/asv-system-docs) | System documentation (all platforms) | Active |
| [xvrobotics/wamv_nav](https://github.com/xvrobotics/wamv_nav) | WAM-V navigation/autonomy stack | Active |
| [Marine-Robotics-Club/LowLevel_Wamv1](https://github.com/Marine-Robotics-Club/LowLevel_Wamv1) | WAM-V 1 low-level control | Active |
| [Marine-Robotics-Club/roboboat_usv](https://github.com/Marine-Robotics-Club/roboboat_usv) | RoboBoat USV code | Active |

## Most Important Open Questions

See `OPEN_QUESTIONS.md` for the full list. Key gaps:
- TowFish test logs are not yet populated in asv-system-docs
- MATLAB post-processing scripts location is unknown
- RP2040 USB-host bridge firmware source is not in Thomas_Towfish repo
- WAM-V 1 GPS reference coordinates not confirmed
- Layback correction algorithm not yet implemented in any found code

## Most Important Risks Before Field Deployment

1. Verify TowFish buoyancy BEFORE water deployment
2. Verify magnetometer outputs numeric values (not `nan`) before towing
3. Verify IMU recording is active on SD card
4. Verify no cable twisting in tow cable
5. Bring extra ballast weights
6. Verify battery voltage > 22V before deployment
7. Verify leak sensor reads NO_LEAK before deployment
8. Account for layback offset (tow cable length) in GPS mapping
9. Check bottom depth vs. towfish depth to avoid bottom contact
10. Verify XBee/telemetry link before departure from dock

## Contributors

- Xavier Vicent Navarro (xvrobotics) — WAM-V autonomy, Thomas_Towfish ROS code
- Daniel Morris (danielmorris2022) — asv-system-docs, this context repo
- RASP_V2 firmware by EH (initials) — Teensy 4.1 TowFish firmware
- Caleb Wilson (calebwi1son) — shell scripts, RobotX 2024 launch code

## License

Documentation only. Code copyright belongs to respective original repository owners.
