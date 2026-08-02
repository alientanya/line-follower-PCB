# Interchangeable Sensor Arrays & Placement Strategy

The **FLF Bot Modular PCB** platform features a standardized, interchangeable sensor array interface. By using common inter-board header pinouts (`layerconnect.kicad_sch`), the robot can switch between different optical sensing technologies and array geometries without modifying the main control board or MCU wiring.

---

## 1. Sensor Array Variants

### Array 1: 16-Sensor Curved Array (QRE1113 + 74HC4067 MUX)
- **Primary Component**: 16x Fairchild/ON Semi QRE1113 miniature reflective phototransistors.
- **Multiplexing**: 74HC4067 16-channel analog MUX converts 16 analog signals down to 1 multiplexed output line (`SIG`).
- **Geometry**: Parabolic curved arc.
- **Key Advantage**: Maximum spatial resolution across aggressive curves and acute turns.
- **Sampling Scheme**: Sequential channel switching via digital control lines `S0`, `S1`, `S2`, `S3`.

---

### Array 2: 9-Sensor Straight Array (QRE1113)
- **Primary Component**: 9x QRE1113 reflective IR sensors.
- **Multiplexing**: Direct ADC sampling (no MUX latency).
- **Geometry**: Straight linear row.
- **Key Advantage**: Zero multiplexing delay and ultra-fast sampling throughput. Ideal for high-speed straight-line acceleration.

---

### Array 3: 9-Sensor Curved Array (TCRT5000)
- **Primary Component**: 9x Vishay TCRT5000 reflective optical sensors.
- **Multiplexing**: Direct ADC sampling.
- **Geometry**: Slightly curved arc.
- **Key Advantage**: Larger optical footprint with higher height tolerance ($0.2\text{mm} - 15\text{mm}$). Ideal for irregular or textured track surfaces.

---

### Array 4: Custom Discrete Sensor Array (IR LED + PT334-6C)
- **Primary Component**: High-output 940nm IR Emitters + PT334-6C Phototransistors.
- **Features**: Individual current-limiting resistors and tunable load resistor networks.
- **Key Advantage**: Complete manual control over optical wavelength, emitter power, and sensor sensitivity.

---

## 2. The 8+1 Sensor Placement Strategy

```text
               [ Front Overshoot Sensor (+1) ]
                            │
               ┌────────────┴────────────┐
               │    Front Apex Detection │
               └─────────────────────────┘
                            ▲
                            │ (Braking & Turn Entry Trigger)
                            │
   [S1]  [S2]  [S3]  [S4]       [S5]  [S6]  [S7]  [S8]
    └─────────────────┬───────────────────┘
               8 Main Tracking Arc
            (Continuous PID Line Position)
```

### Functional Breakdown
1. **8 Main Line Tracking Sensors**:
   - Form an arc positioned at the optimal turning pivot distance.
   - Outputs are processed through a weighted average formula to calculate continuous line error ($e(t)$) for PID motor control:
     $$\text{Position} = \frac{\sum_{i=1}^{8} w_i \cdot S_i}{\sum_{i=1}^{8} S_i}$$
2. **+1 Front Overshoot Sensor**:
   - Positioned further ahead at the front apex.
   - Detects upcoming 90° corners, T-junctions, cross-tracks, or sudden line loss before the main array reaches the turn.
   - Triggers pre-programmed corner-entry deceleration or aggressive yaw rotation algorithms.
