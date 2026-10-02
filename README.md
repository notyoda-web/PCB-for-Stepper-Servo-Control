# STM32 Black Pill Stepper & Servo Control Board - Pin Mapping & Connections

This document details the exact pin-to-pin net connections and interface routing between the STM32 Black Pill microcontroller, the power subsystems, the A4988 stepper drivers, the servo headers, and communication peripherals, as established in the KiCad schematic (`Capture d'écran 2026-10-02 215708.png`)[cite: 3].

---

## Power Input & Regulation Subsystem

* **J1 (XT60PW-M Power Input):**
  * Pin `P` (Positive / 12V rail): Connects directly to the main 3S LiPo battery input line, feeding the input terminals of both buck converters (`12V`) and the VMOT pins of A4988 drivers `A1` and `A2`.
  * Pin `N` (Negative / GND): Connects to the system common ground (`GND`).
* **3.3V Buck Converter:**
  * Input: Connected to `12V` rail from J1[cite: 3].
  * Output: Connected to the `3.3V` net, providing power to the Black Pill `VDD`/`3.3V` pins and auxiliary logic[cite: 3]. Bulk smoothing capacitor `C1` is placed across the output[cite: 3].
* **5V Buck Converter:**
  * Input: Connected to `12V` rail from J1[cite: 3].
  * Output: Connected to the `5V` net, supplying power to the four hobby servo motor headers (`N1`, `N2`, `N5`, `N6`)[cite: 3]. Bulk smoothing capacitor `C2` is placed across the output[cite: 3].

---

## Stepper Motor Subsystem (A4988 Drivers & Connectors)

* **Driver A1 (Left Stepper Driver):**
  * **Power & Decoupling:** `VMOT` connected to `12V`[cite: 3] with electrolytic decoupling capacitor `C3` tied to `GND`[cite: 3]; `VDD` connected to `3.3V`[cite: 3].
  * **Control Pins:** 
    * `STEP` $\rightarrow$ Connected to MCU pin `PA1`[cite: 3].
    * `DIR` $\rightarrow$ Connected to MCU pin `PA2`[cite: 3].
    * `ENABLE` $\rightarrow$ Connected to MCU pin `PA6`[cite: 3].
    * Microstepping (`MS1`, `MS2`, `MS3`) $\rightarrow$ Tied to MCU pins `pb2`, `pb1`, `pb0` respectively[cite: 3].
    * `SLEEP` & `RESET` $\rightarrow$ Tied together on the module.
  * **Motor Output (`N4`):**
    * Pins `1B`, `1A`, `2A`, `2B` mapped directly from A1 driver pins 3, 4, 5, 6 to motor connector `N4` (`1A1_181`, `1A`, `2A`, `2B`)[cite: 3].

* **Driver A2 (Right Stepper Driver):**
  * **Power & Decoupling:** `VMOT` connected to `12V`[cite: 3] with electrolytic decoupling capacitor `C4` tied to `GND`[cite: 3]; `VDD` connected to `3.3V`[cite: 3].
  * **Control Pins:**
    * `STEP` $\rightarrow$ Connected to MCU pin `PB15`[cite: 3].
    * `DIR` $\rightarrow$ Connected to MCU pin `PB14`[cite: 3].
    * `ENABLE` $\rightarrow$ Connected to MCU pin `PB5`[cite: 3].
    * Microstepping (`MS1`, `MS2`, `MS3`) $\rightarrow$ Tied to MCU pins `pb14` (shared/routed), `pb13`, `pb12`[cite: 3].
    * `SLEEP` & `RESET` $\rightarrow$ Tied together on the module.
  * **Motor Output (`N3`):**
    * Pins `1B`, `1A`, `2A`, `2B` mapped directly from A2 driver pins to motor connector `N3`[cite: 3].

---

## Servo Motor Subsystem

All four servo headers share a common power (`5V`) and ground (`GND`) bus, with individual PWM control lines driven directly by MCU GPIO/timer pins[cite: 3]:

* **Servo Header N2:** Pin 1 (Signal) $\rightarrow$ MCU pin `PB6`[cite: 3]; Pin 2 $\rightarrow$ `5V`[cite: 3]; Pin 3 $\rightarrow$ `GND`[cite: 3].
* **Servo Header N1:** Pin 1 (Signal) $\rightarrow$ MCU pin `PB7`[cite: 3]; Pin 2 $\rightarrow$ `5V`[cite: 3]; Pin 3 $\rightarrow$ `GND`[cite: 3].
* **Servo Header N6:** Pin 1 (Signal) $\rightarrow$ MCU pin `PB8`[cite: 3]; Pin 2 $\rightarrow$ `5V`[cite: 3]; Pin 3 $\rightarrow$ `GND`[cite: 3].
* **Servo Header N5:** Pin 1 (Signal) $\rightarrow$ MCU pin `PB9`[cite: 3]; Pin 2 $\rightarrow$ `5V`[cite: 3]; Pin 3 $\rightarrow$ `GND`[cite: 3].

---

## Communication Interface (UART)

* **J3 (UART Header):**
  * Pin 1 (`Rx`) $\rightarrow$ Connected to Black Pill MCU pin `PA9` (`Tx` of USART1) for host ingestion[cite: 3].
  * Pin 2 (`Tx`) $\rightarrow$ Connected to Black Pill MCU pin `PA10` (`Rx` of USART1) for telemetry transmission[cite: 3].
  * Pin 3 (`GND`) $\rightarrow$ Connected to system common ground (`GND`)[cite: 3].
