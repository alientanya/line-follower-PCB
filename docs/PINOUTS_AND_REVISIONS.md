# Pin Mapping & Hardware Revisions Guide

This document details the STM32F411 microcontroller pin allocation, hardware peripheral assignments (PWM, ADC, UART, GPIO), and comparison between hardware revisions.

---

## 1. Hardware Revisions Comparison

| Specification | Lavender 1.0 (Blackpill) | Lavender 2.0 (IC Version) |
| :--- | :--- | :--- |
| **Status** | **Field-Tested & Operational** | **Work In Progress (WIP)** |
| **Microcontroller Form** | Plug-in Blackpill Breakout | Onboard Discrete STM32F411 IC |
| **USB Interface** | Onboard Blackpill USB-C Port | External SWD / USB breakout |
| **Crystal Oscillator** | 25MHz HSE on daughterboard | Onboard 25MHz Crystal + Load Caps |
| **Repairability** | Modular daughterboard swap | Component-level rework |
| **Physical Deployment** | Deployed on competition FLF robot | Unfabricated prototype |

---

## 2. STM32F411 Microcontroller Pin Allocation

### Motor Driver (TB6612FNG) Mapping
- **Motor Left PWM (`PWMA`)**: Hardware Timer Output (e.g. `TIM2_CH1` / `PA0`)
- **Motor Right PWM (`PWMB`)**: Hardware Timer Output (e.g. `TIM2_CH2` / `PA1`)
- **Motor Left Direction (`AIN1`, `AIN2`)**: Digital Output GPIOs
- **Motor Right Direction (`BIN1`, `BIN2`)**: Digital Output GPIOs
- **Motor Driver Standby (`STBY`)**: Active-high GPIO Enable

---

### Analog Multiplexer (74HC4067) Mapping
- **Channel Select `S0`**: Digital Output GPIO
- **Channel Select `S1`**: Digital Output GPIO
- **Channel Select `S2`**: Digital Output GPIO
- **Channel Select `S3`**: Digital Output GPIO
- **Multiplexer Enable (`EN`)**: Active-low GND / GPIO
- **Multiplexer Signal (`SIG`)**: STM32 ADC Channel (`ADC1_IN0` / `PA0` or designated ADC pin)

---

### Telemetry & Debug Interface
- **HC-05 Bluetooth TX / RX**: USART1 / USART2 (`TX` / `RX` pins)
- **SWD Debugging**: `SWDIO` (PA13), `SWCLK` (PA14)
- **User Buttons (RC Debounced)**: GPIO Inputs with internal pull-up / pull-down configuration
