# TowFish Firmware Reference (RASP_V2)

**Purpose:** Document the Teensy 4.1 firmware for the TowFish — logic, commands, data flow, libraries, and known issues.
**Last Updated:** 2025-07
**Source Repos:** [Thomas_Towfish](https://github.com/Marine-Robotics-Club/Thomas_Towfish)
**Confidence:** HIGH — extracted directly from `RASP_V2.ino`.

---

## File

- **Source:** [RASP_V2.ino](https://github.com/Marine-Robotics-Club/Thomas_Towfish/blob/main/src/RASP_V2/RASP_V2.ino)
- **Target MCU:** Teensy 4.1
- **Author / Date:** EH, 2/27/2024
- **Version:** RASP_V2

---

## Libraries Used

| Library | Purpose |
|---------|---------|
| `stdio.h` | Standard I/O |
| `SPI.h` | SPI bus (SD card) |
| `SD.h` | SD card file operations |
| `Wire.h` | I2C bus (IMU) |
| `Adafruit_Sensor.h` | Adafruit unified sensor abstraction |
| `Adafruit_BNO055.h` | 9-DOF IMU driver |
| `utility/imumaths.h` | IMU math helpers |

---

## Defines & Constants

| Define | Value | Meaning |
|--------|-------|--------|
| `Xbee` | `Serial5` | XBee radio assigned to hardware UART5, baud 115200 |
| `STAY_ON` | `1` | Power latch constant |
| `U2` | `0` | Array index for hall sensor 2 |
| `U3` | `1` | Array index for hall sensor 3 |
| `U4` | `2` | Array index for hall sensor 4 |
| `HALL1` | `32` | Digital input pin for hall sensor U2 |
| `HALL2` | `25` | Digital input pin for hall sensor U3 |
| `HALL3` | `0` | Digital input pin for hall sensor U4 |
| `VBAT` | `A0` | Analog input: battery voltage |
| `LEAK` | `A1` | Analog input: leak sensor |
| `LED` | `13` | On-board LED output |

---

## Key Functions

| Function | Description |
|----------|-------------|
| `setup()` | Initializes Serial, I/O pins, BNO055 IMU, and SD card. |
| `loop()` | Main loop: reads hall sensors and health, processes command character, runs state machine. |
| `printOnce()` | Outputs sensor data and health values over XBee serial. |
| `printOnceSerial()` | Same as `printOnce()` but outputs to USB serial (for debug). |
| `updateHallSensors()` | Reads all 3 hall sensors with 16-sample debounce. |
| `updateHealth()` | Reads and averages battery voltage (A0) and leak sensor (A1). |

---

## State Machine

The main `loop()` uses a character-based state machine driven by a `cmd` variable. Command characters are received over XBee (`Serial5`) or USB serial.

| Command Char | Action |
|-------------|--------|
| `'S'` | Start saving data to SD card |
| `'q'` | Stop saving / stop streaming |
| `'p'` | Stream data continuously |
| `'u'` | Send data once |
| `'l'` | List files on SD card |
| `'D'` | Dump selected data file |
| `'='` | Select dump file (by number) |
| `'d'` | Display selected dump file name |
| `'~'` | Default / idle state |
| (any other) | Display command menu |

**Hall Sensor Overrides:**
- U2 (pin 32): Sets command to `'S'` (start save)
- U4 (pin 0): Sets command to `'q'` (stop save)
- U3 (pin 25): Power control (hold 3 s = power off; reed relay = power on)

---

## Data Acquisition

### IMU (Adafruit BNO055, I2C 0x28)

Sensor events are read in each data collection tick:

| BNO055 Vector | Fields Written | Units |
|---------------|---------------|-------|
| `VECTOR_ACCELEROMETER` | Accel X, Y, Z | m/s² |
| `VECTOR_GYROSCOPE` | Gyro X, Y, Z | rad/s |
| `VECTOR_EULER` | OrientX, OrientY, OrientZ | degrees |

### CSV Output Format (SD Card)

Files named `RASP_001.csv` through `RASP_099.csv`. A new file is created each time `'S'` (Save) is triggered.

**CSV Header (as written by firmware):**
```
Time (ms),Accel X (m/s^2),Accel Y (m/s^2),Accel Z (m/s^2),Gyro X (rad/s),Gyro Y (rad/s),Gyro Z (rad/s),OrientX (deg),OrientY (deg),OrientZ (deg)
```

> **NOTE:** The firmware-defined CSV header does NOT include pressure, battery, or leak fields. However, the `data_format.md` reference (from the full mission CSV format) includes these fields. The full mission CSV is likely assembled by a separate script or there is a firmware version mismatch. See [data_format.md](./data_format.md) for the full expected format.

### Health Monitoring

- **Battery voltage:** Averaged over multiple ADC reads from pin A0. Quality flag: `battery_v > 20.0`.
- **Leak sensor:** Averaged over multiple ADC reads from pin A1. Status preserved as string. Quality flag: `leak_ok` when status == `'NO_LEAK'`.

---

## SD Card Management

- SD card initialized via `SD.begin(BUILTIN_SDCARD)` (Teensy 4.1 built-in)
- Files: `RASP_001.csv` to `RASP_099.csv` (format `RASP_%03d.csv`)
- Implemented operations:
  - List files (`'l'`)
  - Select dump file (`'='`)
  - Dump file (`'D'`)
  - Display selected file name (`'d'`)
- **NOT IMPLEMENTED:** Delete specific file, Delete all files (per comment in source)

---

## Known Issues & Notes

1. **Duplicate Gyro Columns (INFERRED BUG):** Based on data_format.md analysis, the full mission CSV format shows 36+ fields. The firmware header only has 10 fields. The discrepancy suggests either a different firmware version produces the full CSV, or additional sensors/scripts append data.

2. **Pressure sensor not in firmware:** The `pressure_mbar` field documented in `data_format.md` has no corresponding sensor code in `RASP_V2.ino`. The pressure sensor may be on a separate microcontroller, may have been added in a fork, or may be on the Raspberry Pi companion computer.

3. **XBee vs USB serial:** A comment in line 40 of `RASP_V2.ino` notes: `CHANGE TO Serial to send out USB port instead of radio`. Changing `#define Xbee Serial5` to `#define Xbee Serial` routes all output to USB instead of XBee. This is used for bench testing.

4. **Max 99 files:** The `%03d` file naming scheme supports only 001–099. Behavior when this limit is reached is UNKNOWN (not documented in firmware).

---

## Programming / Deployment

- **IDE:** Arduino IDE or PlatformIO with Teensyduino extension
- **Target:** Teensy 4.1
- **Upload:** Via USB to Teensy 4.1
- **Required Libraries:** Adafruit BNO055, Adafruit Unified Sensor (install via Arduino Library Manager)

---

## Related Files

- [RASP_V2.ino](https://github.com/Marine-Robotics-Club/Thomas_Towfish/blob/main/src/RASP_V2/RASP_V2.ino)
- [hardware.md](./hardware.md)
- [data_format.md](./data_format.md)
- [operations.md](./operations.md)
