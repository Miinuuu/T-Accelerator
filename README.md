# T-Accelerator — Integrated Tensor Accelerator

**FPGA acceleration for speculative decoding: CPU Target + Alveo U200 Drafter.**

The AMD Ryzen 9 9950X runs the Target model and verifies draft tokens. The U200 runs the drafter.
Current image: **r56**, at **300 MHz**. Data below was measured or reported on **2026-10-29**.

## Architecture

<p align="center">
  <img src="fig/top.png" width="900" alt="T-Accelerator top-level block diagram">
  <br><em>Top-level architecture (<a href="fig/top.pdf">PDF</a>)</em>
</p>

| Component | Function |
|---|---|
| TPU | PM16 × TC128 datapath with 1,024 DSP48E2. INT8/SINT4 dense and attention operations. |
| TACC | INT32 accumulation, GPTQ scaling and FP32 FMA. |
| SFUC | Activation functions, Softmax, RMSNorm and ArgMax/TopK. |
| Memory | 20 MiB URAM AXI SRAM, 1 MiB BRAM TUBUF and one DDR4 bank. |
| Control | Two command streams fetched from host memory. Registered credit crossings connect the three SLRs. |

The drafter keeps KV cache and activations on chip. DFlash and cflash use the same compute hardware.
See the [architecture contracts](../docs/architecture/README.md) for implementation details.

## Physical Implementation

Alveo U200 (`xcu200-fsgd2104-2-e`), Vivado/Vitis 2024.2, r56 routed design.

| Timing at 300 MHz | Result |
|---|---:|
| Setup slack (WNS) | +0.017 ns |
| Hold slack (WHS) | +0.004 ns |
| Failing endpoints | 0 setup / 0 hold |

**Kernel resources, excluding the platform shell:**

| Resource | Used | User budget | Utilization |
|---|---:|---:|---:|
| LUT | 373,680 | 1,004,480 | 37.20% |
| Registers | 420,000 | 2,106,020 | 19.94% |
| BRAM tiles | 696 | 1,900 | 36.63% |
| URAM | 640 | 940 | 68.09% |
| DSP | 1,418 | 6,833 | 20.75% |

LUT usage includes 27,155 LUTs used as memory. Percentages use the capacity remaining after the platform shell.

Sources: [kernel utilization](../build/k19_pnr_r56_20261028/reports/kernel_util_routed.rpt),
[post-route timing](../build/k19_pnr_r56_20261028/reports/hw_bb_locked_timing_summary_postroute_physopted.rpt).

## On-Board Results

Qwen3-4B Q8_0 Target, DFlash z-lab block 16, Ryzen 9 9950X, U200 r56 at 300 MHz.
The table reports mean decode throughput over two repetitions, each with eight prompts.

| Prompt set | Maximum new tokens | CPU + U200 (tok/s) | CPU-only (tok/s) |
|---|---:|---:|---:|
| short8 | 96 | 47.99 | 48.39 |
| med8 | 96 | 45.06 | 45.42 |
| short8 | 256 | 46.48 | 48.23 |

The U200 configuration uses 12 Target threads, with four physical cores reserved for Host and runner work.
Runner L3 isolation and huge pages are enabled. The CPU-only baseline uses the native `llama.cpp` DFlash loop with 16 threads.
The CPU drafter uses float-initialized Q8_0 weights. The U200 drafter uses QAT/codebook weights.

All 30 runs in the comparison passed token equality checks.
The U200 configuration is 0.8–3.6% below the native CPU-only baseline in these conditions.

Source: [board comparison and conditions](../docs/research/throughput.md#active-throughput-plan--cpu-vs-u200),
[run results](../build/work/tpu/mac_eff/cmp/run/runs.txt).

## Reproducibility

- Toolchain: Vivado/Vitis 2024.2, XRT 2.19.194, Ubuntu 24.04.
- [Image settings](../build/xrt/board/image.sh) and [run configs](../sw/spec/configs) define the execution defaults.
- The runner checks the image fingerprint before execution.
- Core RTL uses IEEE 1735 encryption for double-blind review. Vivado supports simulation, synthesis, implementation and bitstream generation.
- `hw_emu` validates functionality. DDR timing and bandwidth require board measurements.
