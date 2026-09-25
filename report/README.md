# T-Accelerator Hardware Signoff & Implementation Verification Reports

This directory contains the authoritative implementation, place-and-route (P&R), timing closure, and resource utilization signoff reports for the **T-Accelerator (TiACCEL)** core running at **300.00 MHz** on the AMD Xilinx Alveo U200 datacenter accelerator card.

All reports were produced directly by **AMD Vivado™ Design Suite v2024.2 (lin64, Build 5239630)** during the official production link run `k19_pnr_r21_20260924`.

---

## 1. Physical Signoff Summary

| Metric | Target / Specification | Vivado Physical Signoff | Status | Authoritative Report |
| :--- | :--- | :--- | :---: | :--- |
| **Target Device** | AMD Alveo U200 PCIe | `xcu200-fsgd2104-2-e` | PASS | [`system_estimate_tiaccel_top.txt`](system_estimate_tiaccel_top.txt) |
| **Kernel Clock Frequency** | **300.00 MHz** (`T = 3.333 ns`) | **300.030 MHz** (`T = 3.333 ns`) | PASS | [`timing_summary_postroute_physopted_300mhz.rpt`](timing_summary_postroute_physopted_300mhz.rpt) |
| **Worst Negative Slack (WNS)** | $> 0.000\text{ ns}$ | **+0.005 ns** | PASS | [`timing_summary_postroute_physopted_300mhz.rpt`](timing_summary_postroute_physopted_300mhz.rpt) |
| **Total Negative Slack (TNS)** | $0.000\text{ ns}$ | **0.000 ns** (0 failing endpoints / 1,995,114) | PASS | [`timing_summary_postroute_physopted_300mhz.rpt`](timing_summary_postroute_physopted_300mhz.rpt) |
| **Worst Hold Slack (WHS)** | $> 0.000\text{ ns}$ | **+0.006 ns** | PASS | [`timing_summary_postroute_physopted_300mhz.rpt`](timing_summary_postroute_physopted_300mhz.rpt) |
| **Total Hold Slack (THS)** | $0.000\text{ ns}$ | **0.000 ns** (0 failing endpoints / 1,991,510) | PASS | [`timing_summary_postroute_physopted_300mhz.rpt`](timing_summary_postroute_physopted_300mhz.rpt) |
| **Net Route Completion** | 100.00% Fully Routed | **1,374,411 / 1,374,411 nets** (0 routing errors) | PASS | [`route_status.rpt`](route_status.rpt) |
| **Design Rule Check (DRC)** | 0 Errors | **0 Errors** (Clean signoff) | PASS | [`drc_postroute.rpt`](drc_postroute.rpt) |
| **Total On-Chip Power** | $< 75.0\text{ W}$ PCIe Slot Limit | **49.257 W** (Dynamic: 45.51 W, Static: 3.75 W) | PASS | [`power_postroute.rpt`](power_postroute.rpt) |
| **Junction Temperature** | $< 85.0\text{ }^\circ\text{C}$ | **50.7 °C** (Max ambient 74.3 °C) | PASS | [`power_postroute.rpt`](power_postroute.rpt) |
| **Hardware Silicon Test** | Bit-exact verification | **PASS** (100% bit-exact across test suite) | PASS | [`onboard_silicon_verification_r21.md`](onboard_silicon_verification_r21.md) |

---

## 2. Resource Utilization Breakdown

### 2.1 Full-Chip Post-Route Utilization (Device: `xcu200-fsgd2104-2-e`)
*Source report: [`full_utilization_routed.rpt`](full_utilization_routed.rpt)*

| Resource Type | Used | Fixed / Prohibited | Available | Utilization (%) |
| :--- | :---: | :---: | :---: | :---: |
| **CLB LUTs** | **497,997** | 143,711 | 1,182,240 | **42.12%** |
| ├─ LUT as Logic | 453,341 | 132,493 | 1,182,240 | 38.35% |
| └─ LUT as Memory (Distributed RAM / SRL) | 44,656 | 11,218 | 591,840 | 7.55% |
| **CLB Registers (FFs)** | **644,702** | 196,788 | 2,364,480 | **27.27%** |
| **Block RAM (BRAM Tile)** | **903.5** | 0 | 2,160 | **41.83%** |
| ├─ RAMB36E2 | 835 | 202 | 2,160 | 38.66% |
| └─ RAMB18E2 | 137 | 7 | 4,320 | 3.17% |
| **UltraRAM (URAM288)** | **660** | 20 | 960 | **68.75%** |
| **DSP Blocks (DSP48E2)** | **1,331** | 7 | 6,840 | **19.46%** |
| **CLB Slices** | 106,205 | 0 | 147,780 | 71.87% |
| **CARRY8 Primitives** | 4,414 | 1,089 | 147,780 | 2.99% |

---

### 2.2 Accelerator Kernel (`tiaccel_top`) Post-Route Utilization
*Source report: [`kernel_utilization_routed.rpt`](kernel_utilization_routed.rpt)*

| Subsystem | LUT | LUTAsMem | REG (FF) | BRAM (Tiles) | URAM | DSP48E2 |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **Platform Shell (Static / BLP)** | 194,501 (16.45%) | 22,205 (3.75%) | 272,849 (11.54%) | 268 (12.41%) | 20 (2.08%) | 10 (0.15%) |
| **User Dynamic Region Budget** | 987,739 (100.0%) | 569,635 (100.0%) | 2,091,631 (100.0%) | 1,892 (100.0%) | 940 (100.0%) | 6,830 (100.0%) |
| **tiaccel_top Kernel (Core)** | **303,513 (30.73%)** | **22,451 (3.94%)** | **371,853 (17.78%)** | **635.5 (33.56%)**| **640 (68.09%)** | **1,321 (19.34%)** |
| **Unused User Budget** | 684,226 (69.27%) | 547,184 (96.06%) | 1,719,778 (82.22%) | 1,256.5 (66.44%) | 300 (31.91%) | 5,509 (80.66%) |

---

### 2.3 Per-Module Architectural Breakdown
*Extracted directly from [`hierarchy_routed.rpt`](hierarchy_routed.rpt)*

| Module Instance | Architecture & Function | Total LUT | Logic LUT | Mem LUT | Flip-Flops | BRAM | URAM | DSP48E2 |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| `u_tpu` | **Weight-Stationary Parallel Tree Matrix Engine (PM16 × TC128)** | 37,366 | 27,654 | 9,472 | 42,354 | 0 | 0 | **1,024** |
| `u_sfuc` | **Fused Non-Linearity / Softmax / LayerNorm Vector Core** | 138,659 | 135,211 | 2,902 | 124,225 | 36 | 70 | **225** |
| `u_tacc` | **Multi-Row Tensor Accumulator & Add-Norm Core** | 23,487 | 22,638 | 634 | 22,484 | 29 | 18 | **64** |
| `u_tubuf` | **Unified Tensor Activation / Operand Ping-Pong Buffer** | 10,100 | 10,097 | 0 | 18,060 | 224 | 16 | **0** |
| `u_mic_sp` | **Internal Memory Interconnect & Cross-Bar Switch (SRAM)** | 26,926 | 25,166 | 1,760 | 51,099 | 120 | 8 | **0** |
| `u_mic_sp_dram` | **DRAM Channel Interconnect & Burst Controller** | 29,422 | 26,438 | 2,984 | 52,577 | 120 | 8 | **0** |
| `u_rspu` | **Residual Scalar Processing Unit (Element-Wise ALU)** | 11,236 | 10,592 | 644 | 13,684 | 7 | 2 | **0** |
| `u_tosc` | **Tensor Operand Stream Sequencer & Address Generator** | 9,900 | 9,815 | 0 | 12,442 | 0 | 0 | **5** |
| `u_tdmc` | **Descriptor DMA Controller & Command Engine** | 5,806 | 5,724 | 32 | 9,609 | 14 | 2 | **3** |
| `u_*_xing` | **Inter-SLR Dedicated Credit-Crossing Bridge Modules** | 1,941 | 164 | 1,776 | 3,270 | 0 | 0 | **0** |

> **Key Architectural Takeaways**:
> 1. **Matrix Engine DSP Exactness**: `u_tpu` consumes exactly **1,024 DSP48E2 primitives**, corresponding directly to the 512 dual-DSP leaves configured with pre-adder packing and cascade routing ($16 \times 64 = 1,024$ multipliers).
> 2. **Zero-BRAM Matrix Core**: The matrix core utilizes zero BRAM tiles and instead uses **9,472 LUTRAM/distributed memory bits** for zero-latency single-cycle weight and operand broadcast.
> 3. **Non-Linear Vector Engine (SFUC)**: The non-linear core (`u_sfuc`) accounts for **225 DSP48E2** units and **70 URAM288** blocks to perform high-throughput fused Softmax, GELU/SiLU, and FP16 vector operations without off-chip stalls.

---

### 2.4 Multi-SLR Distribution & Laguna Interconnect
*Source report: [`slr_utilization_routed.rpt`](slr_utilization_routed.rpt)*

| Physical Die / SLR | Primary Assigned Logic | CLB LUTs (%) | CLB Registers (%) | Block RAM (%) | URAM (%) | DSP48E2 (%) |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: |
| **SLR0 (Bottom)** | PCIe Gen3x16 DMA, DDR4 Mem Controller, AXI SRAM, SFUC | 181,269 (46.00%) | 189,774 (24.08%) | 181.5 (25.21%) | 256 (80.00%) | 228 (10.00%) |
| **SLR1 (Middle)** | Host MMIO, Pipeline Inter-SLR Repeater Stages, Shell Logic | 201,043 (51.02%) | 295,607 (37.51%) | 341.5 (47.43%) | 148 (46.25%) | 7 (0.31%) |
| **SLR2 (Top)** | **TPU Matrix Engine (PM16 × TC128)**, TUBuf, TOSC Sequencer | 115,685 (29.36%) | 159,321 (20.21%) | 380.5 (52.85%) | 256 (80.00%) | **1,096 (48.07%)** |

#### Inter-SLR Crossing Super Logic Lines (SLLs):
- **SLR2 $\leftrightarrow$ SLR1**: **8,811 / 17,280 SLLs used (50.99%)**
  - SLR1 $\to$ SLR2: 3,391 wires (2,011 pairs using both TX_REG and RX_REG Laguna flops).
  - SLR2 $\to$ SLR1: 5,420 wires (4,110 pairs using both TX_REG and RX_REG Laguna flops).
- **SLR1 $\leftrightarrow$ SLR0**: **7,301 / 17,280 SLLs used (42.25%)**
  - SLR0 $\to$ SLR1: 3,385 wires (2,104 pairs using both TX_REG and RX_REG Laguna flops).
  - SLR1 $\to$ SLR0: 3,916 wires (2,298 pairs using both TX_REG and RX_REG Laguna flops).
- **Total Physical SLL Lines Traversed**: **16,112 wires** with 0 unrouted signals.

---

## 3. On-Board Silicon Hardware Verification

*Source log: [`onboard_silicon_verification_r21.md`](onboard_silicon_verification_r21.md)*

The bitstream generated from this P&R run (`tiaccel_top.xclbin`, SHA-256 `7a364588...`, UUID `f5fd1c4f-d109-8a6a-f904-8cca445d789d`) was deployed and executed on a physical **AMD Alveo U200** PCIe board running at native **300.00 MHz** (without frequency throttling).

| Hardware Test Suite | Verification Type | Workload / Checks | Latency / Throughput | Silicon Result |
| :--- | :--- | :--- | :--- | :---: |
| **CDA** (Compressed-Domain Attention) | Bit-exact verification | 20 test vectors | 1.199 ms | **PASS 20/20** |
| **LM16** (Language Model 16-layer) | Golden Model Equivalence | 66 checks, top1 254995 | 0.556 ms | **PASS** |
| **LM32** (Language Model 32-layer) | Full Attention + MLP | 130 checks, top1 255008 | 1.299 ms | **PASS** |
| **LM32high** (High-Precision LM32) | Dynamic quantization stress | 130 checks, top1 255008 | 1.276 ms | **PASS** |
| **W65** (End-to-End LLM Transformer) | Multi-Head Attention + Norm | 528/528 bit-exact tokens | **12.52 ms/token** (~80 tok/s) | **PASS 528/528** |
| **Gemma 4 E2B Speculative Decoding**<br>(Host CPU 9950X + Alveo U200) | Collaborative Inference<br>(Target Q8_0 + Drafter MTP) | 32 Prompts × 96 Tokens<br>(Bit-exact with Target Greedy) | **32.63 tok/s**<br>(**1.77× speedup** vs CPU 18.45) | **PASS 32/32** |

> **Collaborative Co-Processing Setup (CPU 9950X + FPGA U200)**:
> - **Target Model (Verification)**: **Gemma 4 E2B** running on **Host CPU: AMD Ryzen 9 9950X** (16-Core / 32-Thread Zen 5 via `llama.cpp` Q8_0, 12 OpenMP threads).
> - **Draft Model (Generation)**: **Gemma 4 Assistant (MTP) Drafter** executed on **FPGA: AMD Alveo U200** (TiAccel 300 MHz, SINT4 QAT r4 weights, CDA compressed sint8 KV-cache).
> - **Real-Time Interactive Serving**: Verified live on hardware in `fig/demo.gif` (171 tokens, AL 2.04, 28.5 tok/s).

---

## 4. Report File Index

| Report File | File Size | Description & Key Evidence |
| :--- | :---: | :--- |
| [`timing_summary_postroute_physopted_300mhz.rpt`](timing_summary_postroute_physopted_300mhz.rpt) | 168 KB | Complete Vivado post-route timing signoff summary (WNS +0.005 ns, 300.030 MHz kernel clock, check_timing, intra/inter-clock tables). |
| [`route_status.rpt`](route_status.rpt) | 714 B | Routed net status: 1,374,411 routable nets, 1,374,411 fully routed nets, 0 routing errors. |
| [`full_utilization_routed.rpt`](full_utilization_routed.rpt) | 30 KB | Device-level utilization report showing CLBs, LUTs, FFs, BRAMs, URAMs, DSPs, and clock buffers. |
| [`kernel_utilization_routed.rpt`](kernel_utilization_routed.rpt) | 2.4 KB | Accelerator kernel vs. platform shell resource allocation breakdown. |
| [`slr_utilization_routed.rpt`](slr_utilization_routed.rpt) | 6.6 KB | Per-SLR logic distribution and inter-SLR Laguna SLL crossing analysis. |
| [`power_postroute.rpt`](power_postroute.rpt) | 81 KB | Post-route vector-based thermal and electrical power dissipation (49.257 W total, 50.7 °C junction). |
| [`drc_postroute.rpt`](drc_postroute.rpt) | 1.8 MB | Full post-route design rule check (DRC) report confirming 0 critical warnings and 0 DRC violations. |
| [`methodology_postroute.rpt`](methodology_postroute.rpt) | 162 KB | Xilinx UltraFast design methodology compliance audit. |
| [`hierarchy_routed.rpt`](hierarchy_routed.rpt) | 205 KB | Detailed hierarchical module-level resource audit (confirming 1,024 DSPs in `u_tpu`, 225 DSPs in `u_sfuc`, etc.). |
| [`system_estimate_tiaccel_top.txt`](system_estimate_tiaccel_top.txt) | 1.8 KB | Vitis `v++` compilation summary for target platform `xilinx_u200_gen3x16_xdma_2_202110_1`. |
| [`onboard_silicon_verification_r21.md`](onboard_silicon_verification_r21.md) | 1.8 KB | On-board physical execution test suite results on Alveo U200 silicon. |
