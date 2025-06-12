# PRISM – Parallel Register Interface System Module

**PRISM** is a modular, hardware-oriented communication interface designed for efficient parallel data exchange between microcontrollers and clients. It focuses on high-speed transfer, clear abstraction layers, and extensibility through a registry system for SIMD-style operations.

This repository serves as the entry point and meta-project for all PRISM components, including protocol documentation, API headers, firmware, registry, and tooling.

---

## 📦 Repository Structure

- `docs/` – Protocol documentation and system overview (CC-BY-SA 4.0)
- [`registry/`](https://github.com/RoseLeDark/prism-registry) – Function definitions, proposals, and standardization logic (CC-BY 4.0)
- [`api/`](https://github.com/RoseLeDark/prism-api) – Header-only client API for integration (MPL-2.0)
- [`api/arduino/`](https://github.com/RoseLeDark/prism-api-arduino) – Arduino tools and interface bindings (MIT or MPL-2.0)
- [`firmware/prism-esp-soft/`](https://github.com/RoseLeDark/prism-esp-soft) – Firmware for ESP32 coprocessor implementation (LGPL-2.1+)
- `LICENSE.md` – Licensing overview of all subcomponents

---

## 🔌 Communication Protocol

PRISM communicates over I²C and an 8-bit parallel data buffer. Up to 10 lines are used to bridge host and device(s):

- **I²C**: Control channel (device address selection, broadcast at `0x51`)
- **Lines 1–4 + 7–10**: Parallel 8-bit data transfer
- **Line 5**: Data strobe (new data)
- **Line 6**: Entry control (combine/write modes)

PRISM supports multiple ESP32-based SIMD coprocessors working in parallel.

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

Available operations include:

- `prism_set*` – Input register manipulation
- `prism_op_*` – Operation triggers
- `prism_add_*`, `prism_sub_*`, etc. – Inline ALU-style commands
- `prism_extract_*` – Result decoding

New functions can be registered via the Registry with vendor-specific suffixes.

---

## 🧬 PRISM Registry

The [`prism-registry`](https://github.com/RoseLeDark/prism-registry) defines:

- Function prototypes and naming conventions
- Required inputs/outputs, side effects, and examples
- Versioning and compatibility metadata
- Contribution rules for proposals

Each function must define behavior, advantages, and examples. Vendor-specific extensions (e.g. `_intel`, `_amd`) are supported.

---

## 🛠 Arduino & Microcontroller Support

PRISM supports hobbyist and embedded systems via:

- [`prism-api-arduino`](https://github.com/RoseLeDark/prism-api-arduino): Arduino-compatible API bindings
- [`prism-api`](https://github.com/RoseLeDark/prism-api): C/C++ headers for AVR, STM32, ESP32, etc.

Works with `Wire` or custom I²C implementations.

---

## 🔐 Licensing Overview

| Component                  | License        |
|----------------------------|----------------|
| `docs/`                    | CC-BY-SA 4.0   |
| `registry/`                | CC-BY 4.0      |
| `api/`                     | MPL-2.0        |
| `api/arduino/`             | MIT or MPL-2.0 |
| `firmware/prism-esp-soft/`| LGPL-2.1+      |

Full licensing terms are explained in [`LICENSE.md`](./LICENSE.md).

---

## 🤝 Contributions

We welcome proposals and code contributions. You can:

- Submit new SIMD functions to the [registry](https://github.com/RoseLeDark/prism-registry)
- Extend the firmware for new MCUs
- Improve integration with other platforms
- Translate documentation or enhance developer tools

---

## 🌐 Related Repositories

- [prism-api](https://github.com/RoseLeDark/prism-api)
- [prism-api-arduino](https://github.com/RoseLeDark/prism-api-arduino)
- [prism-esp-soft](https://github.com/RoseLeDark/prism-esp-soft)
- [prism-registry](https://github.com/RoseLeDark/prism-registry)

---
