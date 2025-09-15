# RFID Door Lock System with Integrated Temperature Screening

A touchless access control prototype that combines RFID user authentication with non-contact temperature screening for added safety and hygiene.

[![Platform: Arduino](https://img.shields.io/badge/Platform-Arduino-00979D?logo=arduino&logoColor=white)](https://www.arduino.cc/)
![Language: C++](https://img.shields.io/badge/C%2B%2B-17-blue?logo=c%2B%2B)
![Repo size](https://img.shields.io/github/repo-size/amugoodbad229/RFID-project)
![Last commit](https://img.shields.io/github/last-commit/amugoodbad229/RFID-project)

---

## Table of Contents
- [Overview](#overview)
- [Features](#features)
- [Hardware](#hardware)
- [System Architecture](#system-architecture)
- [Software Flow](#software-flow)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Library Dependencies](#library-dependencies)
  - [Configuration](#configuration)
  - [Build and Upload](#build-and-upload)
- [Usage](#usage)
- [Troubleshooting](#troubleshooting)
- [Roadmap](#roadmap)
- [Safety Notes](#safety-notes)
- [Acknowledgments](#acknowledgments)
- [License](#license)

---

## Overview

This project implements an RFID-based door lock system with integrated temperature screening. The system uses an MFRC522 RFID reader for contactless user identification and an MLX90614 infrared thermopile sensor for measuring body temperature during authentication. A 16x2 LCD provides user feedback, a buzzer signals alerts, and a servo actuates the lock.

Core goals:
- Touchless fever screening during access control
- Enhanced security via RFID authentication
- Improved hygiene and convenience over traditional methods

---

## Features
- RFID authentication using MFRC522 (13.56 MHz)
- Non-contact temperature measurement via MLX90614
- Configurable temperature thresholds for alerts and lock behavior
- Visual feedback on 16x2 LCD; audible feedback via buzzer
- Servo-based door lock actuation
- Clear, guided user flow with minimal interaction time

---

## Hardware

Key components:
- Arduino Uno (or compatible)
- MFRC522 RFID Reader (SPI, 13.56 MHz)
- MLX90614 IR Thermopile (I2C)
- 16x2 LCD (parallel or I2C backpack)
- SG90 (or similar) micro servo
- Buzzer
- Breadboard, jumper wires, and suitable power source

Example wiring and physical layout are shown here:
- Hardware setup:
  <img src="https://github.com/amugoodbad229/RFID-project/blob/main/Hardware%20setup.jpeg" width="350">
- Software flowchart: shown in [Software Flow](#software-flow)

Note: Pin mappings can vary based on your sketch. If you change pins in code, update wiring accordingly.

---

## System Architecture

High-level operation:
1. The RC522 module continuously polls for nearby RFID tags.
2. On detecting a tag, the UID is read and checked against an authorized list.
3. If authorized, the MLX90614 takes a temperature reading.
4. Based on configured thresholds, the system either unlocks the door or denies access with an appropriate message and buzzer signal.
5. The system resets and waits for the next tag.

Primary modules:
- Arduino Uno — central controller integrating all peripherals
- MFRC522 — reads RFID tag UIDs over SPI at 13.56 MHz
- MLX90614 — non-contact temperature sensing over I2C
- 16x2 LCD — user prompts and status messages
- Buzzer — audible alerts
- Servo — lock/unlock actuation

---

## Software Flow

<img src="https://github.com/amugoodbad229/RFID-project/blob/main/RFID%20flowchart.jpeg" width="350">

Default decision logic:
- Temp < 36.5°C: Unlock door
- 36.5°C ≤ Temp ≤ 37.5°C: Keep locked, mild fever warning
- Temp > 37.5°C: Keep locked, high fever alert

These thresholds can be adjusted in the code (see [Configuration](#configuration)).

---

## Getting Started

### Prerequisites
- Arduino IDE (2.x recommended) or PlatformIO
- A supported Arduino board (e.g., Uno)
- USB cable and drivers installed

### Library Dependencies
Install via Arduino IDE Library Manager where possible:
- MFRC522 by GithubCommunity or Miguel Balboa (search “MFRC522”)
- Adafruit MLX90614 library (search “Adafruit MLX90614”)
- Servo (built-in)
- LiquidCrystal (built-in) or LiquidCrystal_I2C (if using I2C LCD backpack)

Note: Ensure the selected libraries match the includes used in the sketch.

### Configuration
In the main sketch:
- Set authorized UIDs list (array/string set containing tag UIDs)
- Adjust temperature thresholds:
  - MIN_CLEAR_TEMP_C (e.g., 36.5)
  - MAX_CLEAR_TEMP_C (e.g., 37.5)
- Confirm pin assignments for:
  - MFRC522: SS(SDA), RST, SCK, MOSI, MISO
  - MLX90614: SDA, SCL
  - Servo signal pin
  - Buzzer pin
  - LCD type (parallel vs I2C) and address (if I2C)

Tip: Test each module independently first (RFID read, temperature read, servo motion, LCD text).

### Build and Upload
1. Connect your Arduino board via USB.
2. Open the sketch in Arduino IDE.
3. Select the correct board and COM port.
4. Install missing libraries if prompted.
5. Click Upload.
6. Open Serial Monitor (optional) for debugging output.

---

## Usage
1. Present an authorized RFID card within ~2–5 cm of the reader.
2. Hold your hand/forehead at ~4–6 cm from the MLX90614 sensor.
3. Wait 2–3 seconds for measurement and decision.
4. If authorized and within safe temperature range, the servo unlocks the door.
5. Follow on-screen LCD instructions for any alerts or errors.

---

## Troubleshooting
- RFID not detected:
  - Verify SPI wiring (SS, RST, SCK, MOSI, MISO) and 3.3V power to MFRC522.
  - Check library versions and that SPI is enabled for your board.
- Temperature reads 0 or NaN:
  - Confirm I2C wiring (SDA/SCL), pull-ups, and correct address (default 0x5A).
  - Ensure the Adafruit MLX90614 library is installed.
- LCD shows garbled text or nothing:
  - Verify contrast (for parallel), I2C address (0x27 or 0x3F are common), and wiring.
- Servo jitter or weak motion:
  - Provide adequate 5V power; avoid powering the servo from the Arduino’s 5V if it browns out.
  - Common ground between servo and Arduino is required.
- Buzzer silent:
  - Check polarity (if active buzzer) and confirm pin mode/output in code.

---

## Roadmap
- Wi‑Fi connectivity for cloud logging, alerts, and analytics
- Encrypted RFID communication for improved security
- Role-based account management and secure user enrollment
- On-device settings menu (via buttons) for thresholds and calibration
- Optional mask detection or additional sensors
- Basic data logging to EEPROM/SD
- Intelligent access heuristics (experimentation)

---

## Safety Notes
- This is a hobbyist prototype and not a medical device.
- MLX90614 readings can be affected by distance, emissivity, and environment; calibrate and validate for your use case.
- Do not rely solely on this system for health screening or safety-critical access control.

---

## Acknowledgments
- MFRC522 library by the open-source community
- Adafruit MLX90614 library
- Arduino community examples and documentation

---

## License
This repository currently does not include a license file. Consider adding a LICENSE (e.g., MIT, Apache-2.0) to clarify usage and distribution terms.
