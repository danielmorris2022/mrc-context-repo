# WAM-V 2 Firmware

**Purpose:** Teensy 4.1 PCU firmware documentation for WAM-V 2 — pin assignments, serial port mapping, thruster/servo control, and Jetson interface.
**Last Updated:** 2026-06-05
**Source Repo:** [LowLevel_Wamv2](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2) — `src/teensyRos2/WAMV_PCU_ROS2/`
**Confidence:** HIGH — derived directly from firmware source code

---

## Firmware File

| File | Path | Purpose |
|------|------|---------|
| `WAMV_PCU_ROS2.ino` | [`src/teensyRos2/WAMV_PCU_ROS2/WAMV_PCU_ROS2.ino`](https://github.com/Marine-Robotics-Club/LowLevel_Wamv2/blob/main/src/teensyRos2/WAMV_PCU_ROS2/WAMV_PCU_ROS2.ino) | Main Arduino sketch for Teensy 4.1 PCU |
| `PCU.cpp` | `src/teensyRos2/WAMV_PCU_ROS2/PCU.cpp` | Torqeedo PCU control implementation |
| `PCU.h` | `src/teensyRos2/WAMV_PCU_ROS2/PCU.h` | Torqeedo PCU control header |
| `Light.h` | `src/teensyRos2/WAMV_PCU_ROS2/Light.h` | Light tower control |

---

## Hardware Target

- **MCU:** Teensy 4.1
- **Board:** WAMV-16 Main Control Board 200-405
- **Connection to Jetson:** USB serial (9600 baud)
- **Libraries:** Bolder Flight Systems SBUS v8.1.2, BlueRobotics ping1d v0.1.2, Servo.h

---

## Pin Definitions

### Thrusters / Servos (R/C PWM Output)

| Pin | Constant | Direction | Purpose |
|-----|----------|-----------|--------|
| 2 | `CT_PORT_THRUST` | OUT | Port main thruster |
| 3 | `CT_STBD_THRUST` | OUT | Starboard main thruster |
| 4 | `CT_PORT_AZIMUTH` | OUT | Port azimuth servo |
| 5 | `CT_STBD_AZIMUTH` | OUT | Starboard azimuth servo |
| 10 | `CT_PW_CHS` | OUT | Spare R/C PWM — currently not used |

### RC Control (Digital Input)

| Pin | Constant | Purpose |
|-----|----------|---------|
| 6 | `RC_ACTIVE_N` | LOW = MANUAL mode, HIGH = AUTONOMOUS |
| 9 | `RC_KILL_ACTIVE_N` | Kill switch input |

### Power / Sensing (Analog)

| Pin | Constant | Purpose |
|-----|----------|---------|
| A4 | `V12_SENSE` | 12V rail sense |
| A5 | `V24_SENSE` | 24V rail sense |
| A12 | `TEMPERATURE_SENSOR` | Board temperature |
| A13 | `MAIN_BATTERY_SENSE` | Main battery voltage |

### Power Enables (Digital Output)

| Pin | Constant | Purpose |
|-----|----------|---------|
| 38 | `PROPULSION_MC_EN` | Propulsion motor controller enable |
| 39 | `V24_EN` | 24V rail enable |
| 40 | `V12_EN` | 12V rail enable |
| 41 | `CPU_PWR_EN` | CPU power enable |

### Light Tower (Digital Output)

| Pin | Constant | Color |
|-----|----------|-------|
| 31 | `ANCHOR_LIGHT` | White anchor light |
| 32 | `NAV_LIGHTS` | Navigation lights |
| 33 | `LIGHT_TOWER_YEL` | Yellow |
| 36 | `LIGHT_TOWER_GRN` | Green |
| 37 | `LIGHT_TOWER_RED` | Red |

---

## Serial Port Mapping

| Port | Device | Protocol | Notes |
|------|--------|----------|-------|
| Serial1 | Torqeedo PCU | Proprietary | Motor controller |
| Serial2 | AIRMAR 110WX Weather Station | NMEA-0183 | Wind speed/dir, air temp, pressure |
| Serial3 | RoboteQ MDC1460 (Port motor) | RoboteQ ASCII | Port motor controller |
| Serial4 | RoboteQ MDC1460 (Stbd motor) | RoboteQ ASCII | Starboard motor controller |
| Serial5 | AIRMAR DT800 Flow Sensor | NMEA-0183 | Receive only — water speed/depth |
| Serial6 | Digi International XT09-SI RF Modem | Serial | RF communications |
| Serial7 | SBUS RC Receiver | SBUS | R/C transmitter input |
| Serial8 | BlueRobotics PING Sonar | Ping Protocol | Depth sensor |
| USB (Serial) | NVIDIA Jetson companion computer | Custom ASCII | 9600 baud — command/telemetry |

---

## Jetson Serial Packet Format

### Command (Jetson → Teensy)
```
<portThrust, portAzimuth, stbdThrust, stbdAzimuth, red, yellow, green, checksum>
```

| Field | Type | Description |
|-------|------|-------------|
| portThrust | int | Port thruster command (PWM us) |
| portAzimuth | int | Port azimuth servo command |
| stbdThrust | int | Starboard thruster command |
| stbdAzimuth | int | Starboard azimuth servo |
| red | int | Red light tower state |
| yellow | int | Yellow light tower state |
| green | int | Green light tower state |
| checksum | int | Packet integrity check |

### Telemetry (Teensy → Jetson)
Sent via `send_jetson()` — includes: depth, pressure, battery voltage, RC mode flag, wind data.
Exact format: **UNKNOWN** — not fully documented in source; requires field inspection.

---

## Control Logic

### Mode Selection

| Mode | Condition | Thruster Source |
|------|-----------|----------------|
| MANUAL | `RC_ACTIVE_N` LOW | SBUS RC transmitter directly |
| AUTONOMOUS | `RC_ACTIVE_N` HIGH | ROS/Jetson serial commands |
| KILL | `RC_KILL_ACTIVE_N` active | All thrusters to neutral (1500 μs) |

### Thruster System
- WAM-V 2 uses the **Torqeedo thruster system** with PCU
- Translation: SBUS → PCU commands OR ROS → PCU commands
- Motor updates sent periodically; `RC_ACTIVE_N` determines command source
- `isAutoFlag`: `true` = AUTONOMOUS (use Jetson), `false` = MANUAL (use SBUS)

---

## Known Bugs / Issues

| ID | Description | Status |
|----|-------------|--------|
| WV2-4 | Jetson-to-Teensy serial packet format not fully documented | Open — field inspection needed |
| BUG-4 | Light tower ROS subscription not implemented (`// TO DO: Add ROS subscription for light tower control`) | Open |

---

## Related Files

- [WAM-V 2 Hardware](hardware.md)
- [WAM-V 2 ROS Interface](ros_interface.md)
- [WAM-V 2 Operations](operations.md)
- [TowFish Firmware](../towfish/firmware.md) — for comparison (uses same PCU board)
