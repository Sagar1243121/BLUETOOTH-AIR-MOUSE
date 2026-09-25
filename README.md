# Bluetooth Air Mouse

A wearable-style wireless air mouse built using an **ESP32**, **MPU6050**, and physical buttons. The device detects hand/wrist rotation and converts motion into mouse movement over Bluetooth HID.

## Project Overview

The Bluetooth Air Mouse provides mouse control without a conventional desktop mouse. The ESP32 reads rotational motion from the MPU6050 gyroscope, processes the signal using sensitivity reduction, smoothing and a dead-zone filter, and sends mouse HID commands through Bluetooth.

### Main Features

- Wireless Bluetooth HID mouse
- Gyroscope-based cursor movement
- Left click
- Right click
- Long press left button → scroll up
- Long press right button → scroll down
- Rechargeable battery operation
- Compact custom enclosure
- ESP32-based embedded system

## Hardware

- ESP32 Dev Module
- MPU6050
- 2 × push buttons
- 2000 mAh 3.7 V Li-ion battery
- TP4056 charging/protection module
- 5 V boost converter if required by the selected ESP32 power input
- Wires and enclosure

## Connections

### MPU6050 → ESP32

| MPU6050 | ESP32 |
|---|---|
| VCC | 3.3V |
| GND | GND |
| SDA | GPIO 21 |
| SCL | GPIO 22 |

### Buttons

| Function | ESP32 |
|---|---|
| Left button | GPIO 18 |
| Right button | GPIO 19 |

The firmware uses `INPUT_PULLUP`, so each push button is connected between its GPIO pin and GND.

### Power

Use a suitable protected Li-ion charging/power arrangement. Do not connect a raw Li-ion cell directly to a 3.3 V GPIO pin. If using a boost converter, set its output to the voltage required by the specific ESP32 board's power-input pin before connecting it.

## Software

- Arduino IDE
- ESP32 board package
- `BleMouse` library
- `MPU6050_tockn` library

## Firmware

Open:

`firmware/bluetooth_air_mouse.ino`

Select the correct ESP32 board and COM port, then upload.

## How It Works

```text
Hand/Wrist Rotation
        ↓
     MPU6050
        ↓
   ESP32 I2C
        ↓
Gyroscope readings
        ↓
Sensitivity scaling
        ↓
Smoothing filter
        ↓
     Dead zone
        ↓
 Bluetooth HID
        ↓
Computer / compatible device
```

## Motion Processing

The firmware reads:

- Gyro X → horizontal cursor movement
- Gyro Y → vertical cursor movement

The raw values are scaled down, smoothed and converted to integer mouse movement values.

The dead zone reduces very small movements that could otherwise cause cursor drift.

## Known Limitation

Gyroscope-only tracking can experience drift because the gyroscope measures angular velocity and small sensor bias errors accumulate over time.

A future version can improve this using:

- Accelerometer + gyroscope sensor fusion
- Complementary filter
- Kalman filter
- Automatic zero calibration
- Better power regulation
- Custom PCB
- 3D-printed enclosure
- Configuration application

## Future App Configuration

A future version can expose settings such as:

- Cursor sensitivity
- Smoothing
- Dead-zone size
- Scroll speed
- Button functions
- Recalibration

The configuration protocol should be designed separately from the HID mouse channel, for example using a BLE GATT configuration service.

## Project Applications

- Presentation control
- Accessibility
- Hands-free computer interaction
- Gaming experiments
- Human-computer interaction research
- Wearable interface prototypes

## Author

**Sagar**  
AIML Student

## License

MIT License.
