![Version](https://img.shields.io/badge/Version-4.0_ADV-blue)
![Hardware](https://img.shields.io/badge/Hardware-Cardputer-orange)
![Platform](https://img.shields.io/badge/Platform-M5Stack-red)
![License](https://img.shields.io/badge/License-Proprietary-gray)
[![Boosty](https://img.shields.io/badge/Support-Boosty-orange)](https://boosty.to/zeloksa)

# 📡 Wi-Fi & BLE Radar ADV (V4.0)

**Wi-Fi & BLE Radar ADV V4.0** is a high-speed direction-finding, deep packet inspection (DPI), and spectrum analysis tool built specifically for the **M5Stack Cardputer / Cardputer ADV**. 

Version 4.0 is a major architectural upgrade: it introduces a **250 Hz Promiscuous Wi-Fi Engine**, an **Active BLE GATT Auditor (`[P]`)**, an automatic close-range **Pinpoint Geiger Locator**, passive **Hidden SSID & Client (`>STA`) unmasking**, **OpenDroneID GPS decoding**, and real-time **Wireless Intrusion Detection (WIDS)** alerts.

> [!IMPORTANT]
> **Source Code Status:** This project is proprietary. The source code is private.  
> **Distribution:** Pre-compiled binary (`.bin`) only via the **Releases** tab and **M5Burner**.

---

## ⚡ V4.0 Technical Highlights

### 📶 Wi-Fi Promiscuous Engine & De-Anonymization
- **250 Hz Channel-Locked Tracking:** In Single Target Radar and Device Info modes, the radio locks directly onto the target's channel instead of running slow full-band scans. It captures Management and Data frames up to 250 times per second and displays a live traffic density counter (**`pps`** — packets per second) to spot active video streams from IP cameras and drones.
- **Hidden SSID Unmasking (`~Name` & `*Name`):** Automatically reveals the real name of hidden networks (`~Name`) by correlating virtual/mesh BSSIDs with open sibling interfaces on the same physical router, or by intercepting directed Probe Response (`0x50`) and Association (`0x00`/`0x20`) frames. If no name is broadcast, it identifies the router model via WPS tags or MAC OUI (`*Name`).
- **Active Client Station Sniffer (`>Name`):** Detects transmitting non-AP client devices (`>STA`) directly from Data frames and Probe Requests. Spots hidden IP cameras (*Hikvision, Dahua, Ezviz, Imou, Reolink, Uniview, Axis, Bosch, Hanwha*), drones (*DJI, Autel*), action cams (*GoPro*), *Flipper Zero*, *Raspberry Pi*, and *ESP32* nodes—showing the AP MAC they are connected to and the SSID they are searching for.
- **802.11 PHY, Load & Security Inspection:** Passively decodes the Wi-Fi standard (**Wi-Fi 6 `11ax`**, **Wi-Fi 4 `11n`**, **`11b/g`**), channel bandwidth (**`20MHz` / `40MHz`**), connected client count (**`STA:`** via QBSS `IE 0x0B` or passive `LiveSTA` traffic counter), channel utilization (**`CU%`**), and Deauth protection status (**`PMF: OFF / OPT / REQ`**).
- **Multi-Region Database (`[R]` + Auto 802.11d):** Over **500+ router and ISP prefixes** stored in Flash with zero RAM overhead (*EU, Asia, RU/CIS, US/Global*). In **`AUTO`** mode, the sniffer reads the ISO country code (`[DE]`, `[JP]`, `[RU]`, `[US]`, etc.) directly from nearby router beacons (`802.11d IE 0x07`) and adapts regional ISP naming automatically.

### 🔵 BLE Deep Packet Inspection (DPI 3.4) & Active GATT Probe
- **Active GATT Probe (`[P]` Key):** Select any BLE target and press **`[P]`** to open a non-pairing HCI connection. A non-blocking FreeRTOS task queries the device to unmask its hidden **GAP Device Name (`0x1800`)**, **Model & Manufacturer (`0x180A`)**, **Firmware Version**, **Hardware PnP ID**, **Exact Battery % (`0x180F`)**, and hidden **HID (`0x1812`) / UART (`0xFFE0`)** services.
- **RPA MAC Rotation Auto-Relock (`FP:XXXX`):** Modern phones and trackers rotate their random MAC address to evade tracking. In Single BLE Radar mode, the firmware calculates a 16-bit structural packet fingerprint (`FP:XXXX`). If your target rotates its MAC address, the radar automatically re-locks onto the new MAC and triggers a **`RPA ROTATED -> RELOCKED`** alert without losing bearing.
- **Extended Protocol & Telemetry Decoders:**
  - **Apple Continuity (`0x004C`):** Live Left / Right / Case battery percentages and charging flags for *AirPods & Beats*; real-time iPhone status (**`[In Call/Audio]`**, **`[Screen ON]`**, **`[Idle/Locked]`**, **`[+Apple Watch]`**, **`[AutoHotspot]`**); separated **`AirTag [Lost]`** detection with battery state; *HomeKit*, *Hey Siri*, and *AirPlay* IPv4 extraction.
  - **Xiaomi MiBeacon (`0xFE95`):** Decodes device models (*Mi Band, Watch, Mijia, Scooter, Aqara Lock, Scale*), extracts the **real factory MAC (`RM:XX:XX...`)** hidden inside the payload, and reads live temperature, humidity, and battery telemetry.
  - **Google & Microsoft:** Decodes 24-bit *Google FastPair* audio models (*Pixel Buds, Sony, JBL, Bose, Nothing, OnePlus, Galaxy Buds*), extracts UTF-8 device names from *Windows SwiftPair*, and separates Android *"Link to Windows"* phones from actual Windows PCs, laptops, and Xbox consoles.
  - **Smart Home, Sensors & Trackers:** Live temperature, humidity, pressure, and battery decoding for *RuuviTag*, *Govee*, *BTHome v2*, *SwitchBot* (bots, meters, smart locks), *Qingping*, plus detection for *Samsung SmartTag*, *Tile*, *Chipolo*, *Google FindMy*, *Tuya*, *Yale/August Locks*, *Matter*, *Meshtastic LoRa* (`0x6BA1`), *Flipper Zero* (Black/White/Transparent), and covert *BLE UART Skimmers* (`HM-10`, `CC254x`, `BM70`, `Nordic NUS/DFU`).
- **Drone ID (ASTM F3411 OpenDroneID & DJI):** Extracts live **UAV GPS coordinates (`Lat, Lon`)**, ground speed (`m/s`), and serial numbers from both BLE (`0xFFFA`) and Wi-Fi Beacon (`FA:0B:BC` / `26:37:12`) broadcasts, as well as active *DJI BLE* controller links (`0x08AA`).

### 🎯 Pinpoint Geiger HUD & Threat Detection (WIDS)
- **Auto Pinpoint Geiger Mode (`<= 1.65m`):** When approaching a locked target within 1.65 meters, the radar automatically switches to a close-range Pinpoint Locator HUD featuring an azimuth alignment tape, converging capture gates, **`WARMER / COLDER / HOTSPOT`** trend indicators, a 16-segment bar with Peak Hold, and dual-tone Geiger counter audio.
- **Gyro-Coupled Kalman Filter & Limb Arc:** Dynamic compass ring on the radar perimeter with a **Confidence Arc** showing bearing lock accuracy, a **36-point live RSSI oscilloscope**, and a **Slew-Rate Guard (`>175°/s`)** that freezes bearing angles during sudden hand jerks.
- **Real-Time RF Attack Alerts (WIDS):** Instant visual and acoustic alarms for **Wi-Fi Deauth / Disassoc attacks** (with numeric Reason Code `RC:X`), **Evil Twin** open rogue AP clones, **Pwnagotchi** units, **Wi-Fi Beacon Floods**, and **BLE Spam** attacks (*Apple Popups, FastPair, SwiftPair, Samsung*).

---

## ⚠️ Important Limitations (Must Read)

1. **2.4 GHz Wi-Fi & BLE ONLY:** The ESP32-S3 hardware supports 2.4 GHz Wi-Fi (802.11b/g/n/ax Channels 1–14) and Bluetooth Low Energy (BLE 4.2/5.0). It cannot see 5 GHz Wi-Fi or legacy "Bluetooth Classic" (BR/EDR) devices such as older car stereos or PS3/PS4 controllers.
2. **Understanding MAC Randomization:** Modern iOS and Android devices broadcast randomized MAC addresses (`[Rnd:RPA]`, `[Rnd:NRPA]`) that rotate every 10–15 minutes. V4.0 counters this with **Packet Fingerprinting (`FP:XXXX`)** for automatic target re-lock in Single BLE Radar mode, **Xiaomi `RM:` payload extraction**, and the **Active GATT Probe (`[P]`)** for connectable devices.

---

## 🛠 Installation

### Method 1: M5Burner (Recommended)
1. Open **M5Burner**.
2. Search for `Wi-Fi & BLE Radar ADV` or `Zeloksa`.
3. Select version **V4.0**.
4. Burn to your M5Stack Cardputer / Cardputer ADV.

### Method 2: Manual Flashing
1. Go to the **[Releases]** tab of this repository.
2. Download the latest V4.0 `.bin` firmware file.
3. Flash to your Cardputer using **M5Burner** (Local File) or **`esptool.py`**.

---

## 🕹 Controls & Hotkeys

| Key | Action |
| :--- | :--- |
| **`[ , ]` / `[ / ]`** | **Left / Right:** Switch menu cards, switch Wi-Fi/BLE columns in List, toggle Bar/Arc mode in CH Analyzer, or turn Manual pages. |
| **`[ ; ]` / `[ . ]`** | **Up / Down:** Scroll devices in List or **switch between adjacent targets directly inside the Device Info screen**. |
| **`[ K ]` / `[ M ]`** | **Page Up / Page Down:** Jump 5 items at a time in the Device List. |
| **`[ ENTER ]`** | Open selected mode, open Device Info, start Single Target tracking, or **Mute/Unmute sonar** in Single Radar. |
| **`[ P ]`** | **Active GATT Probe:** Connect to the selected BLE device to read hidden GAP/DIS names, models, and battery level. |
| **`[ F ]`** | **Cycle Tactical Filter** in List: `ALL` $\rightarrow$ `[F1] KNOWN / ID ONLY` $\rightarrow$ `[F2] TACTICAL & DPI`. |
| **`[ S ]`** | **Cycle Sort Mode** in List (`RSSI` $\rightarrow$ `NEWEST` $\rightarrow$ `NAME/CH`) **OR** load saved NVS calibration on the Calibration screen. |
| **`[ R ]`** | **Cycle Region Profile:** `AUTO (802.11d)` $\rightarrow$ `EU` $\rightarrow$ `ASIA` $\rightarrow$ `RU & CIS` $\rightarrow$ `US / GLOBAL`. |
| **`[ D ]`** | **Purge `[OFF]`:** Remove all inactive (`>15s`) devices from the current history list. |
| **`[ L ]`** | **Save CSV Log:** Export all discovered Wi-Fi and BLE devices with full DPI telemetry to microSD (`/radar_log.csv`). |
| **`[ C ]`** | **Recalibrate IMU:** Zero gyroscope drift in Radar mode while preserving existing target bearings. |
| **`[ I ]`** | **Field Manual:** Open/close the built-in 10-page tactical reference guide from Menu, List, or Info screens. |
| **`[ [ ]` / `[ ] ]`** | Decrease / Increase screen brightness (saved to NVS). |
| **`[ - ]` / `[ = ]`** | Decrease / Increase speaker volume (saved to NVS). |
| **`[ ESC / \` ]`** | Go back / Cancel active GATT Probe. |

---

## 📖 Operational Guide

### 🧭 1. IMU Calibration & Instant Boot Bypass (`[S]`)
When entering Radar mode for the first time, place the Cardputer **FLAT** on a table and press **`[ENTER]`**. Once calibrated, the gyroscope offset is automatically saved to non-volatile memory (**NVS**). On future boots, simply press **`[S]`** on the calibration screen to load your saved calibration immediately while holding the device in your hands.

### 📋 2. Device List, Filters (`[F]`) & Name Prefixes
The dual-column list holds up to **1,000 devices** in history (500 Wi-Fi + 500 BLE).
- **Name Prefixes:**
  - **`~Name`** - Hidden Wi-Fi network unmasked via a sibling BSSID on the same router or captured client association/probe frames.
  - **`*Name`** - Hidden Wi-Fi network identified by router model (WPS IE) or vendor OUI.
  - **`>Name`** - Active 802.11 Client Station (`>STA`) such as a wireless camera, drone, Flipper Zero, Raspberry Pi, or ESP32 node.
- **Tri-Mode Filter (`[F]`):**
  - **`ALL`:** Shows every detected signal.
  - **`[F1] KNOWN / ID`:** Hides raw MACs and generic random phones, keeping identified vendors and `OPEN` networks.
  - **`[F2] TACTICAL & DPI`:** Displays only high-priority targets (DPI-decoded models, drones, cameras, Flipper, unmasked `~`/`*` networks, and `OPEN` APs).

### 🔍 3. Device Inspector & Active GATT Auditor (`[P]`)
Press **`[ENTER]`** on any device in the list to open its live telemetry card (use **`[;]` / `[.]`** to flip through devices without leaving the screen):
- **Wi-Fi Info:** Locks onto the target's channel at 250 Hz, showing live packet rate (`pps`), Wi-Fi generation (`WiFi6`/`WiFi4`), bandwidth (`20/40MHz`), active client count (`STA`), PMF status, WPS model, and a 36-point RSSI oscilloscope.
- **BLE Info & `[P]` Probe:** Shows MAC address type (`[Public]`, `[Rnd:Stat]`, `[Rnd:RPA]`, `[Rnd:NRPA]`), packet fingerprint (`FP:XXXX`), raw hex payload, and decoded DPI data. Press **`[P]`** to actively connect and read internal GATT characteristics (`0x1800`, `0x180A`, `0x180F`).

### 🎯 4. Single Target Tracking & Pinpoint Geiger HUD
Press **`[ENTER]`** inside the Device Inspector to lock onto the target:
- **Far & Mid Range (`> 1.65m`):** Use your body as an RF shield by holding the Cardputer near your chest and rotating 360°. Follow the **Confidence Arc** on the outer compass ring until the **`LOCKED`** badge lights up (`±15°`).
- **Close Range (`<= 1.65m`):** The screen automatically switches to the **Pinpoint Geiger Locator**. Keep the yellow/green triangle centered between the gates `[ | ]`, watch the **`WARMER >>`** / **`<< COLDER`** / **`!! HOTSPOT !!`** indicators, and follow the Peak Hold line on the 16-segment bar to locate the exact physical transmitter.

---

## 🆕 Wi-Fi & BLE Radar ADV (V4.0) Changelog

### Added
- **Added:** **250 Hz Promiscuous Channel Lock** in Single Wi-Fi Radar and Device Info modes with a live **Packets-Per-Second (`pps`)** traffic counter.
- **Added:** **Auto Pinpoint Geiger Locator HUD** (`<= 1.65m`) with azimuth aiming gates, 16-segment Peak Hold bar, Warmer/Colder/Hotspot trend analysis, and dual-tone Geiger audio.
- **Added:** **Active BLE GATT Auditor (`[P]`)** running in an asynchronous FreeRTOS task with live progress HUD, reading GAP Name (`0x1800`), DIS Model/Vendor/FW/PnP (`0x180A`), Battery % (`0x180F`), and HID/UART services.
- **Added:** **RPA MAC Rotation Auto-Relock** using 16-bit packet fingerprinting (`FP:XXXX`) to maintain target lock when a BLE device rotates its random MAC address.
- **Added:** **Passive Hidden SSID Unmasking (`~Name` / `*Name`)** via Sibling BSSID correlation, Probe Responses (`0x50`), Association Requests (`0x00`/`0x20`), and WPS/Vendor IEs.
- **Added:** **Tactical Wi-Fi Client Sniffer (`>STA`)** with an $O(1)$ SRAM filter to detect transmitting cameras (*Hikvision, Dahua, Ezviz, Imou, Reolink, Uniview, Axis, Bosch, Hanwha*), drones (*DJI, Autel*), *GoPro*, *Flipper Zero*, *Raspberry Pi*, and *ESP32* clients.
- **Added:** **802.11 PHY & Load Decoder** for Wi-Fi 6 (`11ax`), `40MHz` width, QBSS Load (`STA` & `CU%`), passive `LiveSTA` client counting (including LAN/5GHz bridges), and `802.11w PMF` status.
- **Added:** **Multi-Region Profile System (`[R]`) & 802.11d Country Sniffer** with **`AUTO`**, `EU`, `ASIA`, `RU/CIS`, and `US/GLOBAL` modes and **500+ PROGMEM OUI prefixes**.
- **Added:** **BLE DPI 3.4 Decoders:** *Apple Continuity* (AirPods L/R/Case battery, iPhone Call/Screen/Watch status, Separated AirTag alerts, HomeKit, Siri, AirPlay IP), *Xiaomi MiBeacon* (PID models, sensor temp/humidity/battery, and real factory MAC `RM:` unmasking), *Google FastPair* 24-bit models, *Microsoft SwiftPair* names, *RuuviTag*, *Govee*, *BTHome v2*, *SwitchBot*, *Qingping*, *Tuya*, *Yale/August*, *Matter*, *Meshtastic LoRa*, and *BLE UART Skimmers*.
- **Added:** **ASTM F3411 OpenDroneID & DJI Decoder** extracting live drone GPS coordinates, speed, and serial numbers over both BLE and Wi-Fi beacons.
- **Added:** **WIDS Tactical Alerts** for Wi-Fi Deauth attacks (with Reason Code), Evil Twin rogue APs, Pwnagotchi, Wi-Fi Beacon Floods, and classified BLE Spam vectors.
- **Added:** **36-Point Live RSSI Oscilloscope** with approach/retreat delta (`±dB`) in Single Radar and Device Info.
- **Added:** **Tactical Filter (`[F]`)**, **Multi-Mode Sorting (`[S]`)**, **MicroSD CSV Logger (`[L]`)**, **NVS Settings & Calibration Persistence**, and a **10-Page Built-in Field Manual (`[I]`)**.

### Improved & Fixed
- **Improved:** Gyro-coupled adaptive 1D Kalman filter with **Weighted Lobe Centroid** bearing calculation, rear body-shadowing protection, and a **Slew-Rate Guard (`>175°/s`)**.
- **Improved:** Direct **`[;]` / `[.]` target switching** inside the Device Info screen and a centered tactical battery gauge in the Main Menu.
- **Fixed:** Eliminated false `[OFF]` list grayouts when returning from Device Info, Help Manual, or GATT Probe sessions.
- **Fixed:** Enforced `esp_wifi_scan_stop()` prior to channel locking so background scans never interrupt 250 Hz single-target tracking.
- **Fixed:** Safe BLE deinitialization (`safeDeinitBLE()`) and strict 14-character String SSO compliance to prevent heap fragmentation during long sessions with 1,000 stored devices.

---

## ☕ Support the Project
Support the development of advanced diagnostic tools for the Cardputer ecosystem:
- **[https://boosty.to/zeloksa](https://boosty.to/zeloksa)**

---
*Developed by Engineer Zeloksa. Strictly optimized for M5Stack Cardputer & Cardputer ADV.*
