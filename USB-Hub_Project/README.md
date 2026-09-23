USB Hub Project — FE1.1S 4-Port Hub with Custom Enclosure
A complete hardware + mechanical design project: a 4-port USB 2.0 hub built around the FE1.1S controller, paired with a custom 3D-printed enclosure. This document covers both halves of the project — the PCB schematic review and correction, and the enclosure design process — from the lab's mixed-signal hardware work.

What this project is
The board (QuadPort_USB_Hub, PCB7) is a 4-port USB 2.0 hub built around the FE1.1S hub controller (28-pin SSOP), with a micro-USB upstream connection for power and data, and three mounting holes for chassis mounting. The hub is bus-powered — it draws its 5V directly from the upstream micro-USB connection rather than an external supply — and powers its four downstream USB-A ports directly off that same 5V rail with no external power-switch IC.

The project has two parts: the electrical schematic (designed in EasyEDA Pro) and a custom enclosure (designed in Shapr3D) sized to fit the board with mounting standoffs and connector cutouts.
