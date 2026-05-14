The Autonomous IoT Controller is a smart water pump.The Autonomous IoT Controller is a smart water pump.

An application developed for Android that can safely control, program and independently control a series of industrial and household water pumps.

Smart Lighting will be OK with a missed packet in the network but Water Pump Automation won't. If "Turn Off" is not performed, the floodwater or hardware destroys the computer. The design of this project was designed with the principle of fail-safe automation, hierarchy of devices, and closed-loop sensor feedback.

## 📱 Interface Showcase
Side by side composite of the Multi-Device Dashboard, the Sensor & Tank Level UI and Timer Configuration screens.

## 🎯 The Problem & Purpose
Agricultural and industrial users typically have several water pumps linked to large water storage tanks. Traditional remote switches are hazardous – if the Internet goes down while a pump is operating, the person using the remote switch can not turn the pump off and will experience flooding. Also, manual or fixed timers are ineffective and hazardous for filling tanks.

**The Solution:**
Designed a control application using a Firebase Cloud, Local Wi-Fi Sockets, and SMS fallback pipeline in triple redundancy configuration. Most importantly, I took the automation logic from the cloud and into the hardware. The app sets the hardware to take measurements for the upper and lower water limit sensors. These limits are used by the ESP8266 microcontroller to take control of water's supply autonomously, and the mobile application is a monitor and configuration agent.

## 🏗️ Architecture & Tech Stack

The platform develops native Android apps using the Java and XML languages.The platform is native Android (Java, XML).
A real-time database that updates data across states and regions.A real-time database that syncs data across states and regions.
The Hardware Target is ESP8266 Microcontrollers.
This document does not cover other networking options.Other networking options are not addressed in this document.
The Data Persistence module is provided by SharedPreferences or SQLite.SharedPreferences / SQLite are the modules provided to provide Data Persistence.

### The Data Hierarchy
The application has a deeply nested state tree managed:
On the Master Account screen, select Gateway Devices, Pump Nodes, Sensors (Upper/Lower Limits) & Timers.Select Gateway Devices, Pump Nodes, Sensors (Upper/Lower Limits) & Timers from the Master Account screen.

## ⚡ Key Engineering Decisions

### 1. Closed-Loop Autonomous Regulation
Created an interface for the configuration of pumps and actual tank sensors. 
When the water level drops below the lower level sensor, the pump will automatically turn on when the system is dry run protected to ensure water supply.
Upper Limit (Flood Protection): The top sensor is connected to the pump and the water will switch off the pump as soon as it touches the sensor. 
These state changes are triggered by hardware and the app listens to these changes through Firebase/Sockets and updates dashboard UI real-time and without user refresh.

### 2. Dual-Mode Manual Failsafes
When a physical sensor fails, the user must have manual overrides which are still structurally sound:
Users can schedule multiple, day-specific alarms (e.g. ON at 06:00, OFF at 08:00). The app checks the array in order to prevent time collision errors before flashing it to the hardware.
The “Fire & Forget” Countdown: Users can activate the countdown with “Manual ON” but with the app there is always a mandatory time period for the countdown to automatically end. This is like a software dead-man's switch, which means that the motor will automatically turn off should the user forget or lose Internet connection.

### 3. Multi-Tier Access Control (RBAC)
In an industrial environment there are several operators. I created a local login system in which two roles were created: Owner and User.
The Owner has access to the Master PIN which enables them to add/remove devices, edit schedules and set up the rules for the sensors.
Limited User PINs are available to assign to staff by the Owner. The app allows them to restrict employees to toggle ON/OFF controls and monitor dashboard, and it automates the UI based on the role.

### 4. Triple-Redundancy Networking
The app uses Firebase for real-time remote syncing, goes to a direct IP socket connection for zero-latency on-site control, and provides an SMS command generator for remote rural areas that are outside 3G/4G range.

## 🐛 Edge Cases Handled
Improved: Hardware Overrides UI: In the state-conflict scenario where a user tries to manually set the pump 'ON' through the app while the upper tank sensor is also reporting a flooding risk, defaulted to strict hardware safety overrides.
Optimistic UI with Handshakes: Avoids UI tearing by having optimistic state change flips that will roll back to their previous state if the hardware doesn't send a confirmation packet in 3000ms.
