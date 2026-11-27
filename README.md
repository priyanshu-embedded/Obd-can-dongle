# OBD-II CAN Dongle (Car Dongle v1)

A compact OBD-II plug-in dongle PCB that reads vehicle CAN-bus data and exposes it over wireless links. It combines an **ESP32-S3** main controller, an **ESP32-C5** (2.4 / 5 GHz Wi-Fi 6) co-processor, a **TJA1051T/3** CAN transceiver, an **ATECC608B** secure element, on-board buck converters that run from the car's 12 V supply, and an RGB LED strip plus buzzer for user feedback.

Designed in [EasyEDA](https://easyeda.com) — Version 1.0, created 2026-10-09.

![3D render – top](images/3d_top.png)

## Features

- **16-pin OBD-II male connector** plugs straight into the vehicle (CAN-H / CAN-L, J1850, K/L-line, +12 V, ground pins broken out)
- **CAN interface** – TJA1051T/3 high-speed CAN transceiver with selectable 120 Ω termination (jumper header H1)
- **Dual-MCU design**
  - ESP32-S3-MINI-1U-N8 (U1) – main controller, native USB, external antenna connector
  - ESP32-C5-MINI-1U-N8 (U12) – wireless co-processor, linked to the S3 over SPI (MOSI / MISO / SCLK / CS + HANDSHAKE and DATA_READY lines)
- **Hardware security** – ATECC608B-SSVDA (U4) crypto authentication chip on I²C (4.7 kΩ pull-ups)
- **Robust power path**
  - 2 A resettable fuse (F1)
  - SMCJ24 TVS diode (D2) for load-dump / surge clamping
  - IRF9540NS P-MOSFET (Q1) reverse-polarity protection
  - SM24CANB-02HTG TVS (D1) on the CAN lines
  - LMR16010P buck to **5 V** (U5) and to **3.3 V** (U6), each with 15 µH inductor and SS36 catch diode
- **User interface** – 5× WS2812B-B-V6 addressable RGB LEDs, 2.7 kHz buzzer (driven by 2N7002), EN and BOOT buttons (SW2 / SW4) for flashing
- **Programming / debug** – 2×2 pin headers (H2, H3) exposing USB D+/D− for both modules

## Block Diagram

```
 OBD-II 16-pin ──► Fuse ► TVS ► P-FET ──► 24V rail ─┬─► LMR16010 ► 5 V
   │  (+12 V)        protection                      └─► LMR16010 ► 3.3 V ─► ESP32-S3 / ESP32-C5 / ATECC608B
   │
   └─ CAN-H/CAN-L ─► TVS ─► TJA1051T/3 ─► (CAN-Tx / CAN-Rx) ─► ESP32-S3 ◄─SPI─► ESP32-C5
                                                              │
                                                              ├─► WS2812B ×5
                                                              ├─► Buzzer
                                                              └─► ATECC608B (I²C)
```

## Schematic

Full schematic: [PCB_Schematic.pdf](PCB_Schematic.pdf)

![Schematic](images/schematic.png)

Sections: LED strip · 3.3 V buck · 5 V buck · ATECC608B · TVS diode · CAN transceiver · OBD-2 connector protection · ESP32-S3 · ESP32-C5.

## PCB Layout

Full layout: [PCB_Layout.pdf](PCB_Layout.pdf) (also contains the BOM)

| Top layer | Bottom layer |
|---|---|
| ![Layout top](images/layout_top.png) | ![Layout bottom](images/layout_bottom.png) |

## 3D Views

| Top | Bottom |
|---|---|
| ![3D top](images/3d_top.png) | ![3D bottom](images/3d_bottom.png) |

## Bill of Materials (summary)

Full BOM with LCSC part numbers is in [PCB_Layout.pdf](PCB_Layout.pdf) (pages 3–4).

| Ref | Part | Description |
|---|---|---|
| U1 | ESP32-S3-MINI-1U-N8 | Main MCU |
| U12 | ESP32-C5-MINI-1U | Wireless co-processor |
| U2 | TJA1051T/3 | CAN transceiver |
| U4 | ATECC608B-SSVDA-T | Secure element |
| U5, U6 | LMR16010PDDA | 5 V / 3.3 V buck converters |
| U7–U11 | WS2812B-B-V6 | RGB LEDs |
| U3 | OBD2 male 16-pin | Vehicle connector |
| Q1 | IRF9540NS | Reverse-polarity P-MOSFET |
| Q2 | 2N7002 | Buzzer driver |
| D1 | SM24CANB-02HTG | CAN TVS |
| D2 | SMCJ24 | Supply TVS |
| D3, D4 | SS36 | Schottky catch diodes |
| F1 | 0ZCF0100AF2A | 2 A PTC fuse |
| L3, L4 | 15 µH | Buck inductors |
| BUZZER1 | SMD-9650-3327 | 2.7 kHz buzzer |

## Getting Started

1. Order the PCB using the design files in this repository (import into EasyEDA, or export Gerbers from it) and assemble the parts listed in the BOM.
2. Flash firmware to each ESP32 module over USB (header H2 / H3). Hold **BOOT**, tap **EN**, then release **BOOT** to enter download mode.
3. Plug the dongle into the car's OBD-II port. Fit the H1 jumper to enable the 120 Ω CAN termination only if the dongle is at the end of a bus (normally leave it off on a vehicle port).

> ⚠️ **Safety:** The dongle connects to a vehicle's electrical system. Test on a bench supply first, and only use it on vehicles you own or are authorised to work on.

## Repository Contents

```
.
├── README.md
├── PCB_Schematic.pdf
├── PCB_Layout.pdf
├── 3D PCB_Top.png
├── 3D PCB_Bottom.png
└── images/            # images used in this README
```

## License

Add a license of your choice (e.g. MIT for firmware, CERN-OHL-P for hardware).
