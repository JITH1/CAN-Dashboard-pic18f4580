# 🚗 CAN-Dashboard-pic18f4580

A 3-node **CAN bus telemetry system** built on **PIC18F4580** microcontrollers, simulating a real vehicle instrument cluster. Two sender ECUs broadcast RPM, turn-indicator, speed, and gear data over CAN; a receiver ECU decodes the frames and renders them live on a character LCD dashboard. **Build using embedded protocol : CAN (Controller Area Network).**

---

## 📋 Overview

This project models a simplified automotive network: independent nodes producing sensor/input data, a shared CAN bus for transport, and a dedicated display node acting as the driver-facing instrument cluster — the same architectural pattern used in real vehicle body/powertrain networks. 🔧


<img width="3011" height="6778" alt="diagram (1)" src="https://github.com/user-attachments/assets/0371281e-d2b9-4dde-a12b-7bc891008893" />

---


## 🧩 Nodes

| ECU | Role | Inputs | CAN ID (SID) | Notes |
|---|---|---|---|---|
| **ECU1** 🎛️ | RPM + indicator transmitter | Potentiometer (RPM sim.), 4 push-buttons (L/R/hazard) | `0x1A` (RPM), `0x18` (indicator) | ADC sampled every loop, sent every 50 cycles |
| **ECU2** 🎚️ | Speed + gear transmitter | Potentiometer (speed sim.), up/down buttons (gear) | `0xA5` (speed), `0x2C` (gear) | Gear state machine: R, N, 1–5 |
| **ECU3** 🖥️ | Dashboard receiver | CAN bus | Filters on all 4 IDs above | Drives 16x2 CLCD + Timer0-interrupt indicator LED blink |

---

## ✨ Features

- 🆔 Custom CAN frame layout with dedicated standard IDs per signal (RPM, indicators, speed, gear)
- 🛡️ Hardware acceptance filtering on ECU3 (`RXF0–RXF3`) so only relevant IDs reach the buffers, using both RXB0 and RXB1 with masks explicitly configured (undefined at POR otherwise)
- 📊 ADC-based analog sensor simulation (10-bit, right-justified) for RPM and speed
- 🔘 Debounced 4-button digital input handling for indicators and gear shifting
- ⏱️ Timer0-interrupt-driven indicator LED blinking on the dashboard node, decoupled from the CAN receive loop
- 🖥️ 16x2 character LCD live-updated with RPM, indicator, speed, and gear fields

---

## Working Demonstration 📽️

- The DIP 8-Way Switch is off during capturing , so you guys can't see the 💡 LED pattern blinking for respective indicator and hazard . I designed it using Timer0 which toggles the led in a fixed interval .

https://github.com/user-attachments/assets/e49b608a-d6d3-44e3-83ba-ea90d8721f98

--- 

## 🔧 Hardware

- 🧠 3x PIC18F4580 (20 MHz crystal, `_XTAL_FREQ = 20000000`)
- 📡 CAN transceiver per node (e.g. MCP2551/2561) wired to `RB2` (TX) / `RB3` (RX)
- 🎚️ Potentiometers on `AN4` for RPM/speed simulation
- 🔘 Push-buttons on `PORTC<3:0>` for indicator and gear inputs
- 🖥️ 16x2 character LCD on ECU3
- 💡 Indicator LEDs on `PORTB` (ECU3)

---

## 📁 Repository Structure

```
ECU1_normal.X/       # RPM + indicator transmitter
├── main.c
├── can.c
└── can_h.h

ECU2_Normal.X/        # Speed + gear transmitter
├── main.c
├── ecu2_can.c
└── ecu2.h

ECU3_normal.X/        # Dashboard receiver
├── main.c
├── can_Rx.c
├── can.h
├── clcd.c
└── clcd.h
```

Each folder is a standalone **MPLAB X IDE** project (XC8 toolchain).

---

## 🛠️ Building & Flashing

1. 📂 Open the desired `.X` folder in **MPLAB X IDE**.
2. ⚙️ Ensure the **XC8 compiler** is installed and selected as the toolchain.
3. 🎯 Set the target device to `PIC18F4580`.
4. 🔨 Build (`production` configuration) and flash via PICkit/ICD to each board.
5. 🔗 Wire all three boards to a common CAN bus (with termination resistors) and power on ECU3 last to watch the dashboard populate as ECU1/ECU2 start transmitting.

---

## 🐛 Debugging Notes

Getting reliable multi-ID filtering on ECU3 surfaced a few classic CAN/PIC pitfalls, resolved during development:

- ⚠️ Standard ID filter/mask mismatches that silently dropped valid frames
- 🔤 Incorrect register bit names (`RXB0FUL` → `RXB0CONbits.RXFUL`)
- 🔀 RXB1 messages read using RXB0 registers by mistake
- 🎭 `RXM0`/`RXM1` mask registers left at their undefined post-reset state instead of being explicitly initialized

---

## 🚀 Possible Extensions

- 🔄 Move from polling to CAN receive-interrupt-driven dashboard updates
- 🛑 Add bus-off/error-frame handling and recovery
- 🖼️ Replace the character LCD with a graphical display
- 📶 Log live traffic over UART for bench-side diagnostics

---

## 👤 About Me

**JITHIN P**
🎓 Electronics and Communication Engineering graduate
📍 Bengaluru, India

**Skilled in:**
- 🔩 Embedded Systems & Bare-Metal Coding
- 💻 Firmware Development
- 🧮 Data Structures & Algorithms (DSA)
- ⚡ C++
- 🔧 C Programming
- 🐧 Linux Internals
- 📡 Protocols: CAN, SPI, I2C, UART

### 🔗 Connect with me

[![GitHub](https://img.shields.io/badge/GitHub-JITH1-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/JITH1)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/jithinjith)
