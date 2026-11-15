# OpenVCU BMS Hardware (`openvcu-bms`)

This repository contains the KiCad 10 schematics, PCB layouts, custom footprint/symbol libraries, Bill of Materials (BOM), and 3D CAD models for the **OpenVCU High-Voltage Battery Management System (BMS) Master Controller**.

## Ecosystem & Related Repositories

* **[OpenVCU Core (`OpenVCU/openvcu`)](https://github.com/OpenVCU/openvcu):** Core Vehicle Control Unit (VCU) firmware and system code.
* **[OpenVCU BMS Hardware (`OpenVCU/openvcu-bms`)](https://github.com/OpenVCU/openvcu-bms):** BMS Master hardware source files, KiCad 10 project, BOMs, and CAD models (this repository).
* **[OpenVCU BMS Firmware (`OpenVCU/openvcu-bms-firmware`)](https://github.com/OpenVCU/openvcu-bms-firmware):** Embedded C firmware (`STM32G474CCTx`) for the BMS Master controller, supporting the `INA228` power monitor and unified `LTC681x` / `ADBMS1818` isoSPI cell monitors.

---

## Hardware Specifications & Key Features

Designed for high-voltage electric vehicle battery packs (400V–800V class) with an emphasis on component availability, automated SMD PCBA manufacturing, reinforced galvanic isolation, and reliable operation down to **-30°C**:

* **Microcontroller (`MCU.kicad_sch`):** STMicroelectronics **STM32G474CCTx** (LQFP-48, 170 MHz Cortex-M4F, 256 KB Flash).
* **Dual isoSPI Daisy-Chain Master (`MCU.kicad_sch`):**
  * Dual **LTC6820** (`U2, U11`) reversible isoSPI transceivers coupled through Pulse **HM2108NL** (`TR2, TR4`) isolation transformers to `J19` (`ISOSPI1`) and `J20` (`ISOSPI2`).
  * Supports redundant reversible ring topologies with **LTC6810**, **LTC6804**, **LTC6811**, **LTC6812**, **LTC6813**, and **ADBMS1818** slave modules.
* **High-Voltage Power & Current Monitoring (`PowerMonitor.kicad_sch`):**
  * **TI INA228AIDGSR** (`U16`) 20-bit I2C current/bus monitor with 40-bit hardware Coulomb accumulation across a **100 µΩ** Vishay `CSM2F-8518-L100J01` (`R29`) precision shunt.
  * Triple **TI AMC1301** (`U1, U12, U13`) reinforced isolated amplifiers paired with **OPA2197IDR** (`U15`) operational amplifiers for isolated pack and contactor voltage sensing.
* **Contactor, Precharge & Safety Interfaces (`Interfaces.kicad_sch`):**
  * 5x Nexperia **BUK9Y40-55B,115** (`LFPAK56`) automotive MOSFETs (`Q7, Q8, Q13, Q14, Q15`) driving Positive/Negative HV Contactors (`J1, J4`), Discharge/Charge loops (`J8, J9, J10`), and Cooling (`J2`).
  * Integrated high-voltage precharge circuit using a Standex-Meder **SHV12-1A85-78L3K** (`K1`) reed relay and 4x **135 Ω** wirewound power resistors (`R34, R44, R52, R53`).
  * Opto-isolated Charger/E-Stop sensing via **SFH617A-2X009T** (`U5, U9`) and **DMN6075S-7** small-signal MOSFETs.
* **Isolated CAN Bus (`CAN.kicad_sch`):**
  * **TI ISO1042DWVR** (`U8`) isolated CAN/CAN-FD transceiver powered via an **SN6501DBV** (`U4`) push-pull transformer driver (`TR1`) and **SZNUP2105L** (`D4`) bus ESD protection.
* **USB-C & Auto-Bootloader Interface (`USB.kicad_sch`):**
  * 16-pin **USB Type-C** receptacle (`J12`) with **SRV05-4** (`U17`) ESD protection and **CP2102C / CP2102N** (`U7`) USB-to-UART bridge, including hardware `DTR`/`RTS` auto-reset (`NRST`/`BOOT0`) circuitry.
* **Power Supply Architecture (`PowerSupply.kicad_sch`):**
  * Primary step-down stage built around the **TI LM5165YDRC** (`U19`) wide-input synchronous buck converter, plus isolated 5V rails (`SN6501DBV` + `WURTH-760390014` transformers).

---

## Repository & KiCad Project Structure

Requires **KiCad 10.0+** to open and edit the schematic and board files:

| File / Directory | Description |
| :--- | :--- |
| `BMS-Master.kicad_pro` | Main KiCad 10 project file |
| `BMS-Master.kicad_sch` | Top-level hierarchical root schematic |
| `Interfaces.kicad_sch` | High-current contactor drivers, HV precharge relay, and isolated inputs |
| `PowerMonitor.kicad_sch` | Shunt sensing (`INA228`), HV divider networks, and `AMC1301` isolated amplifiers |
| `PowerSupply.kicad_sch` | 12V input protection, `LM5165` buck converter, and power rails |
| `MCU.kicad_sch` | `STM32G474CCTx` MCU, dual `LTC6820` isoSPI masters, crystal, and headers |
| `CAN.kicad_sch` | Isolated CAN bus (`ISO1042DWVR`) and isolated `SN6501` power supply |
| `USB.kicad_sch` | USB-C receptacle, ESD array, and `CP2102` UART/bootloader bridge |
| `OpenVCU.kicad_sym` | Custom project schematic symbol library |
| `OpenVCU.pretty/` | Custom project PCB footprint library (`.kicad_mod`) |
| `CAD/` | 3D STEP (`.stp`) mechanical models for PCB assembly verification |
| `BMS-Master.csv` | Master Bill of Materials (BOM) |

---

## Credits & Lineage

Originally derived from the open-source DieBieMS / ENNOID-BMS hardware architecture and redesigned for the **OpenVCU** platform.
