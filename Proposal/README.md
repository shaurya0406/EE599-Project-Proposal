# **EE599 Project Overview**

## *Secure RISC-V SoC with CORDIC-Based Phase Computation Engine for Ground Station Antenna Arrays*



## The Problem

Think of a **ground station antenna array** used for radar or communication.
Each antenna element needs to be fed with the **right phase shift** so that the whole array beams signals in the desired direction.

* If phases are wrong → the beam points to the wrong place.
* Computing these phases involves **lots of sine, cosine, and multiplications** for every antenna, every time you change direction.
* A general CPU **can do it**, but it’s **slow and stalls** when burdened with so many trig operations.

So the question is: *can we build a small chip that takes this heavy math off the CPU’s shoulders, and does it faster, more securely, and more reliably?*

---

## Our Solution (The Chip We’re Building)

We’re designing a **custom ASIC** that looks like a mini secure computer-on-a-chip:

1. **A tiny RISC-V core**

   * Runs software, coordinates data movement.
   * Light and efficient (think of it as the “traffic cop”).

2. **A custom math accelerator (CORDIC + LUT)**

   * Special hardware unit that computes sines, cosines, and phase values much faster than a CPU.
   * Uses a hybrid method:

     * **LUT** (Lookup Table) for coarse values.
     * **CORDIC** (shift-add algorithm) for refinement.
   * This makes phase computation both **fast and area-efficient**.

3. **Secure and Reliable Memory**

   * SRAM protected with **ECC (Error Correcting Codes)** so if a bit flips, it can be detected and corrected.
   * Ensures data integrity during long radar sessions.

4. **Secure Boot & Crypto**

   * On power-up, the chip checks that the software is authentic (no tampered firmware).
   * Uses lightweight crypto (SHA-256, ECDSA).
   * Prevents malicious or accidental misuse in sensitive ground station setups.

5. **Bus Interface (APB/AXI-Lite)**

   * Provides a **clean path** for moving data between the RISC-V core, memory, and accelerator.
   * Ensures the accelerator doesn’t stall the CPU.

---

## Why This Matters

* **Real-world application**: Antenna arrays in ground stations (radar, comms).
* **Speed**: Instead of waiting for a CPU to grind through trig functions, the accelerator can compute many phase values quickly.
* **Security**: Ground stations are critical infrastructure. If someone tampers with firmware or data, you could point beams in the wrong direction. Secure boot + ECC makes this resilient.
* **Relevance to VLSI/EE599**: It’s not just RTL → you’ll touch **architecture design, synthesis, place & route, DFT, and tape-out**. This is the **full chip flow** that the course emphasizes.

---

## Implementation Strategy (Big Picture)

1. **Front-end (RTL design)**

   * Write the RISC-V core (or reuse a tiny one), the CORDIC/LUT engine, and memory system.
   * Verify with testbenches and golden models (Python/MATLAB).

2. **Verification & DFT**

   * ECC injection tests, phase accuracy checks, crypto test vectors.
   * Scan chains, MBIST for SRAM, boundary scan for chip-level testing.

3. **Back-end (Physical Design)**

   * Floorplan: place the RISC-V, accelerator, memory.
   * Timing closure: ensure 50–80 MHz operation.
   * Power routing and IR checks.
   * DRC/LVS clean before tape-out.

4. **Final Demo**

   * Chip computes antenna phases in real time.
   * Shows correct vs tampered firmware behavior.
   * Demonstrates ECC correcting injected memory errors.

---

## Foreseeable Issues & Mitigations

* **CORDIC timing closure** → Pipeline if needed.
* **Area constraints** → Keep crypto minimal, use small LUTs.
* **DFT overhead** → Plan scan & MBIST from day one.
* **Integration bugs** → Rely on software-driven tests + golden model comparisons early.
* **Schedule pressure** → Freeze architecture by proposal week, avoid scope creep.

---

