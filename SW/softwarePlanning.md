# Software Planning Document: BLE User Input System

## Overview

This document outlines the architecture and design considerations for encapsulating BLEServer and BLEClient functionality into reusable, maintainable modules. The system consists of:

- A **BLEServer** on an ESP32-C3 handling user input (e.g., from an ARC sensor).
- A **BLEClient** running on a host (e.g., macOS Python app or another ESP32) that receives input and performs actions like volume control.

The server operates in an ultra-low-power mode with smart BLE advertising logic. The client ensures resilient reconnection and data interpretation.

---

## Goals

- Encapsulate BLE logic, ARC sensor processing, and system behavior into separate modules.
- Support extensibility for future sensors and additional client-side actions.
- Apply embedded software best practices: separation of concerns, clear interfaces, minimal coupling, and robust state handling.

---

## Modules: BLEServer (ESP32-C3 Peripheral)

### 1. `BLEServerManager`
**Responsibilities:**
- BLE setup (service, characteristic, advertising)
- Advertising interval management (fast/slow)
- Connection/disconnection callbacks
- BLE notifications

**Key Functions:**
- `init()`
- `startAdvertisingFast()`
- `startAdvertisingSlow()`
- `stopAdvertising()`
- `disconnectClient()`
- `notifyValue(String)`

**Dependencies:**
- Relies on callbacks/events triggered by `InputMonitor` or `ActivityManager`.

---

### 2. `InputMonitor`
**Responsibilities:**
- Manage the ARC sensor input
- Handle analog change detection or digital interrupts
- Map analog values to 0–9 range

**Key Functions:**
- `begin()`
- `getMappedValue()`
- `hasActivityChanged()`

**Dependencies:**
- Triggers `ActivityManager` or directly calls `BLEServerManager::notifyValue()` on change.

---

### 3. `ActivityManager`
**Responsibilities:**
- Track system activity using timestamps
- Detect inactivity
- Trigger transitions to low-power states

**Key Functions:**
- `updateActivity()`
- `isInactive()`
- `tick()` — called in `loop()`

**Dependencies:**
- Interacts with `BLEServerManager` to stop BLE on inactivity.

---

### 4. `PowerController` (optional)
**Responsibilities:**
- Control GPIOs to power external peripherals
- Manage light/deep sleep entry and exit

---

## Modules: BLEClient (macOS Python)

### 1. `BLEClientManager`
**Responsibilities:**
- Scan and connect to BLEServer
- Manage disconnection and reconnection logic
- Read from characteristic or subscribe to notifications (fallback)

**Key Functions:**
- `scan_and_connect()`
- `read_value_loop()`
- `handle_disconnect()`

---

### 2. `InputInterpreter`
**Responsibilities:**
- Interpret digit values (0–9) as commands
- Route commands to appropriate system action handlers

**Key Functions:**
- `interpret_input(value)`
- `execute_command(command)`

---

### 3. `SystemActionController`
**Responsibilities:**
- Control volume or other system-level commands (e.g., via `osascript` or platform APIs)
- Abstract platform-specific actions for extensibility

**Key Functions:**
- `set_volume(percent)`
- `mute()`
- `custom_action(value)`

---

## System Behaviors to Encapsulate

### BLEServer
- [x] Notify digit values when input changes
- [x] Start advertising on activity
- [x] Stop BLE on inactivity
- [x] Smart advertising interval (fast for reconnect, slow while connected)
- [x] Clean disconnect handling

### BLEClient
- [x] Auto-scan for BLEServer
- [x] Resilient reconnect
- [x] Polling-based BLE read
- [x] Volume control based on digit
- [ ] Expandable input interpreter (e.g., 'M' = mute, 'V' = volume up)

---

## Notes

- Modules should avoid direct `Serial.print()` or UI code — use a central logger.
- Keep BLE-specific code encapsulated to allow switching to other transports (e.g., WiFi, UART) later.
- Consider implementing **state machines** for server and client behavior transitions.

---

## Future Enhancements

- Add `ConfigManager` module to allow remote configuration via BLE characteristics
- Replace ARC input with capacitive touch or multiple buttons
- Support HID emulation on ESP32 (for platforms with USB host)
- Enable BLE pairing/security
- Extend Python client to a menu bar app or GUI

---