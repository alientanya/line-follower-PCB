# FLF Bot Modular PCB

A modular PCB platform for Fast Line Follower (FLF) robots, designed to prioritize sensor experimentation and component flexibility.
This board allows multiple sensor array designs to be used interchangeably without redesigning the core electronics.
Instead of committing to a single sensor layout, this PCB supports four distinct sensor array designs, all compatible with the same base system.

---

## Menu

- [Hardware Revisions](#hardware-revisions)
- [Repository Structure](#repository-structure)
- [Core Features](#core-features)  
- [Core Components & Design Choices](#core-components--design-choices)  
- [Sensors](#sensors)  
- [Interchangeable Sensor Arrays](#interchangeable-sensor-arrays)  
- [8+1 Sensor Placement Strategy](#81-sensor-placement-strategy)  
- [Main Control PCB](#main-control-pcb)  
- [MCU Control](#mcu-control)  
- [Detailed Module Documentation](#detailed-module-documentation)
- [Design Considerations](#design-considerations)  
- [License](#license)

---

## Hardware Revisions

- **Lavender 1.0 (Blackpill version)** — <img alt="Static Badge" src="https://img.shields.io/badge/Status-Field%20Tested%20%26%20Ready-brightgreen">  
  Fully designed, fabricated, assembled, and successfully deployed on the physical FLF robot. Uses the STM32F411 Blackpill module.
- **Lavender 2.0 (IC version)** — <img alt="Static Badge" src="https://img.shields.io/badge/Status-Work%20In%20Progress-orange">  
  Work-in-progress experimental design integrating a discrete STM32F411 IC directly onto the PCB.

---

## Repository Structure

```text
├── Lavender 1.0 (Blackpill vers)/    # Hardware-verified revision used on the physical robot
│   ├── line follower simple.kicad_pcb # Main body PCB layout
│   ├── line follower simple.kicad_sch # Main body schematic
│   ├── QRE1113.kicad_sch              # 16-Sensor QRE1113 schematic
│   ├── TCRT_sensors.kicad_sch         # 9-Sensor TCRT5000 schematic
│   ├── customssensor.kicad_sch        # Custom IR LED + PT334 schematic
│   ├── layerconnect.kicad_sch         # Inter-board connector schematic
│   └── FLF_gerbers/                   # Production Gerber output files
├── Lavender 2.0 (IC vers)/           # WIP prototype with discrete STM32 IC
│   ├── STMf411.kicad_sch              # Discrete STM32F411 MCU schematic
│   └── line follower simple.kicad_pcb # 2.0 PCB layout (WIP)
├── PCB customs/                       # 3D STEP models & custom KiCad footprint libraries
├── docs/                              # Detailed hardware documentation guides
│   ├── MODULES.md                     # Circuit modules & subsystem technical reference
│   ├── SENSOR_ARRAYS.md               # Sensor array designs & 8+1 strategy guide
│   └── PINOUTS_AND_REVISIONS.md       # Pin mapping & revision comparison guide
├── images/                            # PCB renders and schematic diagrams
├── LICENSE                            # MIT License
└── README.md                          # Main project README
```

---

## Core Features

* 4 interchangeable sensor array designs
* Modular architecture using headers
* STM32F411 (Blackpill) based control (Lavender 1.0)
* HC-05 Bluetooth connectivity
* Stackable PCB with internal battery space

---

## Core Components & Design Choices

### Microcontroller — <img alt="Static Badge" src="https://img.shields.io/badge/STM32F411-FF7D9E"> (Blackpill)

* High-speed ADC for sensor reading
* Rich GPIO availability
* Hardware timers for motor PWM
* Plug-in module → easy replacement

---

### Motor Driver — <img alt="Static Badge" src="https://img.shields.io/badge/TB6612FNG-FF78E4">

* Dual DC motor driver
* Efficient and compact
* Suitable for N20 motors

---

### Voltage Regulation — <img alt="Static Badge" src="https://img.shields.io/badge/MP1584-E979FC">

* Buck converter → stable 3.3V
* High efficiency for battery operation
* Adjustable output

---

### Communication — <img alt="Static Badge" src="https://img.shields.io/badge/HC05%20-BB5CF2">

* UART-based Bluetooth module
* Used for debugging and tuning
* Supports STATE and EN pins

---

### Analog Multiplexer — <img alt="Static Badge" src="https://img.shields.io/badge/74HC4067%20-761AD9">

* 16-channel analog MUX
* Used in high-density sensor configuration
* Reduces MCU pin usage

---

## Sensors

#### <img alt="Static Badge" src="https://img.shields.io/badge/QRE1113-57A5FF">

* Compact reflective IR sensor
* Fast response
* Used in high-density and simple arrays

#### <img alt="Static Badge" src="https://img.shields.io/badge/TCRT5000-0C9AC7">

* Larger footprint
* More tolerant of alignment and height

#### <img alt="Static Badge" src="https://img.shields.io/badge/IR%20LED%20%2B%20PT334%206C%20Phototransistor%20-0FA398">

* Discrete design
* Custom Sensors
* Fully tunable sensitivity

---

## Interchangeable Sensor Arrays

All arrays share a common connector interface, making them plug-and-play.

### 1. 16-Sensor Curved Array (QRE1113 + MUX)
<img src="images/16_QRE_PCB.png" width="400">

* High resolution                            
* Uses 74HC4067
* Designed for aggressive curves

### 2. 9-Sensor Straight Array (QRE1113)
<img src="images/9_QRE_PCB.png" width="400">

* Direct MCU input
* Low latency
* Simpler processing

### 3. 9-Sensor Curved Array (TCRT5000)
<img src="images/9_TCRT_PCB.png" width="400">

* Better surface tolerance
* Slight curvature improves tracking

### 4. Custom Sensor Array
<img src="images/9_Custom_PCB.png" width="400">

* IR LED + PT334-6C
* Fully customizable behaviour

---

## 8+1 Sensor Placement Strategy

* 8 main sensors → line tracking
* +1 front sensor → overshoot detection

Improves turning accuracy at high speed.

---

## MCU Control

The STM32F411 (Blackpill) acts as the central controller, interfacing with sensors, motor driver, and Bluetooth module.

* Reads sensor data:

  * Direct ADC (for 9-sensor arrays)
  * Via 74HC4067 MUX (for 16-sensor array)
* Processes line position
* Controls motors using PWM + direction pins (TB6612FNG)
* Handles UART communication with HC-05

All sensor arrays use a consistent interface, so firmware changes are minimal when switching configurations.

<img src="images/MCU_schematic.png" width="400">

---

## Main Control PCB

This section describes the core board that handles control, power distribution, and user interaction.

* Hosts the STM32F411 (Blackpill), TB6612FNG motor driver, and HC-05 module
* Separates logic and motor power to reduce noise interference
* Includes dual switches:
  * Motor Kill
  * Battery Kill

* Provides connectors for all interchangeable sensor arrays
* Routes sensor signals (direct or via MUX) to the MCU
* Includes 2 push buttons:
  * Used for control, mode selection, and calibration
  * Hardware debounced (RC)
* Designed as part of a stacked PCB system:
  * Interfaces with the top board via pin headers
  * Creates space between layers for battery placement

  <img src="images/BodyPCB.png" width="400">

---

## Detailed Module Documentation

Explore the dedicated hardware documentation guides for in-depth schematic breakdowns, pinout mappings, and circuit specifications:

- 🔌 **[Circuit Modules & Subsystems Guide](docs/MODULES.md)** — Technical reference for Power Management & Rail Isolation, MCU & SWD Debugging, TB6612FNG Motor Driver, 74HC4067 MUX, Emitter Biasing, RC Hardware Debouncing, and the Inter-Board Header Bus.
- 🎯 **[Interchangeable Sensor Arrays & Placement Strategy](docs/SENSOR_ARRAYS.md)** — Comprehensive breakdown of the 4 sensor array variants (16-QRE MUX, 9-QRE, 9-TCRT, Custom Discrete) and the 8+1 apex placement geometry.
- 📌 **[Pin Mapping & Hardware Revisions Guide](docs/PINOUTS_AND_REVISIONS.md)** — Hardware peripheral pin assignments (PWM, ADC, MUX select, UART), SWD interface, and comparison between Lavender 1.0 (Field-Tested) and Lavender 2.0 (WIP).

---

## Design Considerations

- Tradeoff between sensor resolution and sampling speed (MUX vs direct ADC)
- Separation of motor and logic power to reduce noise
- Modular sensor interface to allow rapid hardware iteration
- Use of off-the-shelf modules (MCU, driver, regulator) for reliability and easy replacement
- Support for multiple sensor geometries to evaluate performance under different track conditions

---

## Summary

Instead of locking the design into a single sensor configuration, this PCB is built as a flexible platform that encourages rapid experimentation and iteration. Supporting multiple interchangeable sensor arrays, it allows direct comparison between different sensing approaches, geometries, and component choices under the same hardware conditions. This makes it easier to fine-tune performance, optimise control algorithms, and adapt the system for different track types. Ultimately, the goal of this design is not just to work, but to provide a reliable foundation for continuously improving and pushing the limits of FLF performance.

---

## License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.
