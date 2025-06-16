# PRISM – Parallel Reduced Instruction SIMD Machine

**PRISM** is a HIGH-performance distributed SIMD computing for embedded systems

This repository serves as the entry point and meta-project for all PRISM components, including protocol documentation, API headers, firmware, registry, and tooling.


## Overview
PRISM ia a revolutionary SIMD (Single Instruction Multiple Data) frameword designed for distrubuted embedded computing. It provides a powerful API for parallel vector operations across multiple devices connected via I²C and parallel 8-bit buses.

## Key Features
- **256-Bit Vector Operations** - 4 banks * 8 Entrys * 32-bit each
- **Flexible Data Types** - INT8 to INT128 support with sub-entry granularity
- **Dual Bus Architecture** - Parallel 8-bit + I²C for optimal performance
- **Multi-Device Scaling** - Broadcast operations across multiple cores platforms
- **SSE-Inspire API** - Familiar interface for developers

## Architecture
```
+-------------------------------------------+
:           PRISM CORE                      :
+-------------------------------------------+
: Bank A   : Bank B   : Bank C   : Bank D   :
: 8*32 Bit : 8*32 Bit : 8*32 Bit : 8*32 Bit :
+-------------------------------------------+
:            Dual Bus Interface             :
: Parallel 8 bit Bus |         I²C          :
+-------------------------------------------+
```

### Data Organization

- **4 Banks:** A, B, C, D registers
- **8 Entries per Bank:** 32-bit each (256-bit total per Chunk)
- **Maximum precision:** 128-bit number across entries


## 📦 Repository Structure

- `docs/` – Protocol documentation and system overview (CC-BY-SA 4.0)
- [`registry/`](https://github.com/RoseLeDark/prism-registry) – Function definitions, proposals, and standardization logic (CC-BY 4.0)
- [`api/`](https://github.com/RoseLeDark/prism-api) – Header-only client API for integration (MPL-2.0)
- [`api/arduino/`](https://github.com/RoseLeDark/prism-api-arduino) – Arduino tools and interface bindings (MPL-2.0)
- [`firmware/prism-esp-soft/`](https://github.com/RoseLeDark/prism-esp-soft) – Firmware for ESP32 coprocessor implementation (LGPL-2.1+)
- `LICENSE.md` – Licensing overview of all subcomponents


## 🔌 Communication Protocol

PRISM communicates over I²C and an 8-bit parallel data buffer. Up to 10 lines are used to bridge host and device(s):

- **I²C**: Control channel (device address selection, broadcast at `0x51`)
- **Lines 1–4 + 7–10**: Parallel 8-bit data transfer
- **Line 5**: Data strobe (new data)
- **Line 6**: Entry control (combine/write modes)

PRISM supports multiple ESP32-based SIMD coprocessors working in parallel.


## 🧰 Client API Example

### Basic Usage

```cpp
#include <prism.h>

prism_err err = PR_OK;

// Initialize deviced
prdev_t device = prism_device_create(addr: 0x52, createI²C: true, config: NULL, device: &device);

v256i a = _v256_set8_uiv(1, 2, 3, 4, 5, 6, 7, 8); // Create a 256-bit vector with values from 1 to 8
_v256i b = _v256_set8_uiv(10, 20, 30, 40, 50, 60, 70, 80); // Create another vector with values from 10 to 80

// Send Vectoren to devices
err =_v256_store_bank_a(device, a, portMaxTime);
err =_v256_store_bank_b(device, b, portMaxTime);

// Send add opcode to device and Perform SIMD addition
err =_v256_add8_ui32(device, portMaxTime);

// Read result from device
_v256i c;
err =_v256_load_bank_c(device, &c, timeout);
if(err != PR_OK) return;

// Extract invidual values
uint32_t result = _v256_extract_ui32(c, 0);
```

### Multi-Device Operation
```cpp
#include <prism.h>

prdev_t device;
prism_device_create(addr: 0x52, createI²C: true, config: NULL, device: &device);

// Create Broadcast Device
prdev_t broadcast;
prism_device_create(addr: 0x51, createI²C: false, config: NULL, device: &broadcast);

// Send Same operation to all connected PRISM cores
_v256_store_bank_a(broadcast, vectore_data, portMaxTime);
_v256_add8_ui32(broadcast, portMaxTime);
...

```

### API Reference
- **prism_device_create(addr, createI²C, config, out device)** - Initialize device connection, must be implated on Host. See [`prism-api-arduino`](https://github.com/RoseLeDark/prism-api-arduino) for Arduino Implantation

- **_v256_set{1-8}_[ui/si}v(...)** - Create vectores with signed or unsigned integer
- **_v256_extract_[ui/si}{8/16/32}(vector, index)** - Extract values from vectors
### Operations
- **_v256_add{1-8}_{si/vi)32(device, timeout)** - SIMD addition
- **_v256_sub{1-8}_{si/vi)32(device, timeout)** - SIMD subtraction
- **_v256_mul{1-8}_{si/vi)32(device, timeout)** - SIMD multiplication
- **_v256_div{1-8}_{si/vi)32(device, timeout)** - SIMD division
- **_v256_not{1-8}_{si/vi)32(device, timeout)** - SIMD NOT
- **_v256_xor{1-8}_{si/vi)32(device, timeout)** - SIMD XOR
- **_v256_nor{1-8}_{si/vi)32(device, timeout)** - SIMD NOR
- **_v256_or{1-8}_{si/vi)32(device, timeout)** - SIMD OR
- **_v256_nand{1-8}_{si/vi)32(device, timeout)** - SIMD NAND
- **_v256_and{1-8}_{si/vi)32(device, timeout)** - SIMD AND
- **_v256_notC_x(device, timeout)** - SIMD NotC
### Data Transfare
- **_v256_store_bank_a(device, vectore_data, timeout)** - Write to regisrter on PRISM SIMD Device
- **_v256_load_bank_c(device, out vec, timeout)** - Reade to vector from PRISM Device

## Supported Platforms

### Current
- **ESP32:** Full Implementation with dual-bus support see: [prism-esp-soft](https://github.com/RoseLeDark/prism-esp-soft)
- **Arduino:** Host API Compaiblility see: [`prism-api-arduino`](https://github.com/RoseLeDark/prism-api-arduino)

### Planned
 - **FPGA:** Verilog/VHDL
 - **RISC-V:** Native instruction extension
 - **ARM Cortex-M:** Optimized implentations

## Use Case

- **Edge AI Comouting** - Distributed neural nextwork interfaces
- **Digital Signal Processing** - Real-Time audio/video processing
- **IoT Data Processing** - Sensor fusion and analystics
- **Cryptpgraphic Operations** - Parallel hash computations
- **Scientific Computing** - Distributed numerical simulations

## 🧬 PRISM Registry

The [`prism-registry`](https://github.com/RoseLeDark/prism-registry) defines:

- Function prototypes and naming conventions
- Required inputs/outputs, side effects, and examples
- Versioning and compatibility metadata
- Contribution rules for proposals

Each function must define behavior, advantages, and examples. Vendor-specific extensions (e.g. `_int`, `_foo`) are supported.

## 🔐 Licensing Overview

| Component                  | License        |
|:---------------------------|----------------|
| `registry/`                | CC-BY 4.0      |
| `api/`                     | MPL-2.0        |
| `api/arduino/`             | MPL-2.0        |
| `firmware/prism-esp-soft/` | LGPL-2.1+      |

Full licensing terms are explained in [`LICENSE.md`](./LICENSE.md).


## 🤝 Contributions

We welcome proposals and code contributions. You can:

- Submit new SIMD functions to the [registry](https://github.com/RoseLeDark/prism-registry)
- Extend the firmware for new MCUs
- Improve integration with other platforms
- Translate documentation or enhance developer tools


## 🌐 Related Repositories

- [prism-api](https://github.com/RoseLeDark/prism-api)
- [prism-api-arduino](https://github.com/RoseLeDark/prism-api-arduino)
- [prism-esp-soft](https://github.com/RoseLeDark/prism-esp-soft)
- [prism-registry](https://github.com/RoseLeDark/prism-registry)

## Roadmap
+ v. 2.0.0
    - [ ] 128-bit support 
    - [ ] 256 bit support
    - [ ] c++ interface with vectore, marix and other math objets
+ v1.5.0
    - [ ] Float32/Float16 support
    - [ ] Advanved vector operations (dot product, cross product)
    - [ ] Hardware abstract layer improvements
+ v.1.0.5
    - [X] Production-ready API
    - [X] Arduino Host Interface
    - [ ] esp32 firmware 
