# Connectivity Interface Module

**A Smart Gate Add-On for the Letron SL3000 Sliding Gate Opener**

The Connectivity Interface Module (CIM) is an ESP32-based add-on designed to integrate seamlessly with the Letron SL3000 sliding gate opener. It interfaces directly with the gate’s existing RF control infrastructure—the same system used by the original remotes—allowing smart control without modifying the base system. CIM adds Home Assistant integration, optional state monitoring, and remote triggering using standard hobbyist tools. All connections are made via screw terminals for a fully non-invasive, easily reversible install that preserves full compatibility with manufacturer remotes.

---

## 🚪 Features

* Home Assistant integration using **ESPHome**, a framework for controlling ESP32 devices over Wi-Fi
* Remote gate triggering via either ESPHome commands or the original Letron RF remotes
* Screw terminal connections for simple, fully reversible install
* Optional limit switch input for gate open/close state sensing
* USB-C port for easy firmware flashing and debugging, with onboard bootloader button

---

## 🧠 System Overview

The Connectivity Interface Module (CIM) physically interfaces with the Letron SL3000 main control board at the same 6-pin header where the original RF receiver connects. It sits in-line using a custom interposer PCB, allowing the original RF receiver to remain in place while the CIM takes over control of the gate trigger input.

The Letron controller expects a 5 V active-low signal on its trigger input to initiate a gate cycle. The CIM is responsible for generating this signal. It does so in two cases: either in response to a Home Assistant command received via ESPHome, or when it detects that the RF receiver has pulled its own output low after receiving a button press from a Letron remote. The CIM monitors the RF receiver’s output on a separate line and independently asserts a low signal to the Letron control board when a trigger condition is met. The logic for whether the gate opens or closes remains entirely within the Letron controller.

The CIM draws power from the same 12 V supply provided to the RF receiver. An onboard voltage regulator steps this down to 3.3 V for the ESP32 and associated circuitry. No external power supply is needed.

---

## 4. Getting Started

### 4.1 Prerequisites

* A running Home Assistant server/instance
* ESPHome installed on your computer (for compiling and flashing firmware)
* USB-C cable for firmware flashing
* PH1 screwdriver to remove the Letron enclosure
* Flat-blade (slotted) screwdriver for the 3.81 mm screw terminals

### 4.2 Firmware Flashing (Pre-Install)

1. Install ESPHome on your workstation or in Home Assistant.
2. In the repo’s `/firmware/` folder, copy `example.yaml` → `gate-controller.yaml`.
3. Edit `gate-controller.yaml`:

   * Set your Wi-Fi SSID and password
   * Configure a static IP (matching your router’s subnet, mask, gateway, DNS)
4. **Hold the onboard bootloader button down**, then connect the CIM via USB-C.
5. Run:

   ```bash
   esphome run gate-controller.yaml
   ```
6. After flashing, unplug USB-C and power-cycle the board (no button press). Confirm your router has assigned or reserved the specified IP.

### 4.3 Home Assistant Integration

1. In HA, go to **Settings → Devices & Services**.
2. ESPHome should appear under “Discovered Devices.”
3. Click **Configure**.
4. **Add your device**.
5. Test software connectivity by sending a gate trigger command from Home Assistant while the CIM is still USB-C connected—watch the ESPHome logs to confirm the ESP32 received and acted on the command.

### 4.4 Hardware Setup

1. **Power off the gate controller**

   * Remove the battery connection **and** switch off AC power to the onboard charger.
2. **Open the enclosure**

   * Use a PH1 screwdriver to remove the Letron SL3000 cover.
3. **Install the CIM interposer**

   * Unplug the original RF receiver from the 6-pin header on the Letron board.
   * Plug the CIM interposer PCB into that header using the 6-way screw terminal (MAIN_CONN)
   * Reconnect the RF receiver to the CIM's 6-pin header (RF_CONN)
   * Jump the **CL** and **OL** limit-switch terminals from the Letron controller to the CIM's 2-way screw terminal (LSW_CONN)

4. **Restore power**

   * Re-plug the 12 V feed and switch AC power back on.

---

## 5. Known Issues / Prototype Notes

> **Prototype status:** Only v0.4 was assembled. Design corrections are captured in v0.5 but will not be produced and tested.

* **Issue – Floating RF\_OUT line**
  Measured RF\_OUT at \~5 V idle—but it’s pulled high by the Letron controller, not driven by the RF board. When inactive it drifted to \~0.8 V, which the CIM’s v0.4 divider didn’t recognize as HIGH.

  **Workaround**
  Added a 10 kΩ pull-up to 5 V before the divider so the ESP32 sees a solid HIGH until the RF receiver pulls the line low.

  **Design update**
  Eliminate the divider and pull-up: RF\_OUT now connects directly to the ESP32 input using its internal 3.3 V pull-up.

* **Issue – Buck converter EN-pin logic**
  The 5 V buck converter’s EN pin must be LOW to enable output but was wired HIGH, so it stayed off.

  **Workaround**
  Cut the EN leg on the buck IC to force it LOW, enabling the converter.

  **Design update**
  Tie EN to GND so the converter is always enabled.

---

## 📂 Hardware

* `/hardware/v0.4/` – As-built v0.4 prototype files (do not produce):

  * `schematic.pdf` – PDF export for reference
  * `gerber/` – Gerber files for board fabrication
  * `bom.csv` – Bill of Materials

* `/hardware/v0.5/` – Improved v0.5 prototype files (not yet tested):

  * `schematic.pdf` – PDF export for reference
  * `gerber/` – Gerber files for board fabrication
  * `bom.csv` – Bill of Materials

## 📂 Firmware

* `/firmware/` – ESPHome configurations and examples:

  * `gate-controller.yaml` – Primary firmware for v0.4/v0.5 hardware

## ⚖️ License

[MIT License](LICENSE)

---

## 🤝 Credits

* **Developer:** ThienDDinh
* **Original System:** Letron SL3000 sliding gate opener
