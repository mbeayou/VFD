# DIY 3-Phase Variable Frequency Drive (VFD)

## Project Overview
This project involves the design and construction of a Variable Frequency Drive (VFD) to control the speed and torque of a 3-phase AC induction motor. The system utilizes an **ESP32 microcontroller** to generate Space Vector Pulse Width Modulation (SVPWM) signals. These signals are isolated and amplified to switch a High-Voltage IGBT bridge, converting rectified DC back into variable frequency AC.

Key features include:
* **High Voltage DC Bus** derived from standard AC mains.
* **Galvanic Isolation** protecting the logic circuitry from high voltage.
* **Bootstrap Gate Driving** for efficient high-side switching.
* **Manual Speed Control** via a potentiometer.

---

## Schematic Analysis

### 1. High Voltage DC Link
The power input stage accepts AC mains voltage. [cite_start]It flows through a **10A Fuse (F1)** for overcurrent protection and an **NTC Thermistor (TH1)** to limit the initial inrush current when the capacitors charge[cite: 1, 3, 4].

The AC is rectified by a **GBJ2510 Bridge Rectifier**, converting it to pulsating DC. This is smoothed by two parallel **470µF Electrolytic Capacitors (C1, C2)** to create a stable high-voltage DC Rail. A **100kΩ Resistor (R1)** acts as a bleeder to safely discharge the capacitors when power is removed.

![Schematic Diagram: Power Input and Rectification Stage](Schematic_Power_Input_and_Rectification_Stage.png)

### 2. Control Logic (ESP32)
The **ESP32-DEVKITC-32D** is the central controller. It reads the target speed from a **10kΩ Potentiometer (RV1)** connected to the `SENSOR_VP` pin.

The ESP32 outputs six control signals (HIN and LIN for phases U, V, and W) to drive the motor.

![Schematic Diagram: ESP32 Control Module](Schematic_ESP32_Control_Module.png)

### 3. Signal Isolation (TLP350) and Gate Drivers (IR2110)
To ensure safety and noise immunity, the 3.3V logic signals from the ESP32 do not drive the power stage directly. They pass through **TLP350 Optocouplers**. These components use light to transmit the signal across an isolation barrier, protecting the microcontroller from the high-voltage DC rail.

The isolated signals are then fed into three **IR2110 Gate Driver ICs (U1, U4, U9)**. These chips provide the necessary current and voltage (15V) to switch the IGBTs rapidly.

The drivers utilize a **Bootstrap Circuit** consisting of **UF4007 Diodes (D2, D3, D4)** and **10µF Capacitors (C3, C6, C8)** to generate the floating voltage required to drive the High-Side IGBTs.

![Schematic Diagram: Gate Driver](Schematic_Gate_Driver.png)

### 5. Inverter Output Bridge
The power output stage is a 3-phase bridge comprised of six **IRG4PF50W IGBTs**.
* **Phase U:** Q1 (High) and Q2 (Low).
* **Phase W:** Q3 (High) and Q4 (Low).
* **Phase V:** Q5 (High) and Q6 (Low).

**22Ω Gate Resistors** are used to control switching speed and reduce EMI, while **10kΩ Pull-down Resistors** ensure the gates stay off during startup.


---

## Component List

| Component | Purpose | Image |
| :--- | :--- | :--- |
| **ESP32-DEVKITC-32D** | [cite_start]Microcontroller for SPWM generation and control logic[cite: 62]. | ![ESP32 Module](placeholder-small-esp32) |
| **IRG4PF50W** | [cite_start]High-power IGBT switch for the inverter bridge[cite: 35]. | ![IRG4PF50W IGBT](placeholder-small-igbt) |
| **IR2110** | [cite_start]High/Low side driver IC for driving IGBT gates[cite: 18]. | ![IR2110 Driver IC](placeholder-small-ir2110) |
| **TLP350** | [cite_start]Optocoupler for galvanic isolation of logic signals[cite: 10]. | ![TLP350 Opto](placeholder-small-tlp350) |
| **GBJ2510** | [cite_start]Bridge Rectifier to convert AC Mains to DC[cite: 23]. | ![GBJ2510 Bridge](placeholder-small-bridge-rectifier) |
| **470µF Capacitor** | [cite_start]Smoothing capacitor for the high-voltage DC Bus[cite: 24]. | ![470uF Capacitor](placeholder-small-cap-470uf) |
| **UF4007** | [cite_start]Fast recovery bootstrap diode for high-side driving[cite: 20]. | ![UF4007 Diode](placeholder-small-diode) |
| **10k Potentiometer** | [cite_start]User input for setting motor frequency/speed[cite: 6]. | ![Potentiometer](placeholder-small-pot) |
| **NTC Thermistor** | [cite_start]Inrush current limiter for circuit protection[cite: 4]. | ![NTC Thermistor](placeholder-small-ntc) |
| **Fuse 10A** | [cite_start]Main power input safety protection[cite: 3]. | ![10A Fuse](placeholder-small-fuse) |
| **Resistor 22Ω** | [cite_start]Gate resistor to dampen ringing and limit peak current[cite: 21]. | ![22 Ohm Resistor](placeholder-small-resistor-22) |
| **Resistor 10kΩ** | [cite_start]Pull-down resistor for IGBT gates[cite: 34]. | ![10k Resistor](placeholder-small-resistor-10k) |
