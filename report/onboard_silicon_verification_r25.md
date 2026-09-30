# r25 Board Silicon Verification Results (2026-09-30)

Production Bitstream: `tiaccel_top.xclbin` (300.00 MHz Signoff Image)  
- **SHA-256**: `5b517dea7eb526a45e9045c6db5f994a798476ab4a13d6f09194dd79c0d0fcb0`  
- **UUID**: `92aedefb-454d-9529-58fc-03f57e212318`  
- **DATA_CLK**: **300.00 MHz** (WNS: **+0.009 ns**, TNS: **0.000 ns**, WHS: **+0.006 ns**, 0 Violations)  
- **Hardware Platform**: AMD / Xilinx Alveo U200 PCIe (`xcu200-fsgd2104-2-e`)  
- **Memory Contract**: TUBUF 1 MiB + On-Chip AXI SRAM 20 MiB (`TIACCEL_U200_SRAM_20M`)  

---

## 1. Regression Test Suite Results

Measured directly on Alveo U200 silicon hardware via XRT 2.19.194:

| Case | Result | Replay Latency (ms) | Host Reads | Host Writes | Status |
|---|---|---:|---:|---:|:---:|
| **CDA** (Compressed-Domain Attention) | PASS 20/20 bit-exact | 1.136 | 452 | 2,730 | **PASS** |
| **LM16** (16-Layer Language Model) | PASS 66 checks, top1 254995 | 0.519 | 313 | 461 | **PASS** |
| **LM32** (32-Layer Language Model) | PASS 130 checks, top1 255008 | 1.220 | 873 | 1,232 | **PASS** |
| **LM32high** (High-Precision LM32) | PASS 130 checks, top1 255008 | 1.217 | 871 | 1,232 | **PASS** |
| **W65** (Multi-Head Attention Chain) | PASS 528/528 bit-exact | 50.08 – 50.68 | 22,008 | 69,428 | **PASS** |

- **Standalone Token Latency (W65 4-Step Chain)**:
  - **Idle Host**: **12.52 – 12.67 ms / token** (~80 tok/s decode throughput)
  - **Loaded Machine (4-process regression background load)**: 14.16 – 14.62 ms / token (~70 tok/s)

---

## 2. Gemma 4 E2B Speculative Decoding Co-Processing (Host CPU 9950X + Alveo U200 FPGA)

- **Target Model (Verification)**: Gemma 4 E2B Q8_0 on Host CPU (AMD Ryzen 9 9950X, 16 cores, `llama.cpp` CPU-native engine, 12 OpenMP threads)
- **Draft Model (Generation)**: Gemma 4 Assistant (MTP) Drafter on FPGA (Alveo U200, TiAccel 300 MHz, SINT4 QAT r4 weights, CDA compressed sint8 KV-cache, fused projection launches)
- **r25 Production Serving (32 Prompts × 96 Tokens, K8 Learned Stop, q12c, Resident KV Delta)**:
  - **CPU 9950X Target-Only Baseline**: 22.9 tok/s (Verify: 43.7 ms)
  - **CPU 9950X Target + U200 Drafter**: **56.2 tok/s** (**2.45× speedup**, Acceptance Length $\alpha = 3.142$, Verify 48.9 ms, exposed Draft 8.1 ms)
  - **Mathematical Equivalence**: 100% bit-exact output match with Target greedy generation across all 32 benchmark prompts
- **Live Terminal Interactive Chat Demonstration (`fig/demo.gif`)**:
  - Sustained interactive throughput: **28.5 tok/s**, Prefill latency: **96 ms**, Acceptance Length: $\alpha = 2.04$
