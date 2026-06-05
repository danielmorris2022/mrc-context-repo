# TowFish System

> Last Updated: 2026-06-05 | Confidence: HIGH | Sources: Marine-Robotics-Club/Thomas_Towfish, danielmorris2022/asv-system-docs

## Overview

The TowFish is a towed electromagnetic and geomagnetic sensing platform designed and built at FAU. It is towed behind a WAM-V surface vessel (typically WAM-V 2) at approximately 10 m standoff distance.

## Purpose

- Collect magnetic-field (B-field) and electric-field (E-field) data while isolated from the tow vessel electronics and ferromagnetic materials
- Conduct geomagnetic surveys over known Florida anomalies
- Map magnetic fields in the water column
- Test stability using onboard IMU data
- Relate towfish motion data to sensor data quality

## System Files

| File | Purpose |
|---|---|
| [overview.md](overview.md) | Full overview |
| [hardware.md](hardware.md) | Physical hardware description |
| [electronics.md](electronics.md) | Electrical systems |
| [firmware.md](firmware.md) | Teensy 4.1 and RP2040 firmware |
| [sensors.md](sensors.md) | All sensor descriptions |
| [magnetometer.md](magnetometer.md) | Twinleaf/QuSpin magnetometer |
| [imu_and_attitude.md](imu_and_attitude.md) | BNO055 IMU |
| [pressure_depth.md](pressure_depth.md) | MS5837 pressure sensor |
| [data_format.md](data_format.md) | **CRITICAL: CSV data format reference** |
| [data_logging.md](data_logging.md) | Logging procedures |
| [post_processing.md](post_processing.md) | MATLAB/Python post-processing |
| [operations.md](operations.md) | Field operations |
| [test_history.md](test_history.md) | Historical test logs |
| [known_issues.md](known_issues.md) | Known bugs and issues |
| [wamv_integration.md](wamv_integration.md) | WAM-V integration guide |
| [links.md](links.md) | All relevant links |

## Key Hardware Facts

- Main controller: **Teensy 4.1**
- Magnetometer interface: **RP2040 USB-host bridge** -> UART to Teensy Serial5
- IMU: **BNO055** (I2C addr 0x28)
- Pressure/depth: **Blue Robotics Bar30 / MS5837**
- Magnetometer: **Twinleaf microSAM** (QuSpin-based total field)
- Battery: **6S LiPo** (22.2V nominal, 25.2V full)
- Power rails: 5V (USB host), 3.3V (logic)
- Telemetry baud rate: **115200**
- Telemetry rate: **~20 Hz**
- SD logging: RASP_001.csv through RASP_099.csv

## Key ROS Topics (Topside)

| Topic | Type | Direction | Description |
|---|---|---|---|
| `/twinleaf/mag_field` | Float32 | Published | Magnetometer field in nT |
| `/twinleaf/gps` | NavSatFix | Published | WAM-V GPS for co-logging |
| `/twinleaf/speed` | Float32 | Published | WAM-V speed |
| `/towfish/vehicle_pose` | Pose2D | Published | Towfish NED position |
| `/towfish/p3d_ned` | Odometry | Published | Full odometry in NED |
| `/towfish/bfvelo_ned` | Vector3 | Published | Body-frame velocity NED |
| `/drone/global_position/raw/fix` | NavSatFix | Subscribed | Vehicle GPS |
| `/drone/imu/data` | Imu | Subscribed | Vehicle IMU |
| `/drone/global_position/raw/gps_vel` | TwistStamped | Subscribed | Vehicle speed |

## Primary Source Repositories

- [Marine-Robotics-Club/Thomas_Towfish](https://github.com/Marine-Robotics-Club/Thomas_Towfish) — code
- [danielmorris2022/asv-system-docs/docs/towfish](https://github.com/danielmorris2022/asv-system-docs/tree/main/docs/towfish) — documentation
