# RFID Attendance System Using NodeMCU and Google Sheets

A real-time, contactless attendance logging system built with an **RC522 RFID reader**, a **NodeMCU (ESP8266)** microcontroller, and **Google Sheets** as a free cloud database. Each tap of an RFID card/tag instantly logs the card holder's name along with the date and time — no manual registers, no paid backend, no dedicated server.

## Features

- 📡 **Contactless & instant** — attendance is logged in seconds with a single tap
- ☁️ **Cloud-based logging** — data is written straight to a Google Sheet via Google Apps Script, viewable from any device
- 🔊 **Audio feedback** — an onboard buzzer confirms every successful scan
- 💸 **Low-cost** — built entirely from inexpensive, widely available components
- 🗄️ **No database required** — Google Sheets acts as a free, zero-maintenance backend
- 🔌 **Simple wiring** — SPI connection between the RC522 and NodeMCU, no extra shields needed

## How It Works

1. A user taps their RFID card/tag near the **RC522** reader.
2. The RC522 reads the tag's unique UID and passes it to the **NodeMCU** over SPI.
3. The NodeMCU firmware matches the UID against a stored list of card holders and builds a timestamp.
4. The NodeMCU connects to Wi-Fi and sends an HTTP(S) request to a **Google Apps Script Web App** URL.
5. The Apps Script parses the request and appends a new row — **Name, Time, Card Holder Name** — to the linked **Google Sheet**.
6. The buzzer sounds to confirm the scan was logged successfully.

## Hardware Components

| Component | Purpose |
|---|---|
| NodeMCU ESP8266 | Wi-Fi enabled microcontroller — runs the firmware and handles connectivity |
| RFID-RC522 Module | 13.56 MHz RFID reader/writer, communicates via SPI |
| RFID Tags / Cards | Passive tags with a unique, factory-set UID |
| Buzzer | Audio feedback on scan |
| Breadboard & Jumper Wires | Prototyping the circuit |
| USB Power Supply | Powers the NodeMCU |

## Circuit / Wiring

Connect the RC522 to the NodeMCU as follows:

| RC522 Pin | NodeMCU Pin |
|---|---|
| RST | D4 (GPIO2) |
| SDA (SS) | D8 (GPIO15) |
| MOSI | D7 (GPIO13) |
| MISO | D6 (GPIO12) |
| SCK | D5 (GPIO14) |
| GND | GND |
| 3.3V | 3V3 |

> ⚠️ The RC522 is a **3.3V** device — do not power it from the 5V pin.

The buzzer's positive lead connects to a spare digital GPIO (e.g. D3) with the negative lead to GND.

See `circuit_diagram.png` in this repo for the full wiring diagram.

## Software Setup

### 1. Arduino IDE
- Install the **ESP8266 board package** in Arduino IDE (Boards Manager → search "esp8266").
- Install the **MFRC522** library (Sketch → Include Library → Manage Libraries → search "MFRC522").
- Select your NodeMCU board (`NodeMCU 1.0 (ESP-12E Module)`) and the correct COM port.

### 2. Google Apps Script (Web App backend)
1. Create a new Google Sheet with columns: `Name`, `Time`, `Card Holder Name`.
2. Open **Extensions → Apps Script** and write a script that:
   - Accepts GET/POST parameters (name, time, card holder name).
   - Appends them as a new row using `SpreadsheetApp`.
3. Deploy the script as a **Web App** (Deploy → New deployment → Web app), with access set to "Anyone".
4. Copy the deployment URL — this is the endpoint your NodeMCU firmware will call.

### 3. Firmware
1. Open the `.ino` sketch in this repo.
2. Update the following in the code:
   - Wi-Fi SSID and password
   - Google Apps Script Web App URL
   - UID-to-name mapping for each registered RFID tag
3. Upload the sketch to the NodeMCU.
4. Open the Serial Monitor (115200 baud) to confirm Wi-Fi connection and successful scans.

## Usage

1. Power on the NodeMCU (via USB).
2. Wait for it to connect to Wi-Fi (confirmed on Serial Monitor / status LED).
3. Tap a registered RFID card/tag on the RC522 reader.
4. The buzzer beeps to confirm the scan.
5. Check the linked Google Sheet — a new row appears with the date, time, and card holder's name.

## Repository Structure

```
├── RFID_Attendance_System.ino     # NodeMCU firmware
├── circuit_diagram.png            # Wiring diagram (RC522 to NodeMCU)
├── hardware_setup.jpg             # Photo of the assembled prototype
├── sheet_output.png               # Sample attendance log in Google Sheets
├── RFID_Attendance_System.pptx    # Project presentation
└── README.md
```

## Future Scope

- Add an OLED/LCD display for instant on-device scan feedback
- Send email/SMS/app notifications on each scan
- Add facial recognition as a second verification factor
- Build a web dashboard for attendance analytics and reports

## Applications

- Schools & colleges — student attendance tracking
- Offices — employee clock-in / clock-out logs
- Labs & libraries — controlled equipment or room access
- Events — quick check-in for registered attendees

## License

This project is open-source — feel free to use, modify, and distribute it. Add your preferred license (e.g. MIT) here.
