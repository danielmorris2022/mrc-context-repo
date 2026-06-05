# System Architecture Map

> Last Updated: 2026-06-05 | Confidence: HIGH | Sources: Marine-Robotics-Club GitHub, danielmorris2022/asv-system-docs

## Overview

The FAU Marine Robotics Club operates three primary marine platforms and one towed sensor payload, all based at Florida Atlantic University in Fort Lauderdale / Dania Beach, Florida.

```
                          ┌─────────────────────────────────────┐
                          │        BASE STATION                 │
                          │  IP: 192.168.1.5                    │
                          │  SSID: WAMV16_BASE2                 │
                          │  Ubiquiti AP: 192.168.1.10          │
                          └────────────┬────────────────────────┘
                                       │ Wi-Fi (Ubiquiti bridge)
              ┌───────────────┬────────┴──────┬─────────────────┐
              │               │               │                 │
   ┌──────────▼──────┐ ┌──────▼──────┐ ┌─────▼──────┐  ┌──────▼──────┐
   │   WAM-V 1       │ │   WAM-V 2   │ │  RoboBoat  │  │  TowFish    │
   │ 192.168.1.1/3   │ │192.168.1.2/4│ │192.168.1.6 │  │ (no IP;     │
   │ WAMV16_CB1      │ │ WAMV16_CB2  │ │            │  │  XBee/USB)  │
   └─────────────────┘ └──────┬──────┘ └────────────┘  └─────────────┘
                              │ tow cable (~10 m standoff ideal)
                         ┌────▼────────┐
                         │  TowFish    │
                         │  (payload)  │
                         └─────────────┘
```

## Platform Summary

| Platform | Type | Primary Use | Computing | ROS | Status |
|---|---|---|---|---|---|
| WAM-V 1 | WAM-V 16 USV (w/ bow thrusters) | Autonomy research, RobotX | Jetson Xavier (192.168.1.110) + Jetson Orin (192.168.1.120) | ROS 2 Humble | Active |
| WAM-V 2 | WAM-V 16 USV (no bow thrusters) | Geomagnetic survey, TowFish ops | Jetson Xavier (192.168.1.100) + Jetson Orin (192.168.1.186) | ROS 2 Humble | Active |
| RoboBoat | Custom small USV | Competition autonomy | Jetson-based | ROS 2 | Active |
| TowFish | Towed EM/magnetic sensor payload | Geomagnetic survey | Teensy 4.1 + RP2040 + laptop | No ROS onboard | Active |

## Software Stack Overview

### WAM-V 1 Software
- **OS**: Ubuntu 22.04
- **ROS**: ROS 2 Humble
- **Low-level repo**: [Marine-Robotics-Club/LowLevel_Wamv1](https://github.com/Marine-Robotics-Club/LowLevel_Wamv1)
- **High-level repo**: [xvrobotics/wamv_nav](https://github.com/xvrobotics/wamv_nav) (branches for wamv1)
- **Key packages**: wamv_state, wamv_allocation, wamv_control, wamv_planner, fuzzy_logic, ros2serial, teensyRos2, wamv_perception

### WAM-V 2 Software
- **OS**: Ubuntu 22.04
- **ROS**: ROS 2 Humble
- **Low-level repo**: [Marine-Robotics-Club/LowLevel_Wamv2](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2)
- **High-level repo**: [Marine-Robotics-Club/High_Level2](https://github.com/Marine-Robotics-Club/High_Level2) (EMPTY, not yet populated)
- **Key packages**: fau2, fau_msgs, vehicle_state2, path_planner2, teensyRos2 (WAMV_PCU_ROS2 Arduino firmware)
- **Teensy 4.1 firmware**: WAMV_PCU_ROS2.ino
  - Interfaces: Torqeedo PCU (Serial1), AIRMAR weather (Serial2), RoboteQ MDC1460 port (Serial3), RoboteQ MDC1460 stbd (Serial4), AIRMAR DT800 flow (Serial5), Digi RF modem (Serial6), SBUS RC (Serial7), BlueRobotics PING sonar (Serial8)

### RoboBoat Software
- **OS**: Ubuntu 22.04
- **ROS**: ROS 2 Humble (assumed)
- **Repos**: [Marine-Robotics-Club/roboboat_usv](https://github.com/Marine-Robotics-Club/roboboat_usv), [Marine-Robotics-Club/roboboat_station](https://github.com/Marine-Robotics-Club/roboboat_station), [Marine-Robotics-Club/roboboat_ws](https://github.com/Marine-Robotics-Club/roboboat_ws)

### TowFish Software
- **Topside OS**: Ubuntu 22.04 + ROS 2 Humble (laptop on WAM-V or shore)
- **Onboard**: Teensy 4.1 (RASP_V2.ino firmware) + RP2040 (USB-host bridge firmware, NOT YET IN REPO)
- **Topside ROS workspace**: [Marine-Robotics-Club/Thomas_Towfish](https://github.com/Marine-Robotics-Club/Thomas_Towfish)
- **Key packages**: twinleaf (ROS2 node + CSV logger), vehicle_state (NED converter), fau_gui (GUI + bag tools)

## Network IP Map

| Device | IP | Notes |
|---|---|---|
| Base station router | 192.168.1.5 | SSID WAMV16_BASE2 |
| Ubiquiti base AP | 192.168.1.10 | |
| WAMV1 router | 192.168.1.1 | SSID WAMV16_CB1 |
| WAMV1 edge router (Ubiquiti EdgeRouter) | 192.168.1.3 | |
| WAMV1 Ubiquiti AP | 192.168.1.12 | |
| WAMV1 Jetson Xavier | 192.168.1.110 | LL1 |
| WAMV1 Jetson Orin | 192.168.1.120 | HL1 |
| WAMV2 router | 192.168.1.2 | SSID WAMV16_CB2 |
| WAMV2 edge router (Ubiquiti EdgeRouter) | 192.168.1.4 | |
| WAMV2 Ubiquiti AP | 192.168.1.11 | |
| WAMV2 Jetson Xavier | 192.168.1.100 | LL2 |
| WAMV2 Jetson Orin | 192.168.1.186 | HL2 |
| RoboBoat edge router | 192.168.1.6 | |
| Amcrest cameras (RoboBoat) | 192.168.1.131-133 | |

## GPS Reference Coordinates

| System | Lat | Lon | Source |
|---|---|---|---|
| WAM-V 2 (LowLevel_Wamv2) | 26.055513176834214 | -80.11413939041087 | fau_outdoor.launch.py |
| WAM-V 1 (wamv_nav) | UNKNOWN | UNKNOWN | See state.yaml |

The WAM-V 2 GPS reference corresponds to a location near FAU Harbor Branch / Fort Lauderdale area (lat ~26.055 N, lon ~80.114 W).

## TowFish Communication Chain

```
Twinleaf microSAM magnetometer
  -> CAT5/RJ45 differential cable
  -> Twinleaf Ethernet-to-USB-C adapter
  -> RP2040 USB host bridge (120 MHz, TinyUSB stack)
  -> UART Serial1 (RP2040) -> Teensy Serial5 @ 115200 baud
  -> Teensy 4.1 (RASP_V2 or TPSM firmware)
     - Reads: BNO055 IMU (I2C, 0x28), MS5837 pressure/depth, analog health channels
     - Combines: magnetometer + IMU + pressure + health into telemetry packet
  -> XBee Serial5 @ 115200 baud -> topside laptop
  -> Python logger (twinleaf_logger_ros.py) -> CSV + ROS topics
```

Alternatively (from RASP_V2.ino): XBee on Serial5, file saved as RASP_nnn.csv on SD card.

## Data Flow for Geomagnetic Survey

```
WAM-V 2 (tow vessel)
  -> GPS position (/drone/global_position/raw/fix)
  -> IMU heading (/drone/imu/data)
  -> Speed (/drone/global_position/raw/gps_vel)
     |
     | tow cable (layback ~ tow speed x time offset)
     |
TowFish (sensor payload)
  -> Twinleaf magnetic field -> /twinleaf/mag_field (Float32, nT)
  -> RASP IMU attitude -> /towfish/p3d_ned (Odometry)

Topside logger (twinleaf_logger_ros.py)
  -> CSV: Timestamp, Iteration, Magnetic Field (T), Status, Extra,
          Latitude, Longitude, psiNED (rad), psiNED (deg), Speed (m/s)

Post-processing (MATLAB or Python)
  -> Layback correction: towfish GPS = vehicle GPS - layback vector
  -> Background field subtraction (IGRF/WMM)
  -> Magnetic anomaly map generation
```

## Cross-References

- TowFish details: `systems/towfish/`
- WAM-V 2 details: `systems/wamv-2/`
- WAM-V 1 details: `systems/wamv-1/`
- RoboBoat details: `systems/roboboat/`
- AUV/REMUS context: `systems/auv-remus/`
- Repository details: `repositories/`
- ROS inventory: `ros/`
- Network: `systems/wamv-2/networking.md`
