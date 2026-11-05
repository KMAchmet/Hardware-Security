# Hardware Trojan Infiltration & Countermeasures in FPGA Systems

An academic research project analyzing stealthy Hardware Trojan (HT) insertion mechanisms within FPGA Electronic Design Automation (EDA) toolchains and investigating potential detection countermeasures.

Developed for the Hardware Security course at the Department of Computer Engineering & Informatics (CEID), University of Patras.

## Table of Contents

* [Overview](#overview)
* [Key Concepts](#key-concepts)
* [Methodology & Toolchain](#methodology--toolchain)
* [Experimental Results](#experimental-results)
* [Comparison with Related Work](#comparison-with-related-work)
* [Countermeasures & Future Work](#countermeasures--future-work)
* [References](#references)

## Overview

This research examines a stealthy attack vector targeting FPGA systems via compromised backend EDA tools. Unlike traditional hardware Trojans injected during design or synthesis, the studied attack model utilizes Disconnected Programmable Interconnect Points (TPIP).

The Trojan remains completely detached and dormant in the generated bitstream—bypassing static verification and Proof-Carrying Hardware (PCH) checks—and is reconnected only during the post-configuration programming phase via modified programming utilities.

### Primary Objectives:

* Demonstrate stealthy key-leakage attacks on an AES Cryptographic Core (8-bit data interface) hosted on a Lattice `iCE40HX-1k` FPGA.
* Evaluate the physical overhead (LUT count, power consumption, propagation delay).
* Discuss the limitations of standard bitstream verification and highlight defense techniques, including Machine Learning detection and Read-back verification.

## Key Concepts

* **Connection-Based Conditional Trigger (TPIP):** The Trojan trigger/payload logic is placed during Place-and-Route (PnR) but disconnected prior to bitstream generation.
* **Post-Configuration Activation:** Activation occurs strictly during device programming by re-enabling the TPIP bit.
* **Stealth Overhead:** Operates with minimal resource utilization and near-zero power/delay variances to evade side-channel analysis.
* **Key Leakage Payload:** Secret keys from the AES core are multiplexed onto available I/O pins without altering standard functional output logic.

## Methodology & Toolchain

The research leverages open-source FPGA design tools to model synthesis, placement, routing, and bitstream manipulation:

| Stage | Tool | Description |
| ----- | ----- | ----- |
| **Synthesis** | [Yosys](https://yosyshq.net/yosys/) | Converts HDL code into a BLIF netlist representation. |
| **Place & Route** | [Arachne-pnr](https://github.com/YosysHQ/arachne-pnr) / nextPNR | Places logic blocks and routes interconnects; models TPIP positioning. |
| **Bitstream Analysis** | [Project IceStorm](https://clifford.fm/icestorm) | Packs/unpacks Lattice iCE40 bitstreams for configuration analysis. |

## Experimental Results

Experimental evaluations conducted on an AES encryption core targeting the Lattice `iCE40HX-1k` FPGA demonstrate extremely low overhead, making detection via traditional side-channel analysis difficult.

| Resource / Parameter | Clean Design | Compromised Design | Difference |
| ----- | ----- | ----- | ----- |
| **4-input LUTs** | 543 / 1280 | 551 / 1280 | **+8 LUTs (+1.45%)** |
| **Flip-Flops (FFs)** | 319 | 319 | **0 (0.00%)** |
| **Critical Path Delay** | 19.82 ns | 19.97 ns | **+0.15 ns (+0.76%)** |
| **Dynamic Power** | 10.246 mW | 10.448 mW | **+0.202 mW (+1.96%)** |

## Comparison with Related Work

| Feature | Proposed Model | Krieg et al. [2] | Lin et al. (MOLES) [12] | Chakraborty et al. [14] |
| ----- | ----- | ----- | ----- | ----- |
| **Abstraction Level** | Bitstream / Post-PnR | RTL / Netlist | RTL / ASIC | Direct Bitstream |
| **Trojan Footprint** | 8 LUTs | Variable (LUT-level) | 49 Gates | Ring Oscillators (~150) |
| **Payload Effect** | AES Key Leakage | Privilege Escalation | Power Side-Channel | Thermal Stress (>160°C) |
| **Trigger Type** | TPIP (Post-Config) | Malicious LUT | Always-On | Always-On |

## Countermeasures & Future Work

Because static verification and pre-configuration checks fail to detect post-configuration re-connections, future defensive research focuses on:

1. **Post-Configuration Read-Back Verification:** Reading back the FPGA configuration directly from device registers after programming and comparing it with the trusted bitstream hash.
2. **Machine Learning Bitstream Classification:** Training decision trees (e.g., ID3, Random Forests) on structural bitstream features to flag anomalous interconnect patterns with high accuracy (>98%).

## References

1. S. Bhunia et al., *"Hardware Trojan attacks: Threat analysis and countermeasures,"* Proc. IEEE, 2014.
2. C. Krieg, C. Wolf, A. Jantsch, *"Malicious LUT: a stealthy FPGA trojan injected and triggered by the design flow,"* ICCAD, 2016.
3. Q. A. Ahmed, T. Wiersema, M. Platzner, *"Malicious routing: circumventing bitstream-level verification for FPGAs,"* DATE, 2021.
4. A. Palumbo et al., *"Is your FPGA bitstream hardware Trojan-free? Machine learning can provide an answer,"* J. Syst. Architect., 2022.
