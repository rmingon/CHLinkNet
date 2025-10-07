# CH32V307 Ethernet + PoE Development Board

## Overview

This project is a **custom 4-layer development board** based on the **WCH CH32V307RCT6** RISC-V microcontroller.  
It integrates **Ethernet connectivity**, **USB programming**, and **Power-over-Ethernet (PoE) readiness** through an external module header.

The design was fully created in **KiCad 9.0.3**, including schematic, PCB layout, and 3D mechanical validation.

---

## Key Features

- **MCU:** WCH **CH32V307RCT6**

  - 32-bit RISC-V core @144 MHz
  - 256 KB Flash / 64 KB SRAM
  - Integrated MAC controller for Ethernet
  - USB 2.0 Full-Speed, CAN, ADC, PWM, and multiple GPIOs

- **Ethernet Interface:**

  - **HY931147C** RJ45 connector with integrated magnetics and LEDs
  - RMII interface connected to the CH32V307 MAC peripheral
  - Differential pair routing with impedance control for signal integrity

- **USB Interface:**

  - Micro-USB Type-B connector (J1)
  - **CH340C** USB-to-UART converter for serial programming/debugging
  - USB-powered operation (5 V input)

- **Power Supply:**

  - Input: **5 V** from USB or external PoE module (J3)
  - **LM1117-3.3V** linear regulator provides system 3.3 V rail
  - PoE header allows integration of a DC-DC converter (48 V → 5 V)

- **Clock System:**

  - **25 MHz crystal** (Y1) with 10 pF load capacitors
  - Supports both internal and external oscillators

- **Programming and Debugging:**

  - **1×8 header (J2)** for SWD and UART interface
  - **JP1** (BOOT0) and **JP2** (BOOT1) jumpers for boot mode selection
  - Compatible with WCHISPTool and OpenOCD

- **Status Indicators:**

  - Power LED
  - Dual user LEDs (green and yellow) connected via GPIOs with current-limiting resistors

- **PCB Details:**
  - **4-layer stack-up:**
    - Top: Signal
    - Inner 1: GND plane
    - Inner 2: 3V3 power plane
    - Bottom: Signal
  - Controlled impedance routing for RMII
  - Ground pour under high-speed signals for noise reduction
  - Rounded corners, compact form factor, and 3D-verified mechanical fit

---

## Hardware Architecture

### Power Section

- **Primary Source:** USB 5 V from J1
- **Secondary Source:** PoE module input via J3 header
- **Voltage Regulation:**
  - U3 (LM1117-3.3V) converts 5 V → 3.3 V
  - C4–C6 ensure low-noise decoupling near MCU and PHY
  - Separate power planes on inner layers for 3.3 V and 5 V distribution

### Ethernet Section

- **U1: HY931147C RJ45 connector** with built-in magnetics
- Connected to MCU’s RMII interface pins (TXD, RXD, CRS, REF_CLK, etc.)
- Controlled-impedance differential pairs routed on top layer, referenced to inner GND plane
- Shield and chassis ground isolated via filtering for EMI reduction

### USB-to-UART Interface

- **U4: CH340C** converts USB signals to UART for MCU programming
- Connected to TX_PROG and RX_PROG pins on CH32V307
- Includes pull-up and filtering capacitors for reliable USB operation

### Clock and Reset Circuit

- **Y1: 25 MHz crystal** connected to OSC_IN/OSC_OUT
- **JP1/JP2 jumpers** configure MCU boot mode (Flash, System Memory, SRAM)
- **NRST pin** accessible via programming header (J2)

### Programming Header (J2)

| Pin | Signal   | Description     |
| :-- | :------- | :-------------- |
| 1   | GND      | Ground          |
| 2   | +3V3     | Power reference |
| 3   | RST      | MCU Reset       |
| 4   | RX_PROG  | UART RX         |
| 5   | TX_PROG  | UART TX         |
| 6   | SWCLK    | SWD Clock       |
| 7   | SWDIO    | SWD Data        |
| 8   | NC / RES | Reserved        |

---

## PCB Layout Highlights

- **4-layer design** with solid GND plane and 3V3 power plane
- **Short and symmetric differential routing** for Ethernet and USB signals
- **Via stitching** used to maintain continuous ground reference around high-speed nets
- **Thermal reliefs** on power pads for manufacturability
- **Decoupling capacitors** placed directly next to MCU VDD and PHY pins
- **RJ45 positioned at board edge** for easy cable insertion and mechanical stability

---

## Software & Toolchain

- **Toolchain:**

  - [WCH RISC-V GCC](https://www.wch.cn/downloads/CH32V307.html)
  - [WCHISPTool](https://www.wch.cn/downloads/WCHISPTool.html)
  - [MounRiver Studio](https://www.mounriver.com/)
  - Optional: [OpenOCD](https://openocd.org/) for SWD programming

- **Boot Modes:**
  | BOOT0 | BOOT1 | Boot Mode |
  |:------|:------|:-----------|
  | 0 | 0 | Boot from Flash |
  | 1 | 0 | Boot from System Memory (USB/UART bootloader) |
  | 0 | 1 | Boot from SRAM |

- **Programming Interfaces:**
  - Via USB (CH340C)
  - Via SWD header (J2) using a WCH-Link, J-Link, or OpenOCD adapter

---

## Design Considerations

- Ethernet differential pairs tuned to **50 Ω single-ended / 100 Ω differential impedance**
- Separate **power and ground planes** to minimize noise coupling
- **Short return paths** for high-speed signals
- **ESD protection** recommended on USB and RJ45 lines in next revision
- Provision for **PoE DC-DC converter module** (48 V → 3.3 V)

---

## Future Improvements

- Integrate **onboard PoE power converter**
- Add **ESD suppression arrays** on USB/Ethernet ports
- Replace LM1117 with a **buck converter** for improved efficiency
- Add **test points** for easier debugging and signal probing

---

## License

This project is released under the **CERN Open Hardware License – Strongly Reciprocal (CERN-OHL-S v2)**.  
You are free to study, modify, produce, and distribute this design, provided derivatives remain open-source under the same license.
