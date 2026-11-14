# ⚡ AC Fault Guard
### Single-Phase AC Supply Protection and Fault Detection System

![Arduino](https://img.shields.io/badge/Arduino-Nano-00979D?logo=arduino&logoColor=white)
![Platform](https://img.shields.io/badge/Platform-Embedded%20Systems-blue)
![Supply](https://img.shields.io/badge/Prototype-12V%20AC-orange)
![Status](https://img.shields.io/badge/Status-In%20Development-yellow)

An Arduino Nano-based **single-phase AC protection prototype** designed to monitor electrical parameters, identify abnormal operating conditions, and automatically disconnect a low-voltage AC load.

The system focuses on three protection functions:

- **Undervoltage protection**
- **Overcurrent protection**
- **Earth leakage / residual-current detection**

It combines electrical sensing, embedded control, relay-based disconnection, LCD fault indication, and manual reset functionality.

> **Project status:** Hardware design and implementation are in progress. The architecture, parameters, and tests below describe the intended design. Measured results and verified functions will be added after physical testing.

---

## 📑 Table of Contents

1. [Project Overview](#-project-overview)
2. [Problem Statement](#-problem-statement)
3. [Objectives](#-objectives)
4. [Key Features](#-key-features)
5. [System Architecture](#-system-architecture)
6. [Hardware Components](#-hardware-components)
7. [Working Principle](#-working-principle)
8. [Fault Detection Methods](#-fault-detection-methods)
9. [Arduino Pin Configuration](#-arduino-pin-configuration)
10. [Protection Algorithm](#-protection-algorithm)
11. [Hardware Implementation Plan](#-hardware-implementation-plan)
12. [Testing and Validation](#-testing-and-validation)
13. [Protection Device Comparison](#-protection-device-comparison)
14. [Electrical Safety](#-electrical-safety)
15. [Applications](#-applications)
16. [Future Improvements](#-future-improvements)
17. [Learning Outcomes](#-learning-outcomes)
18. [Project Status](#-project-status)

---

## 🔍 Project Overview

Electrical power systems require reliable protective devices to detect abnormal operating conditions and disconnect affected circuits before equipment damage or unsafe conditions occur.

**AC Fault Guard** is an educational hardware project that demonstrates selected single-phase electrical protection principles using a 12V AC isolated supply and Arduino Nano.

The prototype is designed to continuously measure:
- AC supply voltage
- AC load current
- Residual current caused by imbalance between outgoing and returning current

When a configured fault threshold is exceeded for the required duration, the Arduino generates a trip command to a suitably rated switching relay.

A 16×2 LCD displays system measurements and fault status, while a buzzer and LED provide local alerts. A manual reset mechanism prevents automatic reconnection immediately after a trip.

**Important:** This is a low-voltage educational protection demonstrator, not a certified protective relay, RCCB, RCBO, or short-circuit protection device.

---

## ❗ Problem Statement

Single-phase electrical systems may experience:

- **Undervoltage:** Supply voltage decreases below a permissible operating limit.
- **Overcurrent:** Excessive load current increases the risk of conductor heating and equipment damage.
- **Short circuit:** A low-impedance fault can produce very high current requiring rapid hardware protection.
- **Earth leakage:** Some current returns through an unintended path rather than through the normal return conductor.

These conditions require suitable detection and disconnection methods.

This project investigates how voltage sensing, current measurement, residual-current sensing, and microcontroller-based decision logic can be combined to demonstrate electrical protection principles.

---

## 🎯 Objectives

1. Develop a low-voltage, single-phase AC protection prototype.
2. Measure AC supply voltage and load current.
3. Detect undervoltage using a configurable RMS voltage threshold.
4. Detect sustained overcurrent using a configurable RMS current threshold.
5. Detect residual current using a suitable core-balance current transformer (CBCT) and conditioning circuit.
6. Disconnect the demonstration load through a properly rated relay.
7. Indicate fault conditions using an LCD, LED, and buzzer.
8. Include a manual reset function after fault clearance.
9. Record measured values, trip thresholds, and operating times.
10. Demonstrate practical concepts related to electrical protection and power distribution.

---

## ✨ Key Features

| Feature | Description |
|---|---|
| AC Voltage Monitoring | Measures supply voltage through a conditioned voltage sensor |
| AC Current Monitoring | Measures load current using a suitable current sensor |
| Undervoltage Protection | Trips when voltage remains below the configured threshold |
| Overcurrent Protection | Trips when load current exceeds the configured limit |
| Earth Leakage Detection | Uses CBCT-based residual-current measurement |
| Automatic Load Disconnection | Sends a trip command to a switching relay |
| LCD Monitoring | Displays voltage, current, status, and fault information |
| Audible Fault Alert | Activates a buzzer when a fault occurs |
| Fault Indication | Displays the identified protection event |
| Manual Reset | Requires operator reset after the fault is cleared |

---

## 🏗 System Architecture

### Block Diagram

```text
            CERTIFIED 230V AC / 12V AC
              ISOLATED POWER SUPPLY
                         |
                         v
                12V AC SECONDARY
                         |
                 Fuse / Protection
                         |
              +----------+----------+
              |                     |
       Voltage Sensing       AC Power Circuit
              |                     |
              |             Relay Switching
              |                     |
              |             Current Sensor
              |                     |
              |                 AC Load
              |                     |
              |             Secondary Return
              |                     |
              +----------+----------+
                         |
       CBCT around outgoing and returning
       conductors (residual-current sensing)

        CONDITIONED SENSOR SIGNALS
                         |
                         v
                   ARDUINO NANO
                         |
             Fault Detection Logic
                         |
         +---------------+---------------+
         |               |               |
         v               v               v
      Relay Trip       LCD Display    Buzzer / LED
```

**Architecture notes:**
- The voltage sensor is connected through a suitable measurement interface across the isolated AC secondary.
- The current sensor measures current in the load branch.
- Both outgoing and returning AC conductors pass through the same CBCT in the correct arrangement.
- The protective earth conductor, if present, must not pass through the CBCT.
- The Arduino is powered by a separate regulated 5V DC supply.
- Appropriate isolation, signal conditioning, fuses, and relay ratings are required.

---

## 🔧 Hardware Components

| S.No. | Component | Quantity | Purpose |
|---|---|---:|---|
| 1 | Arduino Nano | 1 | Main processing and control unit |
| 2 | 230V to 12V AC isolation transformer supply | 1 | Isolated low-voltage test supply |
| 3 | AC voltage sensing circuit | 1 | Measures secondary voltage |
| 4 | ACS712 or suitable AC current sensor | 1 | Measures load current |
| 5 | Core Balance CT (CBCT) | 1 | Residual-current measurement |
| 6 | CBCT signal conditioning circuit | 1 | Converts sensor output to a safe readable signal |
| 7 | 5V relay module with suitable AC-rated contacts | 1 | Load switching |
| 8 | 16×2 LCD with I2C interface | 1 | Monitoring and fault indication |
| 9 | Buzzer | 1 | Audible warning |
| 10 | Indicator LEDs and resistors | Set | Visual status indication |
| 11 | Regulated 5V DC supply | 1 | Arduino and control-side power |
| 12 | 12V AC-compatible test loads | As required | Demonstration loads |
| 13 | Fuses, wiring, connectors, enclosure | Set | Physical assembly and independent protection |
| 14 | Manual reset push button | 1 | Fault reset control |

### Component Selection Notes

**Arduino Nano**
- ATmega328P-based microcontroller.
- Provides analog inputs and digital control outputs.
- Used for sensor sampling, protection logic, and user interface.

**ACS712 Current Sensor**
- Hall-effect current measurement device.
- Can measure AC current with appropriate RMS processing.
- Sensor range and resolution must be selected according to the actual load current.

**CBCT**
- Measures the vector sum of currents passing through its magnetic core.
- Requires a correctly designed burden and signal-conditioning interface.
- Must be sensitive enough for the intended residual-current demonstration.

**Relay**
- Used as a low-voltage AC load switching element.
- Contacts must be rated for the applicable AC voltage, current, and load characteristics.
- The relay does not replace independent overcurrent protection.

---

## ⚙️ Working Principle

The system operates through continuous measurement, threshold evaluation, and fault-triggered disconnection.

### Step 1: AC Supply

A certified isolated transformer supply reduces the mains voltage to approximately 12V AC.

The isolated secondary supplies the demonstration load circuit.

### Step 2: Voltage Monitoring

The voltage measurement circuit provides a conditioned signal representing the AC secondary voltage.

The Arduino processes samples to estimate RMS voltage.

### Step 3: Current Monitoring

The current sensor measures current through the connected load.

The Arduino calculates current magnitude and compares it with the configured overcurrent threshold.

### Step 4: Residual Current Monitoring

A CBCT measures the imbalance between outgoing and returning AC currents.

A suitable detector or signal-conditioning stage converts the CBCT output into a form that the Arduino can safely evaluate.

### Step 5: Fault Decision

The controller checks voltage, current, and residual-current conditions against selected thresholds and timing requirements.

### Step 6: Relay Tripping

When a confirmed fault is detected, the Arduino sends a trip command to open the load-switching relay.

Independent hardware protection remains necessary for high-current faults.

### Step 7: Fault Indication

The LCD indicates the fault category. A buzzer and LED provide alerts.

### Step 8: Manual Reset

The output remains disconnected until the detected fault has cleared and an operator performs a manual reset.

---

## 🚨 Fault Detection Methods

### 1. Undervoltage Protection

**Purpose:** Detect supply voltage below the configured operating limit.

The system calculates RMS voltage:

\[
V_{RMS} = \sqrt{\frac{1}{N}\sum_{k=1}^{N}v_k^2}
\]

Here, `v_k` represents calibrated instantaneous AC voltage samples.

**Trip condition:**

```text
Measured RMS Voltage < Undervoltage Threshold
                AND
Condition persists longer than trip delay
                |
                v
          UNDERVOLTAGE TRIP
```

For a nominal 12V AC test supply, an illustrative undervoltage setting is 10V AC.

This is a demonstration setting, not a universal protection standard.

### 2. Overcurrent Protection

**Purpose:** Detect excessive current caused by an overloaded circuit.

The current sensor provides measurements used to calculate RMS load current:

\[
I_{RMS} = \sqrt{\frac{1}{N}\sum_{k=1}^{N}i_k^2}
\]

**Trip condition:**

```text
Measured RMS Current > Overcurrent Pickup
                 AND
Condition persists longer than trip delay
                 |
                 v
           OVERCURRENT TRIP
```

The threshold and operating delay should be selected according to the actual test load and wiring ratings.

**Short-circuit limitation:** A microcontroller, general-purpose sensor, and hobby switching relay cannot be relied upon to safely interrupt high prospective short-circuit currents. The prototype must have independent fuse/current-limiting protection. Controlled overload tests are used instead of deliberately creating short circuits.

### 3. Earth Leakage / Residual-Current Detection

**Purpose:** Detect an imbalance between current leaving the supply and current returning through the intended circuit.

Under normal conditions:

\[
I_L \approx I_N
\]

The residual-current magnitude is:

\[
I_{residual} = |\vec{I}_L + \vec{I}_N|
\]

The phasor sum accounts for the opposite directions of outgoing and returning currents through the CBCT.

For currents expressed as same-reference magnitudes in the simple single-phase case:

\[
I_{residual} \approx |I_L - I_N|
\]

Under normal operation:

```text
Outgoing current ≈ Returning current
                  |
                  v
         Residual current ≈ 0
```

During a residual-current event:

```text
Outgoing current != Returning current
                  |
                  v
             CBCT Output
                  |
                  v
       Residual-current detection
                  |
                  v
             Relay Trip
```

**Important:** A floating 12V AC transformer secondary does not automatically produce a realistic earth-leakage path. Residual-current testing should use a properly designed test winding or isolated current-injection method, not an improvised earth fault.

---

## 🔌 Arduino Pin Configuration

| Component | Arduino Nano Pin |
|---|---|
| Conditioned AC voltage sensor | A0 |
| Conditioned current sensor | A1 |
| Residual-current detector digital output | D2 |
| Relay control input | D8 |
| Buzzer | D9 |
| Fault LED | D10 |
| LCD SDA | A4 |
| LCD SCL | A5 |
| Manual reset button | D3 |

These are proposed pin assignments.

If the CBCT conditioning circuit provides an analog signal instead of a digital threshold output, a suitable analog input such as A2 may be used.

**All Arduino input signals must remain within the controller's specified input voltage range. Never connect unconditioned AC directly to an Arduino pin.**

---

## 🧠 Protection Algorithm

### Operating Sequence

```text
                    START
                      |
                      v
            Initialize Arduino
                      |
                      v
          Initialize LCD and Sensors
                      |
                      v
             Set Protection Limits
                      |
                      v
            Read Sensor Measurements
                      |
                      v
          Calculate RMS V and RMS I
                      |
                      v
          Read Residual-Current Status
                      |
                      v
              Check Protection Logic
                      |
          +-----------+-----------+
          |                       |
          v                       v
     Fault Detected            No Fault
          |                       |
          v                       v
    Confirm Fault Delay       Load Remains ON
          |                       |
          v                       |
       Trip Relay                  |
          |                       |
          v                       |
     Display Fault                 |
          |                       |
          v                       |
     Activate Alarm                |
          |                       |
          v                       |
     Wait for Fault Clear          |
          |                       |
          v                       |
       Manual Reset                |
          |                       |
          +-----------+-----------+
                      |
                      v
               Continue Monitoring
```

### Pseudocode

```cpp
initialize_sensors();
initialize_display();
initialize_relay();
initialize_alarm();

while (true) {
    float voltage = read_voltage_rms();
    float current = read_current_rms();
    bool leakage = read_residual_current_alarm();

    bool under_voltage = check_undervoltage(voltage);
    bool over_current = check_overcurrent(current);

    if (!trip_latched) {
        if (fault_confirmed(under_voltage, over_current, leakage)) {
            trip_latched = true;
            disconnect_load();
            activate_alarm();
            display_fault();
        }
    }

    if (trip_latched && reset_requested() && fault_cleared()) {
        trip_latched = false;
        deactivate_alarm();
        reconnect_load();
    }

    update_lcd(voltage, current, trip_latched);
}
```

> This is illustrative pseudocode, not production-ready Arduino firmware. Actual implementation requires sensor calibration, timing logic, safe startup behavior, fault latching, and appropriate relay control.

---

## 🛠 Hardware Implementation Plan

### Phase 1: Isolated Power Supply

- Select a certified, enclosed 12V AC isolated supply.
- Verify its rated voltage, current, and VA capacity.
- Add suitable secondary-side protection.
- Select appropriate 12V AC test loads.

### Phase 2: Voltage Measurement

- Design or select a suitable AC measurement interface.
- Verify that the output is safe for the Arduino.
- Calibrate measurements against a suitable reference meter.
- Test undervoltage detection using controlled isolated supply adjustment or safe sensor simulation.

### Phase 3: Current Measurement

- Integrate the current sensor in the load circuit.
- Measure current under normal conditions.
- Introduce controlled load changes.
- Verify overcurrent pickup and trip delay.

### Phase 4: Residual Current Measurement

- Select a suitable CBCT.
- Design or obtain compatible signal conditioning.
- Route outgoing and returning conductors correctly.
- Use an appropriate test winding or isolated injection method.
- Validate the residual-current pickup threshold.

### Phase 5: Control and Indication

- Integrate the Arduino Nano.
- Connect relay control and fault indicators.
- Configure LCD measurement display.
- Implement fault latching and manual reset.

### Phase 6: System Testing

- Test normal operation.
- Test undervoltage.
- Test controlled overcurrent.
- Test residual-current injection.
- Verify trip indication and reset behavior.
- Record results and response times.

---

## 🧪 Testing and Validation

### Planned Functional Tests

| Test | Input Condition | Expected Behavior |
|---|---|---|
| Normal Operation | Voltage and current within limits | Relay remains closed |
| Undervoltage | RMS voltage below threshold for defined delay | Undervoltage trip |
| Overcurrent | Load current exceeds pickup for defined delay | Overcurrent trip |
| Residual Current | Calibrated test injection exceeds threshold | Earth leakage trip |
| Fault Indication | Confirmed fault | LCD and buzzer indicate event |
| Manual Reset | Fault cleared and reset pressed | Load reconnects if safe |
| Fault Persistence | Reset attempted while fault persists | Trip remains latched |

### Experimental Results

| Test | Measured Value | Configured Limit | Trip Time | Result |
|---|---|---|---|---|
| Normal Supply | Pending | Pending | — | Not tested |
| Undervoltage | Pending | Pending | Pending | Not tested |
| Overcurrent | Pending | Pending | Pending | Not tested |
| Residual Current | Pending | Pending | Pending | Not tested |
| Manual Reset | Pending | — | — | Not tested |

Measured values will be added after calibration and hardware testing.

### Suggested Evidence

Include the following after implementation:

- Full circuit schematic
- Hardware prototype photographs
- Serial monitor output
- Normal-operation LCD display
- Undervoltage fault indication
- Overcurrent fault indication
- Residual-current test output
- Trip response time measurements

---

## 🛡 Protection Device Comparison

| Device | Overload | Short Circuit | Residual Current |
|---|---|---|---|
| MCB | Yes | Yes | No |
| RCCB | No | No | Yes |
| RCBO | Yes | Yes | Yes |
| Proposed Educational Prototype | Detects configured overload conditions | Independent hardware protection required | Detects configured residual-current conditions |

**MCB (Miniature Circuit Breaker):** Provides overcurrent protection against overload and short-circuit conditions.

**RCCB (Residual Current Circuit Breaker):** Detects residual-current imbalance but does not independently protect against overload or short circuit.

**RCBO (Residual Current Circuit Breaker with Overcurrent Protection):** Combines overcurrent and residual-current protection.

The project is intended to demonstrate selected measurement and trip principles, not replace any certified protective device.

---

## ⚠️ Electrical Safety

1. Use a certified, enclosed, isolated 12V AC supply.
2. Never connect 230V AC directly to the Arduino or exposed breadboard wiring.
3. Keep mains-side wiring enclosed and handled by a qualified person.
4. Use properly rated fuses or current-limiting protection.
5. Use relay contacts rated for the applicable AC circuit and load.
6. Never use a general-purpose Arduino relay as the sole protective device.
7. Do not deliberately short-circuit the transformer secondary.
8. Use controlled overload demonstrations instead of uncontrolled fault currents.
9. Never create a real earth fault to test leakage detection.
10. Use suitable signal conditioning for voltage, current, and CBCT sensors.
11. Keep the control supply and AC measurement interfaces appropriately isolated.
12. Test the prototype under qualified supervision.

**This project is not intended for installation as a protective device in a household or industrial electrical distribution system.**

---

## 🏭 Applications

The concepts demonstrated in this project are relevant to:

- Electrical power system protection education
- Single-phase supply monitoring
- Industrial electrical monitoring demonstrations
- Protective relay principles
- Residual-current measurement research
- Embedded electrical measurement systems
- Low-voltage fault detection experiments

---

## 🚀 Future Improvements

- [ ] Add ESP32-based Wi-Fi monitoring
- [ ] Develop a web-based voltage and current dashboard
- [ ] Store historical fault events
- [ ] Add trip-time measurement and reporting
- [ ] Implement adjustable protection pickup thresholds
- [ ] Include configurable time-delay characteristics
- [ ] Add overvoltage protection
- [ ] Add temperature monitoring
- [ ] Extend protection to multiple distribution feeders
- [ ] Study relay coordination and selective isolation
- [ ] Develop MATLAB/Simulink models for single-phase protection analysis
- [ ] Compare measured hardware behavior against simulation results

---

## 📚 Learning Outcomes

Through this project, the intended learning outcomes include:

**Electrical Engineering**
- AC voltage and current measurement
- Power system protection concepts
- RMS measurement
- Overcurrent and undervoltage protection
- Earth leakage and residual-current principles
- Core-balance current transformer operation
- Relay pickup settings and trip delays
- Electrical isolation and low-voltage testing

**Embedded Systems**
- Arduino Nano programming
- Analog signal acquisition
- Sensor calibration
- Digital output control
- Relay interfacing
- LCD and buzzer integration
- Fault indication and reset logic

**System Engineering**
- Hardware and software integration
- Component selection
- Functional testing
- Protection threshold validation
- Trip response measurement
- Technical documentation

---

## 📌 Project Status

**Current Stage:** Design and Development

| Module | Status |
|---|---|
| System Architecture | Planned |
| Component Selection | In Progress |
| Isolated AC Supply Assembly | Pending |
| Voltage Monitoring | Pending |
| Current Monitoring | Pending |
| Residual-Current Monitoring | Pending |
| Relay Trip Logic | Pending |
| LCD / Buzzer Integration | Pending |
| Integrated Testing | Pending |
| Final Hardware Documentation | Pending |

This table will be updated as modules are completed and verified.

---

## 📄 Project Summary

**Project:** Single-Phase AC Supply Protection and Fault Detection System

**Controller:** Arduino Nano

**Prototype Supply:** Isolated 12V AC

**Monitoring:** Voltage, Load Current, Residual Current

**Protection Functions:** Undervoltage, Overcurrent, Earth Leakage

**Output:** Relay-Based Load Disconnection

**User Interface:** 16×2 LCD, Buzzer, LEDs

**Reset:** Manual Fault Reset

**Domain:** Electrical Power Systems, Protection, Embedded Systems

---

### ⭐ Support

If you find this educational project useful, consider starring the repository.

**Built to explore electrical fault detection, relay operation, and practical single-phase protection principles.**
