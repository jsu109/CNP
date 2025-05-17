## System Function Overview

- Controller shield board designed for integration with the Casio fx-82MS calculator PCB  
- Allows the microcontroller to **read keypresses** from the calculator’s keypad matrix  
- Supports **simulated keypress injection** by actively driving the matrix lines  
- **Powered by a standard 1.5V AA battery**, with voltage boosted to 3.3V for logic-level compatibility  
- Enables extended functionality, including:  
  <!-- - Acting as a **USB HID-compatible numpad**  
  - Performing **imperial-to-metric unit conversions**  
  - Providing **currency conversion features**  
  - Allowing **equation input from external sources** (e.g., Excel)  -->

## Schematic Planning

### MCU.kicad_sch

**Purpose:** microcontroller and its supporting circuitry. 
**Key Design Notes:**
- Controls button reading,emulating and communication to HID dongle via BLE.

**McuTODO**

- &#x274C; something
- &#x2705; something else





---

## Power.kicad_sch

**Purpose:** Manages power input, voltage regulation

**Key Design Notes:**
-  TLV61070ADBV boost converter boosts 1.5V to 3.3V
- Soft latch circuit to power on and off the MCU and associated Circuitry. 

    ## softLatch.kicad_sch

    **Purpose:** Implements a soft-latching circuit to maintain a power on state of the system when ON button is pressed. Latch is reset via software.

   **Key Design Notes:**
    - The latch is set when user presses the 'on' button for the system. 
    - The Latch is reset in software via the TLV_SHDN signal from the MCU.

    **softLatch TODO**

    - &#x2705; Task completed
    

**PowerTODO**

- &#x274C; something
- &#x2705; something else

---

### SwitchLogic.kicad_sch

**Purpose:** Handles connecting KOx and KIx lines

**Key Design Notes:**
- Two 74HC4051 are used to connect KOx and KIx using the SN74LVC1G3157DBVR SPDT switch. 

**SwitchLogicTODO**

- &#x274C; Implement Logic
- &#x2705; Define logic table
- &#x2705; Tidy schematic

---

### buttonReadLogic.kicad_sch

**Purpose:** 

**Key Design Notes:**
- 
---