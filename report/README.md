# T-Accelerator Hardware Signoff & Implementation Verification Reports

This directory contains the authoritative implementation, place-and-route (P&R), timing closure, and resource utilization signoff reports for the **T-Accelerator (TiAccel)** core running at **300.00 MHz** on the AMD / Xilinx Alveo U200 datacenter accelerator card.

All reports were produced directly by **AMD Vivado™ Design Suite v2024.2 (lin64, Build 5239630)** during the official production link run `k19_pnr_r25_20260929`.

---

## 1. Physical Signoff Summary

| Metric | Target / Specification | Vivado Physical Signoff | Status | Authoritative Report |
| :--- | :--- | :--- | :---: | :--- |
| **Target Device** | AMD Alveo U200 PCIe | `xcu200-fsgd2104-2-e` | **PASS** | [`full_utilization_routed.rpt`](full_utilization_routed.rpt) |
| **Kernel Clock Frequency** | **300.00 MHz** (`T = 3.333 ns`) | **300.030 MHz** (`T = 3.333 ns`) | **PASS** | [`timing_summary_postroute_physopted_300mhz.rpt`](timing_summary_postroute_physopted_300mhz.rpt) |
| **Worst Negative Slack (WNS)** | $> 0.000\text{ ns}$ | **+0.009 ns** | **PASS** | [`timing_summary_postroute_physopted_300mhz.rpt`](timing_summary_postroute_physopted_300mhz.rpt) |
| **Total Negative Slack (TNS)** | $0.000\text{ ns}$ | **0.000 ns** (0 failing endpoints) | **PASS** | [`timing_summary_postroute_physopted_300mhz.rpt`](timing_summary_postroute_physopted_300mhz.rpt) |
| **Worst Hold Slack (WHS)** | $> 0.000\text{ ns}$ | **+0.006 ns** | **PASS** | [`timing_summary_postroute_physopted_300mhz.rpt`](timing_summary_postroute_physopted_300mhz.rpt) |
| **Total Hold Slack (THS)** | $0.000\text{ ns}$ | **0.000 ns** (0 failing endpoints) | **PASS** | [`timing_summary_postroute_physopted_300mhz.rpt`](timing_summary_postroute_physopted_300mhz.rpt) |
| **Net Route Completion** | 100.00% Fully Routed | **1,371,961 / 1,371,961 nets** (0 errors) | **PASS** | [`route_status.rpt`](route_status.rpt) |
| **Design Rule Check (DRC)** | 0 Errors | **0 Errors** (Clean signoff) | **PASS** | [`drc_postroute.rpt`](drc_postroute.rpt) |
| **Total On-Chip Power** | $< 75.0\text{ W}$ PCIe Slot Limit | **50.281 W** (Dynamic: 46.49 W, Static: 3.79 W) | **PASS** | [`power_postroute.rpt`](power_postroute.rpt) |
| **Junction Temperature** | $< 85.0\text{ }^\circ\text{C}$ | **51.2 °C** (Margin: 33.8 °C) | **PASS** | [`power_postroute.rpt`](power_postroute.rpt) |
| **Hardware Silicon Test** | Bit-exact verification | **PASS** (100% bit-exact across test suite) | **PASS** | [`onboard_silicon_verification_r25.md`](onboard_silicon_verification_r25.md) |

---

## 2. Resource Utilization Breakdown

### 2.1 Full-Chip Post-Route Utilization (Device: `xcu200-fsgd2104-2-e`)
*Source report: [`full_utilization_routed.rpt`](full_utilization_routed.rpt)*

| Resource Type | Used | Fixed / Prohibited | Available | Utilization (%) |
| :--- | :---: | :---: | :---: | :---: |
| **CLB LUTs** | **498,988** | 143,711 | 1,182,240 | **42.21%** |
| ├─ LUT as Logic | 456,237 | 132,493 | 1,182,240 | 38.59% |
| └─ LUT as Memory (Distributed RAM / SRL) | 42,751 | 11,218 | 591,840 | 7.22% |
| **CLB Registers (FFs)** | **642,008** | 196,788 | 2,364,480 | **27.15%** |
| **Block RAM (BRAM Tile)** | **903.5** | 0 | 2,160 | **41.83%** |
| ├─ RAMB36E2 | 835 | 202 | 2,160 | 38.66% |
| └─ RAMB18E2 | 137 | 7 | 4,320 | 3.17% |
| **UltraRAM (URAM288)** | **660** | 20 | 960 | **68.75%** |
| **DSP Blocks (DSP48E2)** | **1,331** | 7 | 6,840 | **19.46%** |
| **CLB Slices** | 106,816 | 0 | 147,780 | 72.28% |

---

### 2.2 Accelerator Kernel (`tiaccel_top`) Post-Route Utilization
*Source report: [`kernel_utilization_routed.rpt`](kernel_utilization_routed.rpt)*

| Subsystem | LUT | LUTAsMem | REG (FF) | BRAM (Tiles) | URAM | DSP48E2 |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **Platform Shell (Static / BLP)** | 192,431 (16.28%) | 20,288 (3.43%) | 269,387 (11.39%) | 268 (12.41%) | 20 (2.08%) | 10 (0.15%) |
| **User Dynamic Region Budget** | 989,809 (100.0%) | 571,552 (100.0%) | 2,095,093 (100.0%) | 1,892 (100.0%) | 940 (100.0%) | 6,830 (100.0%) |
| **tiaccel_top Kernel (Core)** | **306,582 (30.97%)** | **22,463 (3.93%)** | **372,621 (17.79%)** | **635.5 (33.56%)**| **640 (68.09%)** | **1,321 (19.34%)** |
| **Unused User Budget** | 683,227 (69.03%) | 549,089 (96.07%) | 1,722,472 (82.21%) | 1,256.5 (66.44%) | 300 (31.91%) | 5,509 (80.66%) |

---

### 2.3 Per-Module Architectural Breakdown
*Extracted directly from [`hierarchy_routed.rpt`](hierarchy_routed.rpt)*

| Module Instance | Architecture & Function | Total LUT | FF | BRAM | URAM | DSP48E2 |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: |
| `u_sfuc` | **Special Function Unit Core (Activation, RMSNorm, Stream TopK)** | 140,853 (45.9%) | 125,071 (33.6%) | 71.0 (11.2%) | 0 | **225 (17.0%)** |
| `u_tpu` | **Weight-Stationary Parallel Tree Matrix Engine (PM16 × TC128)** | 37,347 (12.2%) | 41,818 (11.2%) | 0.0 | 0 | **1,024 (77.5%)** |
| `u_mic_sp_dram` | **Off-Chip DDR0 DRAM Controller & DMA Arbiter** | 29,513 (9.6%) | 52,593 (14.1%) | 124.0 (19.5%) | 0 | 0 |
| `u_mic_sp` | **On-Chip L2 AXI SRAM Interconnect & Arbiter** | 27,024 (8.8%) | 51,100 (13.7%) | 124.0 (19.5%) | 0 | 0 |
| `u_tacc` | **16-Lane Tensor Accumulator & Dequant Scale Core** | 23,707 (7.7%) | 22,857 (6.1%) | 38.0 (6.0%) | 0 | **64 (4.8%)** |
| `u_rspu` | **Residual & Transpose Unit (FP16 Attention V Transpose)** | 11,572 (3.8%) | 13,729 (3.7%) | 8.0 (1.3%) | 0 | 0 |
| `u_tubuf` | **Unified Tensor Buffer (1 MiB 16-Bank BRAM)** | 10,096 (3.3%) | 18,081 (4.9%) | 232.0 (36.5%) | 0 | 0 |
| `u_tosc` | **Tensor Operand Stream Sequencer & Address Generator** | 9,916 (3.2%) | 12,441 (3.3%) | 0.0 | 0 | **5 (0.4%)** |
| `u_tdmc` | **Tensor Data Movement Controller** | 5,824 (1.9%) | 9,612 (2.6%) | 15.0 (2.4%) | 0 | **3 (0.2%)** |
| `u_ildmc` | **Instruction / Descriptor DMA Controller** | 3,596 (1.2%) | 6,366 (1.7%) | 7.5 (1.2%) | 0 | 0 |
| `u_axi_sram` | **20 MiB UltraRAM Distributed On-Chip Memory** | 2,944 (1.0%) | 10,717 (2.9%) | 16.0 (2.5%) | **640 (100%)** | 0 |
| `Other / Glue` | **CT Master, Global Register (GR), SLR Crossing Buffers** | 4,190 (1.4%) | 8,236 (2.2%) | 0.0 | 0 | 0 |
| **TiAccel Total** | **Top-Level Kernel (`tiaccel_top`)** | **306,582** | **372,621** | **635.5** | **640** | **1,321** |

---

### 2.4 Multi-SLR Distribution & Laguna Interconnect
*Source report: [`slr_utilization_routed.rpt`](slr_utilization_routed.rpt)*

| Physical Die / SLR | Primary Assigned Logic | CLB LUTs (%) | CLB Registers (%) | Block RAM (%) | URAM (%) | DSP48E2 (%) |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: |
| **SLR0 (Bottom)** | PCIe Gen3x16 DMA, DDR4 Mem Controller, AXI SRAM, SFUC | 183,257 (46.50%) | 190,446 (24.16%) | 181.5 (25.21%) | 256 (80.00%) | 228 (10.00%) |
| **SLR1 (Middle)** | Host MMIO, Pipeline Inter-SLR Repeater Stages, Shell Logic | 201,392 (51.10%) | 295,728 (37.52%) | 341.5 (47.43%) | 148 (46.25%) | 7 (0.31%) |
| **SLR2 (Top)** | **TPU Matrix Engine (PM16 × TC128)**, TUBuf, TOSC Sequencer | 114,339 (29.01%) | 155,834 (19.77%) | 380.5 (52.85%) | 256 (80.00%) | **1,096 (48.07%)** |

- **Total Inter-SLR SLLs Used**: **16,063 wires** (SLR2 $\leftrightarrow$ SLR1: 8,825 wires / 51.07%, SLR1 $\leftrightarrow$ SLR0: 7,238 wires / 41.89%) with 0 unrouted nets.

---

## 3. On-Board Silicon Hardware Verification

*Source log: [`onboard_silicon_verification_r25.md`](onboard_silicon_verification_r25.md)*

The bitstream generated from this P&R run (`tiaccel_top.xclbin`, SHA-256 `5b517dea...`, UUID `92aedefb-454d-9529-58fc-03f57e212318`) was deployed and executed on a physical **AMD Alveo U200** PCIe board running at native **300.00 MHz** (without frequency throttling).

| Hardware Test Suite | Verification Type | Workload / Checks | Latency / Throughput | Silicon Result |
| :--- | :--- | :--- | :--- | :---: |
| **CDA** (Compressed-Domain Attention) | Bit-exact verification | 20 test vectors | 1.136 ms | **PASS 20/20** |
| **LM16** (Language Model 16-layer) | Golden Model Equivalence | 66 checks, top1 254995 | 0.519 ms | **PASS** |
| **LM32** (Language Model 32-layer) | Full Attention + MLP | 130 checks, top1 255008 | 1.220 ms | **PASS** |
| **LM32high** (High-Precision LM32) | Dynamic quantization stress | 130 checks, top1 255008 | 1.217 ms | **PASS** |
| **W65** (End-to-End LLM Transformer) | Multi-Head Attention + Norm | 528/528 bit-exact tokens | **12.52 – 12.67 ms/token** (~80 tok/s) | **PASS 528/528** |
| **Gemma 4 E2B Speculative Decoding**<br>(Host CPU 9950X + Alveo U200) | Collaborative Inference<br>(Target Q8_0 + Drafter MTP) | 32 Prompts × 96 Tokens<br>(Bit-exact with Target Greedy) | **56.2 tok/s**<br>(**2.45× speedup** vs CPU 22.9) | **PASS 32/32** |

---

## 4. Report File Index

| Report File | File Size | Description & Key Evidence |
| :--- | :---: | :--- |
| [`timing_summary_postroute_physopted_300mhz.rpt`](timing_summary_postroute_physopted_300mhz.rpt) | ~170 KB | Complete Vivado post-route timing signoff summary (WNS +0.009 ns, 300.030 MHz kernel clock, zero violations). |
| [`route_status.rpt`](route_status.rpt) | 714 B | Routed net status: 1,371,961 routable nets, 1,371,961 fully routed nets, 0 routing errors. |
| [`full_utilization_routed.rpt`](full_utilization_routed.rpt) | 30 KB | Device-level utilization report showing CLBs, LUTs, FFs, BRAMs, URAMs, DSPs, and clock buffers. |
| [`kernel_utilization_routed.rpt`](kernel_utilization_routed.rpt) | 2.4 KB | Accelerator kernel vs. platform shell resource allocation breakdown. |
| [`slr_utilization_routed.rpt`](slr_utilization_routed.rpt) | 6.7 KB | Per-SLR logic distribution and inter-SLR Laguna SLL crossing analysis. |
| [`power_postroute.rpt`](power_postroute.rpt) | 83 KB | Post-route vector-based thermal and electrical power dissipation (50.281 W total, 51.2 °C junction). |
| [`drc_postroute.rpt`](drc_postroute.rpt) | ~3 KB | Post-route design rule check (DRC) executive summary confirming 0 critical warnings and 0 DRC errors (leaf netlist sanitized). |
| [`methodology_postroute.rpt`](methodology_postroute.rpt) | ~2.5 KB | Xilinx UltraFast design methodology compliance audit summary (leaf netlist sanitized). |
| [`hierarchy_routed.rpt`](hierarchy_routed.rpt) | 203 KB | Detailed hierarchical module-level resource audit (confirming 1,024 DSPs in `u_tpu`, 225 DSPs in `u_sfuc`, etc.). |
| [`onboard_silicon_verification_r25.md`](onboard_silicon_verification_r25.md) | 2.0 KB | On-board physical execution test suite results on Alveo U200 silicon. |
