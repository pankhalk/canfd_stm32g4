# CANFD Loopback Test – STM32G474

A bare-metal FDCAN internal loopback test for the **STM32G474** microcontroller, built with STM32CubeIDE and the HAL library. The firmware transmits a CAN FD frame and immediately receives it back through the internal loopback path, verifying that the FDCAN peripheral is working correctly.

---

## 📋 Overview

| Property | Value |
|---|---|
| MCU | STM32G474 |
| IDE | STM32CubeIDE |
| HAL Version | STM32G4xx HAL |
| FDCAN Mode | Internal Loopback |
| Clock Source | HSI (Internal) |
| Frame Format | CAN FD (No Bit Rate Switch) |

---

## 🔧 How It Works

1. **Filter Setup** – Configures a standard ID filter to accept only frames with ID `0x111`, exact mask match.
2. **Global Filter** – Rejects all other IDs and remote frames.
3. **FDCAN Start** – Starts the FDCAN1 peripheral in internal loopback mode.
4. **Transmit** – Every 500 ms, sends an 8-byte CAN FD frame:
   ```
   ID:   0x111
   Data: CA FE BE EF 01 02 03 04
   ```
5. **Receive** – Waits 10 ms, then reads back from RX FIFO0.
6. **Verify** – Compares TX and RX data using `memcmp()`. If they match, `testPassed` is set to `1`.

---

## 📁 Project Structure

```
canfd_g474_v1.1/
├── Core/
│   ├── Inc/
│   │   └── main.h
│   └── Src/
│       ├── main.c          ← Main application logic
│       ├── stm32g4xx_hal_msp.c
│       └── stm32g4xx_it.c
├── Drivers/                ← STM32 HAL Drivers
├── canfd_g474_v1.1.ioc     ← CubeMX configuration file
└── README.md
```

---

## ⚙️ FDCAN Configuration

| Parameter | Value |
|---|---|
| Instance | FDCAN1 |
| Clock Divider | DIV1 |
| Mode | Internal Loopback |
| Auto Retransmission | Enabled |
| Nominal Prescaler | 1 |
| Nominal Time Seg1 | 13 |
| Nominal Time Seg2 | 2 |
| Data Prescaler | 1 |
| TX Mode | FIFO |
| Standard Filters | 1 |

---

## 🚀 Getting Started

### Prerequisites
- STM32CubeIDE (v1.16.0 or later)
- STM32G474 Nucleo or custom board
- ST-LINK debugger

### Steps
1. Clone the repository:
   ```bash
   git clone git@github.com:pankhalk/canfd_stm32g4.git
   ```
2. Open STM32CubeIDE → **File → Open Projects from File System**
3. Select the cloned folder and import
4. Build the project: **Project → Build All**
5. Flash and debug: **Run → Debug**

---

## 🐛 How to Verify

1. Open a **debug session** in STM32CubeIDE
2. Add a **breakpoint** on this line in `main.c`:
   ```c
   testPassed = 1;   /* <-- PUT YOUR BREAKPOINT ON THIS LINE */
   ```
3. If the breakpoint is hit → FDCAN loopback is working ✅
4. Check the **Live Expressions** window and watch `testPassed` go from `0` to `1`

---

## 📌 Notes

- This project uses **internal loopback** mode — no external CAN transceiver or wiring is needed.
- The clock is sourced from the internal **HSI oscillator** — no external crystal required.
- `testPassed` is declared `volatile` to prevent compiler optimization from removing it.

---

## 📄 License

This project uses STM32 HAL libraries licensed by STMicroelectronics.  
User application code is provided as-is for educational and testing purposes.
