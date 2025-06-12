# PRISM – Parallel Register Interface System Module

**PRISM** is a modular, hardware-oriented communication interface designed for efficient parallel data exchange between microcontrollers and clients. It focuses on high-speed transfer, clear abstraction layers, and extensibility through a registry system for SIMD-style operations.

This repository serves as the entry point and meta-project for all PRISM components, including the protocol documentation, API headers, firmware, registry, and tooling.

---

## 📦 Repository Structure

- `docs/` – Protocol documentation and system overview (CC-BY-SA 4.0)
- `registry/` – Function definitions, proposals, and standardization logic (CC-BY 4.0)
- `api/prism-rp/` – Header-only client API for integration (MPL-2.0)
- `tools/arduino/` – Arduino tools and interface bindings (MIT or MPL-2.0)
- `firmware/prism-esp-soft/` – Firmware implementation for ESP32 devices (LGPL-2.1+)
- `LICENSE.md` – Licensing overview of all subcomponents

---

## 🔌 Communication Protocol

PRISM communicates over I²C combined with an 8-bit parallel buffer bus. This enables precise addressing of devices, broadcasting, and synchronized data transfer using a dual-strobe signaling method.

- **I²C** is used for target selection and control (addressable devices)
- **Parallel Bus (8-bit)** transfers raw data
- **Lines 5 and 6** function as clock and write-strobe signals

Each microcontroller (e.g. ESP32) can act as an intelligent SIMD-capable coprocessor, performing vector operations and responding to host commands.

---

## 🧰 Client API Example

```cpp
_pridev device = prism_dev_hs(addr, Flankspeed);
_v256i a = prism_set1_ui32(0xABCD);
prism_write_v256a(a, device, portMaxTime);
prism_op_notc(device);
_v256i c = prism_read_v256c(device, timeout);
uint32_t result = prism_extract_ui32(c, 0);
```
Operations include:
- prism_setX_*() – Set input registers
- prism_op_*() – Trigger logic operation
- prism_read_*() – Read result vector
- prism_add_*() – Direct computation commands
...and more via the PRISM Registry.

## 🧬 PRISM Registry

The registry (prism-registry) is a centralized database that defines:

- Standard vector operations and instruction set extensions

- Function naming conventions and vendor tags (e.g. _in, _nq)
- Versioning and required features for compatibility
- Proposal submission rules (incl. examples & constraints)

This makes PRISM an open yet standardized ecosystem for client-side SIMD logic.
🛠 Arduino & Microcontroller Integration

- PRISM can be used within Arduino environments or other MCU projects using:
- prism-arduino: Prebuilt helpers for Serial/I²C setup and command abstraction
- prism-api: Header-only interface compatible with AVR, ESP32, STM32, etc.

## 🔐 Licensing

Each subproject has its own license tailored to its role:
Component	License
- docs/	CC-BY-SA 4.0
- registry/	CC-BY 4.0
- prism-api/	MPL-2.0
- prism-arduino/	MIT or MPL-2.0
- prism-esp-soft/	LGPL-2.1+

For full details, see LICENSE.md
## 🤝 Contributing

PRISM welcomes contributions to:

- The registry (new function proposals)
- Arduino integration
- Additional hardware ports (e.g. RISC-V, STM32)
- Documentation and protocol translations

Fork this repo or open an issue in the relevant subproject to get started.

## 📫 Contact

For questions, suggestions, or contributions:

- GitHub Issues
- Project maintainers (see AUTHORS or GitHub profiles)
- Registry proposal submissions via registry/proposals/

## 🌐 Related Repositories

- prism-api – Header-only API
- prism-esp-soft – ESP32 firmware
- prism-registry – Instruction definitions

    prism-arduino – Arduino toolset

