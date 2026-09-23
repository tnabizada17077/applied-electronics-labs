# USB Hub Project — FE1.1S 4-Port Hub with Custom Enclosure

A complete hardware + mechanical design project: a 4-port USB 2.0 hub built around the FE1.1S controller, paired with a custom 3D-printed enclosure. This document covers both halves of the project — the PCB schematic review and correction, and the enclosure design process — from the lab's mixed-signal hardware work.

## What this project is

The QuadPort USB Hub is a 4-port USB 2.0 hub built around the **FE1.1S** hub controller (28-pin SSOP), with a micro-USB upstream connection for power and data, and three mounting holes for chassis mounting. The hub is bus-powered — it draws its 5V directly from the upstream micro-USB connection rather than an external supply — and powers its four downstream USB-A ports directly off that same 5V rail with no external power-switch IC.

The project has two parts: the electrical schematic (designed in EasyEDA Pro) and a custom enclosure sized to fit the board with mounting standoffs and connector cutouts.

## Hardware- schematic design and review

The initial schematic connected the FE1.1S's core USB signal pins (the four downstream D+/D- pairs, the upstream D+/D-, the crystal oscillator, and REXT bias resistor) correctly, but a review against the FE1.1S datasheet's pin description table found that several pins were left unconnected that are actually required for correct operation, since a bare EasyEDA library symbol had generically labeled some of them "NC" rather than their real datasheet functions.

The following corrections were identified and applied:

- **Internal regulator loop** — the FE1.1S generates 3.3V and 1.8V internally through two cascaded regulators (5V→3.3V→1.8V). This requires the regulator outputs to be wired back to their corresponding inputs:
  - **VD18_O (pin 12) → VD18 (pin 28)**
  - **VD33_O (pin 21) → VD33 (pin 13)**

- **Decoupling capacitors**, explicitly required by the datasheet on both regulator outputs:
  - **10µF at VD18_O (pin 12)** to GND — added as C1
  - **10µF at VD33_O (pin 21)** to GND — added as C4

- **BUSJ (pin 19) tied to GND** — since the hub is bus-powered (not self-powered), this input needed a defined logic level rather than floating.

- **10kΩ pull-up on OVCJ (pin 26) to the VD33_O rail** — added as R2, since the design has no external over-current-sensing power-switch IC, this holds the active-low over-current input at a defined "no fault" level instead of floating.

Pins confirmed safe to leave unconnected, given this design has no status LEDs, no external EEPROM, and no external power-switch IC: **TESTJ (27)**, **XRSTJ (17)** — optional, since the chip has a built-in Power-On-Reset — **VBUSM (18)**, **DRV (22)**, **LED1 (23)**, **LED2 (24)**, and **PWRJ (25)**.

### Net naming mismatch (found and resolved)

The issue with the regulator loop connections was identified during the work, as the connections appeared correct on the schematic but were not actually connected. This caught a bug: the two loop connections were **not actually wired together**, despite looking connected on the schematic — the net flag names on each side didn't match exactly (`VD18_O` vs `VD18`, and `VD33_O` vs `VD33`), so EasyEDA was treating them as four separate, unconnected nets rather than two. The flags were renamed so each pair matches exactly, and the fix has been confirmed.

All other nets were checked against the netlist and confirmed correct: the crystal oscillator circuit, all four downstream USB data pairs, the upstream micro-USB data pair, REXT's 2.7kΩ bias resistor to GND, VDD5 tied to the +5V rail alongside all port power, and BUSJ/OVCJ as described above.

### PCB import

The board was prepared for mechanical integration by placing three non-plated mounting holes at its corners in EasyEDA Pro, positioned to give a stable multi-point mount despite the board's stepped outline (it narrows from a wide four-port USB row down to a section holding the controller IC and micro-USB connector).

The board was exported from EasyEDA Pro as a STEP file, including the PCB substrate and full component models (so connector shapes and heights carry over accurately), while excluding vias, silkscreen, and copper signal layers, which have no mechanical relevance and only add unnecessary import complexity. The file was exported as an assembly, keeping the PCB and component bodies as separate, selectable geometry, and imported into Shapr3D on Windows with import quality prioritized over speed.

### Box design

Rather than tracing the board's exact stepped outline, the enclosure uses a simple rectangular bounding box sized to the board's maximum length and width, offset outward by a few millimeters for clearance. This block was extruded to height and shelled to 2mm wall thickness, leaving the top face open for a lid, with roughly 2mm of standoff clearance between the enclosure floor and the underside of the board.

Board and component heights were measured directly from the imported STEP geometry using Shapr3D's point-to-point measurement tool with axis deltas enabled, since the board's non-rectangular shape makes single-edge measurements unreliable. The tallest features — the USB ports — top out at approximately 13.6mm above the board's resting height inside the shell; the enclosure's internal height was extended a few millimeters above this (to roughly 16mm total) to leave clearance for the lid.

### Materials

The design targets **ABS** for its heat resistance, given that four active USB ports drawing power will generate some heat around the controller and connectors. ABS requires an enclosed, heated-bed printer to avoid warping; **PETG** is the planned fallback if cost or printer access makes ABS impractical, offering similar heat tolerance with a more forgiving print process. PLA remains an option for early prototype/fit-check prints before committing to a final material.
