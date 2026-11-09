# FM-Radio-Project
2nd Year Undergrad, 2nd Semester (2024) Labs module group project. 

This repository contains the documentation for an FM Radio built around an Arduino Nano.

> **Note:** This project was completed during my second year of university, prior to gaining professional industry experience. While the designs and code reflect my foundational understanding at the time, I have included an **[Engineering Retrospective]** section below to critique these early design choices based on my current industry standards.

---

## My Contributions

This was a group project along with 3 other course mates. To maintain transparency regarding my specific work, my responsibilities included:
* **Project Leader**
* **Breadboard Assembly**
* **PCB Design** (via Autodesk Eagle)
* **PCB and Veroboard Assembly**
* **PCB and Veroboard Testing**
* **Kickstarter-style promotional video**

---

## Hardware & Component Constraints

The component selection for this project (including the specific FM module, the TDA2822 dual audio amplifier, and various UI modules) was strictly dictated by the university laboratory's available inventory at the time. We were required to build the most feature-rich system possible using only these provided parts.

---

## System Architecture

The system operates on a Master-Slave architecture controlled by the Arduino Nano.

* **I2C Bus:** The Arduino acts as the master device communicating with the PCF8563 RTC (Real-Time Clock) module, the Humidity/Temperature sensor, and the FM Radio chip.
* **GPIO:** Used to interface with the 16x2 LCD display, IR receiver (for remote control), physical push buttons, rotary encoder, and an HC-SR04 ultrasonic distance sensor (used for an automatic proximity-mute feature).
* **Audio Amplification:** The LOUT and ROUT signals from the FM chip are fed into a TDA2822 dual audio amplifier IC, which drives two 1W 8-Ohm speakers.

<img width="729" height="468" alt="image" src="https://github.com/user-attachments/assets/f9afcf48-8846-4ef1-85a1-545e5dcdec37" />

*(Figure 1: High-level functional requirements and architecture)*

---

## Schematic and PCB Design

The circuit schematic and PCB were designed using Autodesk Eagle. The board routes a 9V power supply, stepping down and distributing 5V and 3.3V logic to the respective modules.

<img width="679" height="561" alt="image" src="https://github.com/user-attachments/assets/82fb6866-45d4-4b69-8954-b9d8b9869e06" />

*(Figure 2: Initial circuit schematic)*

<img width="975" height="624" alt="image" src="https://github.com/user-attachments/assets/994bba6b-d105-41ac-b775-d1626c542a39" />

*(Figure 3: PCB Layout sent for manufacturing)*

---

## Final Prototype

Despite some manufacturing and footprint challenges (detailed in the retrospective below), the board was successfully hand-assembled, patched, and housed in a laser-cut acrylic enclosure.

<img width="498" height="343" alt="image" src="https://github.com/user-attachments/assets/2026ce8c-8667-4ff5-84d5-24c2cce39046" />

 
*(Figure 4: The final assembled FM radio unit)*

---

## 🛠️ Engineering Retrospective: What I Would Do Differently Today

Looking back at this Year 2 project with the benefit of industry experience, there are several key areas where my approach to hardware design and embedded software has significantly matured. If I were designing this system today, I would overhaul the following:

### 1. PCB Grounding & Copper Pours
* **The Past Issue:** In the original design, the polygon ground plane failed to connect to the Arduino Nano because the library component used non-standard (oval) pad shapes. This DRC/ratsnest oversight resulted in an ungrounded MCU, which had to be fixed post-manufacturing with a manual jumper wire. See Figures 5 & 6 below.
* **Current Approach:** I now enforce rigorous Design Rule Checks (DRC) and visually verify all copper pour thermals and net connections before generating Gerbers. I also build and vet custom footprints rather than blindly trusting third-party libraries.

<img width="823" height="310" alt="image" src="https://github.com/user-attachments/assets/8598fa56-9beb-4e21-a1e3-37292ce7c321" />

  *(Figure 5 NANO not grounded (see ratsnest) and solution.).*

### 2. Footprint Orientation and Verification
* **The Past Issue:** The FM chip footprint was placed incorrectly relative to the antenna pinouts, effectively mirroring the connections. The physical fix involved flipping the chip upside down during soldering. See figure 6.
* **Current Approach:** I currently use 3D STEP models during the layout phase to verify component orientation, mating, and enclosure clearances. I also print a 1:1 scale paper mock-up of the PCB to physical test-fit complex ICs before ordering the board.

<img width="304" height="130" alt="image" src="https://github.com/user-attachments/assets/41970533-314d-493b-9c07-fef58f5a27e6" />

  *(Figure 6 Incorrect placement for antenna and solution.).*

### 3. Mixed SMD/THT Assembly Oversights
* **The Past Issue:** An SMD RTC module footprint was accidentally selected for a Through-Hole board design, requiring a fragile "dead-bug" style wiring workaround during hand assembly.
* **Current Approach:** Strict Bill of Materials (BOM) management. Today, I predominantly design with SMD components to reduce board size, improve signal integrity, and lower manufacturing costs via PCBA services. Any THT components are strictly segregated and vetted during the schematic capture phase.

<img width="322" height="290" alt="image" src="https://github.com/user-attachments/assets/9b0adb07-093b-4b05-8b03-8dfccf36ec51" />

*(Figure 7 SMD Workaround.).*

### 4. Firmware & UI Responsiveness
* **The Past Issue:** The rotary encoder exhibited inconsistent behavior and jitter due to the use of blocking `delay()` statements in the main Arduino loop.
* **Current Approach:** I would write an interrupt-driven (ISR) firmware architecture for the rotary encoder to ensure zero missed steps. The UI and sensor polling would be handled using non-blocking state machines (e.g., using `millis()` timers) to ensure the system remains highly responsive.
