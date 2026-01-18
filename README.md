# UPI-8042-examples

This repository contains practical examples, low-level routines and technical documentation for the Intel 8742 (UPI-41 family), tested on real hardware.

The goal of this project is to make the Intel UPI-41/42 microcontrollers usable again for hobbyists, retro-computing enthusiasts and embedded developers by providing accurate, working references that are currently very hard to find.

---

## Background and Motivation

When starting this project, almost no complete or reusable examples for the Intel UPI-41 / 8742 were available online. Most information was scattered, incomplete, or limited to very specific applications.

The hardware and general system architecture used in this repository is based on the following (now archived) reference project:

https://web.archive.org/web/20250321054243/https://devster.monkeeh.com/z80/upi42/

That page was the **only publicly available practical project** found that documents a working UPI-42 system with external program memory.

All code, timing analysis and peripheral drivers in this repository are derived from:

- That reference hardware concept
- **Intel official documentation** for the UPI-41 / UPI-42 family
- Extensive testing on real hardware

This repository exists because, at the time of development, there was:
- No reusable driver library
- No accurate timing explanations
- No documented LCD or delay routines
- No clear explanation of the internal timer behavior

---

## Hardware Overview

The system is based on:

- **Intel 8742 (UPI-41 family)**
- External EEPROM for program memory (AT28C64B)
- Address latch for multiplexed port usage (74HC374)
- Discrete wiring (no companion chips like the 8243)

Key characteristics:

- External program memory enabled (EA tied high)
- Program memory accessed through SYNC
- Port P1 latched externally
- Internal RAM used for variables and control
- Internal timer used for accurate delays

All examples assume a **4 MHz crystal** unless explicitly stated otherwise.

---

## What This Repository Provides

- Accurate **delay routines** based on the internal timer
- Fully working **HD44780 LCD driver (4-bit mode)**
- GPIO control examples
- Blink and basic I/O tests
- Timing explanations based on instruction cycles and timer prescaling
- Minimal, well-commented assembly code for the UPI instruction set

All routines are written with the limitations and quirks of the UPI-41 architecture in mind.

---

## What This Repository Does NOT Try to Do

- It is **not** an 8051 tutorial
- It does **not** assume full instruction-set compatibility with other Intel MCUs
- It does **not** use port expanders (P4–P7 / 8243)
- It does **not** rely on simulators — everything is validated on hardware

---

## Sources and References

- Intel UPI-41 / UPI-42 official datasheets and manuals
- Archived UPI-42 system reference:
  https://web.archive.org/web/20250321054243/https://devster.monkeeh.com/z80/upi42/

---

## License

This project is released under the MIT License.
Feel free to use, modify and adapt it for personal or educational projects.

---

## Status

This repository is a work in progress.
Additional examples, documentation and schematics will be added as the project evolves.
