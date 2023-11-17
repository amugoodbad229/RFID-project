# RFID Door Lock System with Temperature Screening

## Overview

This project implements an RFID-based door lock system with integrated temperature screening. The system uses RFID technology for contactless user identification and an infrared thermopile sensor for measuring body temperature during authentication. 

The core goals of this project are:

- Touchless biometric fever screening for access control
- Enhanced security through RFID user authentication
- Improved hygiene and convenience over traditional access methods

## System Architecture

The system consists of the following key components:

- **Arduino UNO** - Microcontroller for integrating all modules
- **RC522 RFID Reader** - Reads RFID tag data over 125 kHz frequency
- **MLX90614 IR Thermopile** - Non-contact temperature sensor 
- **16x2 LCD** - Displays access prompts, alerts and real-time data
- **Buzzer** - Audio notification for access denial alerts
- **SG90 Servo** - Motorized mechanism for lock/unlock

The RC522 continuously polls for nearby RFID tags. When detected, the tag UID is extracted and verified against a database of authorized IDs. If valid, the MLX90614 sensor is triggered to acquire the user's temperature. The measurement is analyzed by threshold logic to determine access. Under normal conditions, the servo unlocks the door. At elevated temperatures, access is denied.

## Hardware Setup

The circuit diagram below illustrates the component wiring:

<img src="https://github.com/amugoodbad229/RFID-project/blob/main/Hardware%20setup.jpeg" width="100">
The thermopile sensor is positioned facing outward near the access point at a ~5cm distance from the expected hand position. The LCD panel, buzzer and RFID reader are also mounted near the access point. The servo module is connected to the lock.

## Software Flow

()
<img src="https://github.com/amugoodbad229/RFID-project/blob/main/RFID%20flowchart%20new.png" width="100">
The Arduino program performs the following sequence:

1. Continuously poll for new RFID tags in range 
2. When found, extract unique UID 
3. Verify if UID is in the authorized database
4. If authorized, trigger MLX90614 temperature reading
5. Apply temperature threshold logic
   1. Temp < 36.5C: Unlock door
   2. 36.5C < Temp < 37.5C: Lock door, mild fever warning
   3. Temp > 37.5C: Lock door, high fever alert 
6. Reset system to detect next tag

## Usage Guide

To operate the system:

1. Hold RFID card within 2–5 cm of the reader
2. Keep hand at a distance of 4-6cm from thermopile sensor
3. Wait 2-3 seconds for temperature measurement
4. If authorized and no fever detected, door will unlock
5. Follow LCD instructions for all other use cases  

## Future Enhancements

- Add WiFi connectivity for cloud logging, alerts and analytics
- Implement machine learning model for intelligent access control based on trends
- Encrypt RFID communication for security against adversaries
- Account management system for secure user enrollment
