# STM32 Black Pill Stepper & Servo Control Board - Pin Mapping & Connections

This document details the exact pin-to-pin net connections and interface routing between the STM32 Black Pill microcontroller, the power subsystems, the A4988 stepper drivers, the servo headers, and communication peripherals, as established in the KiCad schematic .

---

## Power Input & Regulation Subsystem

* **J1 (XT60PW-M Power Input):**
  * Pin `P` (Positive / 12V rail): Connects directly to the main 3S LiPo battery input line, feeding the input terminals of both buck converters (`12V`) and the VMOT pins of A4988 drivers `A1` and `A2`.
  * Pin `N` (Negative / GND): Connects to the system common ground (`GND`).
* **3.3V Buck Converter:**
  * Input: Connected to `12V` rail from J1.
  * Output: Connected to the `3.3V` net, providing power to the Black Pill `VDD`/`3.3V` pins and auxiliary logic. Bulk smoothing capacitor `C1` is placed across the output.
* **5V Buck Converter:**
  * Input: Connected to `12V` rail from J1.
  * Output: Connected to the `5V` net, supplying power to the four hobby servo motor headers (`N1`, `N2`, `N5`, `N6`). Bulk smoothing capacitor `C2` is placed across the output.

---

# Power Distribution & Decoupling Rationale

This document outlines the engineering reasons for using dual buck converters and four electrolytic capacitors in the STM32 Black Pill stepper and servo control board, as shown in the schematic and PCB layout.

---

## 1. The Use of Two Buck Converters

The board utilizes two separate step-down (buck) converters fed by the 3S LiPo battery (`12V` net) to establish distinct power domains:

* **Isolation of Inductive Noise:** Stepper motors and hobby servos generate massive voltage transients, back-EMF, and high-frequency current spikes on their power rails during switching and stalling. By separating the regulation paths, noisy high-power actuator rails are prevented from coupling directly into sensitive digital logic.
* **Dedicated Voltage Domains:** 
  * **5V Buck Converter:** Powers the four hobby servos (`N1`, `N2`, `N5`, `N6`), which draw heavy peak currents under load. Supplying them from a dedicated regulator prevents voltage sags from resetting the microcontroller.
  * **3.3V Buck Converter:** Supplies a clean, stable regulated voltage exclusively to the Black Pill microcontroller (`VDD`/`3.3V`), protecting its internal ADC, core logic, and communication peripherals from brownouts.

---

## 2. The Use of Four Electrolytic Capacitors (`C1`, `C2`, `C3`, `C4`)

The four bulk electrolytic capacitors serve specific filtering and energy-storage functions across the power architecture:

* **Regulator Output Smoothing (`C1` and `C2`):**
  * **`C1` (3.3V Rail):** Placed at the output of the 3.3V buck converter to filter out residual switching ripple, ensuring a steady, low-noise supply for the Black Pill.
  * **`C2` (5V Rail):** Positioned at the 5V regulator output to provide bulk charge reservoirs that instantly compensate for current surges when servos suddenly start moving or change direction.
* **Motor Driver Local Decoupling (`C3` and `C4`):**
  * **`C3` and `C4` (VMOT Decoupling):** Connected directly across the motor supply (`VMOT`) and ground (`GND`) of the two A4988 driver modules (`A1` and `A2`). 
  * **Transient Suppression:** Stepper drivers rapidly chop high currents through inductive windings, creating high-frequency voltage spikes that can destroy silicon. These bulk capacitors act as local energy buffers right next to the driver chips, absorbing back-EMF spikes and stabilizing the input voltage during rapid microstepping steps.


## Stepper Motor Subsystem (A4988 Drivers & Connectors)

* **Driver A1 (Left Stepper Driver):**
  * **Power & Decoupling:** `VMOT` connected to `12V` with electrolytic decoupling capacitor `C3` tied to `GND`; `VDD` connected to `3.3V`.
  * **Control Pins:** 
    * `STEP` $\rightarrow$ Connected to MCU pin `PA1`.
    * `DIR` $\rightarrow$ Connected to MCU pin `PA2`.
    * `ENABLE` $\rightarrow$ Connected to MCU pin `PA6`.
    * Microstepping (`MS1`, `MS2`, `MS3`) $\rightarrow$ Tied to MCU pins `pb2`, `pb1`, `pb0` respectively.
    * `SLEEP` & `RESET` $\rightarrow$ Tied together on the module.
  * **Motor Output (`N4`):**
    * Pins `1B`, `1A`, `2A`, `2B` mapped directly from A1 driver pins 3, 4, 5, 6 to motor connector `N4` (`1A1_181`, `1A`, `2A`, `2B`).

* **Driver A2 (Right Stepper Driver):**
  * **Power & Decoupling:** `VMOT` connected to `12V` with electrolytic decoupling capacitor `C4` tied to `GND`[cite: 3]; `VDD` connected to `3.3V`.
  * **Control Pins:**
    * `STEP` $\rightarrow$ Connected to MCU pin `PB15`.
    * `DIR` $\rightarrow$ Connected to MCU pin `PB14`.
    * `ENABLE` $\rightarrow$ Connected to MCU pin `PB5`.
    * Microstepping (`MS1`, `MS2`, `MS3`) $\rightarrow$ Tied to MCU pins `pb14` (shared/routed), `pb13`, `pb12`.
    * `SLEEP` & `RESET` $\rightarrow$ Tied together on the module.
  * **Motor Output (`N3`):**
    * Pins `1B`, `1A`, `2A`, `2B` mapped directly from A2 driver pins to motor connector `N3`.

---

## Servo Motor Subsystem

All four servo headers share a common power (`5V`) and ground (`GND`) bus, with individual PWM control lines driven directly by MCU GPIO/timer pins:

* **Servo Header N2:** Pin 1 (Signal) $\rightarrow$ MCU pin `PB6`; Pin 2 $\rightarrow$ `5V`; Pin 3 $\rightarrow$ `GND`.
* **Servo Header N1:** Pin 1 (Signal) $\rightarrow$ MCU pin `PB7`; Pin 2 $\rightarrow$ `5V`; Pin 3 $\rightarrow$ `GND`.
* **Servo Header N6:** Pin 1 (Signal) $\rightarrow$ MCU pin `PB8`; Pin 2 $\rightarrow$ `5V`; Pin 3 $\rightarrow$ `GND`.
* **Servo Header N5:** Pin 1 (Signal) $\rightarrow$ MCU pin `PB9`; Pin 2 $\rightarrow$ `5V`; Pin 3 $\rightarrow$ `GND`.

---

## Communication Interface (UART)

* **J3 (UART Header):**
  * Pin 1 (`Rx`) $\rightarrow$ Connected to Black Pill MCU pin `PA9` (`Tx` of USART1) for host ingestion.
  * Pin 2 (`Tx`) $\rightarrow$ Connected to Black Pill MCU pin `PA10` (`Rx` of USART1) for telemetry transmission[cite: 3].
  * Pin 3 (`GND`) $\rightarrow$ Connected to system common ground (`GND`)[cite: 3].
