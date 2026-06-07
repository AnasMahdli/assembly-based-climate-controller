# Smart Agricultural Environment Automation System 🌱⚙️

A robust, closed-loop environmental control system built natively in **PIC16F877A Assembly** using the modern Microchip XC8 `pic-as` compiler. This system autonomously acquires multi-node analog telemetry to dynamically regulate a physical greenhouse ecosystem via water pumps, ventilation fans, and grow lights.

## 🌟 Key Features

* **Cooperative Multitasking:** Abandons blocking delay loops in favor of a `Timer1` hardware-interrupt scheduler, executing distinct 100ms and 1-second operational sweeps.
* **Non-Volatile Threshold Memory:** Utilizes the internal EEPROM module to save and retrieve precise soil moisture thresholds, ensuring recovery after complete power loss. Includes a factory-blank `0xFF` auto-recovery protocol.
* **Hardware PWM Regulation:** Leverages `Timer2` and the `CCP1`/`CCP2` modules to output continuous, high-speed (244 Hz) background PWM signals for variable fan speeds and LED dimming.
* **Live Telemetry Dashboard:** Custom 4-bit driver for HD44780 16x2 LCDs, utilizing BCD conversion arithmetic to display real-time sensor metrics and system states.
* **Industrial Safety Interlocks:**
  * **Dry-Run Alarm:** A background 10-second timer automatically cuts power to the pump if the sensor fails to detect water delivery, preventing motor coil burnout.
  * **Hardware E-STOP:** Instantaneous manual override polling that locks all PWM registers to `0x00` and drops heavy-load relays.

## 🛠️ Hardware Architecture

### Microcontroller
* **MCU:** Microchip PIC16F877A
* **Clock Speed:** 4.00 MHz (External Quartz Crystal)
* **Power:** 5V DC Regulated

### Pin Mapping Directory

| Module / Component | PIC16 Pin | Description |
| :--- | :--- | :--- |
| **Moisture Sensor** | `RA0 (AN0)` | Analog input for soil hydration levels. |
| **Humidity Sensor** | `RA1 (AN1)` | Analog input for ambient humidity. |
| **Temperature Sensor**| `RA2 (AN2)` | Analog input (e.g., LM35) for thermal tracking. |
| **LDR Sensor** | `RA3 (AN3)` | Analog input for Day/Night cycle detection. |
| **E-STOP Button** | `RB7` | Active-low digital input for emergency system halt. |
| **Water Pump Relay** | `RC0` | Digital output driving the irrigation NPN transistor. |
| **Ventilation Fan** | `RC1 (CCP2)`| Hardware PWM output for thermal regulation. |
| **LED Grow Lights** | `RC2 (CCP1)`| Hardware PWM output for hysteresis illumination. |
| **LCD Data Bus** | `RD0 - RD3` | 4-bit parallel data transmission lanes. |
| **LCD RS & EN** | `RD4 & RD5` | Register Select and Enable strobe pins. |

## 💻 Software & Toolchain

This project is written using the modern **Microchip `pic-as` toolchain**, completely replacing the deprecated MPASM assembler. 

* **IDE:** MPLAB X IDE (v5.40 or newer recommended)
* **Compiler:** XC8 Toolchain (v2.30+ featuring `pic-as`)
* **Syntax Highlights:** Utilizes strict case-sensitive macros (`BANKSEL`), `PSECT` memory allocations instead of `CBLOCK`, and modern Intel HEX linking.

## 🚀 Getting Started

### 1. Compilation & Build
1. Clone this repository to your local machine.
2. Open **MPLAB X IDE** and create a new Standalone Project selecting the `PIC16F877A`.
3. Select the **XC8 (pic-as)** compiler in your toolchain settings.
4. Add `main.s` to your Source Files directory.
5. Click **Clean and Build** to generate the `.hex` file.

### 2. Flashing the Hardware
Connect your hardware programmer (e.g., PICkit 3 or PICkit 4) to the ICSP header on your board. Target the generated `.hex` file located in `dist/default/production/` and program the device.

### 3. Proteus Simulation Note
If you are testing this code in Proteus ISIS, ensure the virtual PIC16F877A EEPROM is not left physically blank. The assembly bootloader expects a natural hardware `0xFF` state for first-time setup. You can attach a generated `.eep` file to the microcontroller properties inside Proteus to simulate a factory reset.

## 📁 Repository Structure
* `/src` - Contains the main assembly source code (`main.s`).
* `/schematics` - Contains the Proteus simulation files and exported PDF wiring diagrams.
* `/docs` - Contains the full technical engineering report and logic flowcharts.

## 🤝 Contributing
Contributions, issues, and feature requests are welcome. If you are modifying the interrupt service routine, ensure all temporary registers are strictly placed in the `class=COMMON` shared memory space (`0x70 - 0x7F`) to prevent bank-switching context corruption.

## 📄 License
This project is open-source and available under the [MIT License](LICENSE).
