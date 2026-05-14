# Smart Water Pump - IoT Controller

A native Android application built to automate and monitor industrial and residential water pump systems. 

Managing remote water pumps requires reliable connectivity and autonomous safety limits to prevent tank overflows or motor dry-runs. This app solves that by moving the automation logic directly to the hardware and using a triple-redundancy network for control.

## 📱 Interface Showcase
*(📸 Insert Image: Dashboard, Sensor & Tank Level UI, Timer Configuration)*

## 🏗️ Architecture & Tech Stack
*   **Platform:** Native Android (Java, XML)
*   **Backend / State Sync:** Firebase Realtime Database
*   **Hardware Target:** ESP8266 Microcontrollers
*   **Networking:** HTTPS, Local TCP/IP Sockets, GSM Telephony API
*   **Data Persistence:** SharedPreferences / SQLite

## ⚡ Core Features

### 1. Autonomous Sensor Regulation (Closed-Loop)
The app allows users to pair specific pumps with physical upper and lower water-level sensors in the tank. 
*   **Lower Limit:** Automatically turns the pump ON when water is low.
*   **Upper Limit:** Automatically cuts power to the pump when the tank is full.
This logic runs locally on the hardware, while the mobile app acts as a real-time monitor and configuration engine, updating its UI instantly when the hardware changes state.

### 2. Timers & Scheduling
If sensors are not being used, the app provides two software-based alternatives:
*   **Cron-style Scheduling:** Users can program specific ON/OFF times for specific days of the week.
*   **Manual Override with Auto-OFF:** If a user manually starts the pump from the app, they must define an automatic shut-off time (e.g., 30 minutes). This acts as a dead-man's switch to prevent flooding if the user forgets to turn it off or loses internet access.

### 3. Triple-Redundancy Networking
To ensure the pumps can always be controlled, the app routes commands through three layers:
1.  **Firebase:** For real-time remote syncing when online.
2.  **Local Sockets (TCP/IP):** For zero-latency, on-site control without an internet connection.
3.  **GSM/SMS:** A secure command protocol for remote agricultural deployments without Wi-Fi or 4G.

### 4. Remote Hardware Maintenance & Security
*   **Remote USSD:** Users can send telecom codes (e.g., `*566#`) from the app to the hardware to check the device's SIM balance and purchase data packages remotely.
*   **Access Control:** The app supports an Owner/User PIN system. Owners have full configuration access, while Users are restricted to simple monitoring and ON/OFF toggling.
*   **Smart Warranty:** Reads the hardware's encrypted first-boot timestamp to display a digital warranty card directly in the app.

## 🐛 Edge Cases Handled
*   **Hardware Overrides:** Handled UI state conflicts where a user attempts to manually force a pump 'ON' while the upper tank sensor is actively reporting the tank is full, prioritizing the hardware safety limit.
*   **Optimistic UI Syncing:** Implemented state toggles that automatically revert in the app if the hardware fails to send a confirmation packet within a specific timeout window.
