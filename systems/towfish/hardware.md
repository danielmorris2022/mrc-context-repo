# TowFish Hardware Reference

**Purpose:** Document all physical hardware components, pinouts, and interfaces for the Thomas TowFish system.
**Last Updated:** 2025-07
**Source Repos:** [Thomas_Towfish](https://github.com/Marine-Robotics-Club/Thomas_Towfish)
**Confidence:** MEDIUM — derived from firmware source code and launch files; no dedicated hardware schematic found in repo.

---

## Overview

The TowFish is an underwater towed magnetometry sensor platform. It is deployed behind a WAM-V and collects geomagnetic, IMU, GPS, pressure, and battery/leak health data. The primary compute platform is a **Teensy 4.1** microcontroller running custom firmware (`RASP_V2.ino`). A **Raspberry Pi** (running Ubuntu 22.04 + ROS 2 Humble) serves as the companion computer for ROS-based logging and the Twinleaf magnetometer interface.

---

## Component Inventory

| Component | Part / Model | Role | Interface | Notes |
|-----------|-------------|------|-----------|-------|
| Main MCU | Teensy 4.1 | Data acquisition, SD logging, serial comms | USB / Serial | Firmware: `RASP_V2.ino` |
| IMU | Adafruit BNO055 | 9-DOF orientation, accel, gyro | I2C (addr 0x28) | Library: `Adafruit_BNO055` |
| Magnetometer | Twinleaf (model UNKNOWN) | Scalar/vector magnetic field | USB serial (`/dev/ttyUSB0`) | Baud 115200, 10 Hz |
| Radio | XBee (model UNKNOWN) | Wireless serial telemetry to surface | Serial5 @ 115200 baud | `#define Xbee Serial5` |
| Storage | Built-in SD card (Teensy 4.1) | CSV data logging | SPI (BUILTIN_SDCARD) | Files: `RASP_001.csv` … `RASP_099.csv` |
| Battery Monitor | Analog pin A0 | Battery voltage sense | ADC | `#define VBAT A0`; unreliable below 20.0 V |
| Leak Sensor | Analog pin A1 | Water ingress detection | ADC | `#define LEAK A1`; status preserved as string |
| Hall Sensor U2 | Digital pin 32 | Start saving trigger | GPIO INPUT | Starts SD save |
| Hall Sensor U3 | Digital pin 25 | Power off (hold 3 s) / reed relay on | GPIO INPUT | Power control |
| Hall Sensor U4 | Digital pin 0 | Stop saving trigger | GPIO INPUT | Stops SD save |
| LED | Pin 13 | Status indicator | GPIO OUTPUT | Teensy board LED |
| Pressure Sensor | UNKNOWN | Depth/pressure measurement | UNKNOWN | Pressure_mbar field in CSV; quality flag `pressure_ok`: mbar > 0 |
| GPS | UNKNOWN (via ROS) | Position fix | ROS topic `/drone/global_position/raw/fix` | Subscribed by twinleaf_logger_ros.py |

---

## Teensy 4.1 Pin Map

| Pin | Define | Function |
|-----|--------|----------|
| A0 | `VBAT` | Battery voltage ADC input |
| A1 | `LEAK` | Leak sensor ADC input |
| 0 | `HALL1` / U4 | Hall sensor (stop save) |
| 25 | `HALL2` / U3 | Hall sensor (power off / reed relay) |
| 32 | `HALL3` / U2 | Hall sensor (start save) |
| 13 | `LED` | On-board status LED |
| Serial5 TX/RX | `Xbee` | XBee radio UART @ 115200 |
| BUILTIN_SDCARD | — | SD card (SPI) |
| I2C SDA/SCL | — | BNO055 IMU (addr 0x28) |

---

## Serial / Communication Interfaces

| Interface | Target | Port | Baud | Notes |
|-----------|--------|------|------|-------|
| Serial5 (Teensy) | XBee radio | Hardware UART | 115200 | Telemetry to surface |
| USB Serial (Teensy) | Host / debug | USB | — | `#define Xbee Serial` commented note: "CHANGE TO Serial to send out USB port instead of radio" |
| `/dev/ttyUSB0` (RPi) | Twinleaf magnetometer | USB-serial | 115200 | Read by `twinleaf_logger_ros.py` at 10 Hz |

---

## Raspberry Pi (Companion Computer)

- **OS:** Ubuntu 22.04 (inferred from ROS 2 Humble dependency — README states Humble requires Ubuntu 22.04)
- **ROS Version:** ROS 2 Humble
- **Repo path convention:** `~/Thomas_TowFish/`
- **Config file:** `~/Thomas_TowFish/src/vehicle_state/config/state.yaml`
- **GPS reference coordinates (TowFish Tests):** lat `26.0561334`, lon `-80.1417659` (FAU Boca Raton campus)
- **Other reference locations in config:** Fort Lauderdale, VRX Sydney, Sydney, Hawaii (commented out)

---

## Hall Sensor Behavior

Three magnetic hall sensors (U2, U3, U4) are used as physical controls for the TowFish without needing a serial connection:

| Sensor | Pin | Action |
|--------|-----|--------|
| U2 | 32 | Starts SD card saving |
| U3 | 25 | Hold 3 seconds to power off; reed relay to turn on |
| U4 | 0 | Stops SD card saving |

Debounce: 16-sample window implemented in `updateHallSensors()`.

---

## Power

- Battery voltage monitored via `VBAT` (pin A0)
- `battery_ok` quality flag: `battery_v > 20.0` (below this, data unreliable)
- Power latch: `#define STAY_ON 1`
- U3 hall sensor controls power off (hold 3 s) and power on (reed relay)

---

## Unknown / Not Found

- Pressure sensor model and wiring — UNKNOWN (field exists in CSV but no sensor definition found in firmware)
- XBee module model/version — UNKNOWN
- Twinleaf magnetometer model — UNKNOWN (referred to only as "Twinleaf" in code)
- Physical enclosure / mechanical drawings — NOT FOUND in repo
- Cable harness / connector pinout — NOT FOUND in repo
- Raspberry Pi model — UNKNOWN (Ubuntu 22.04 is compatible with RPi 4/5)
- Whether pressure sensor is on Teensy or RPi — UNKNOWN

---

## Related Files

- Firmware: [RASP_V2.ino](https://github.com/Marine-Robotics-Club/Thomas_Towfish/blob/main/src/RASP_V2/RASP_V2.ino)
- ROS Logger: [twinleaf_logger_ros.py](https://github.com/Marine-Robotics-Club/Thomas_Towfish/blob/main/src/twinleaf/twinleaf/twinleaf_logger_ros.py)
- Standalone Logger: [twinleaf_logger.py](https://github.com/Marine-Robotics-Club/Thomas_Towfish/blob/main/src/twinleaf_python/twinleaf_logger.py)
- Launch file: [vehicle_state.launch.py](https://github.com/Marine-Robotics-Club/Thomas_Towfish/blob/main/src/vehicle_state/launch/vehicle_state.launch.py)
- State config: [state.yaml](https://github.com/Marine-Robotics-Club/Thomas_Towfish/blob/main/src/vehicle_state/config/state.yaml)
- Data format: [data_format.md](./data_format.md)
