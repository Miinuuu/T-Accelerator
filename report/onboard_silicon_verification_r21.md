# r21 board result (2026-09-24)

Image: ../k19_pnr_r21_20260924/hw_300mhz/tiaccel_top.xclbin
(sha256 7a364588..., uuid f5fd1c4f-d109-8a6a-f904-8cca445d789d), DATA_CLK **300 MHz, not scaled**
(FREQ_MHZ=300, routed WNS +0.005 ns / WHS +0.006 ns; phys_opt AggressiveExplore).
Source: main 3a4fbd265 (SNAPSHOT.txt).  Post-link finished by hand because main moved to
dbdd33855 (.gitignore only) during the build; see ../k19_pnr_r21_20260924/POSTLINK_MANUAL.txt.
Runner and fixtures built from main dbdd33855 (sw/ identical to 3a4fbd265); every case's
ir_sha256 equals the r20 kit's (cda a421525d, lm16 25bec906, lm32 42e0279a, lm32high 60113251,
w65 e072a5b5).  Memory contract ddd1885d (TUBUF 1 MiB, AXI SRAM 20 MiB).

| case | result | replay_ms | reads | writes |
|---|---|---|---|---|
| cda | PASS 20/20 bit-exact | 1.199 | 448 | 2730 |
| lm16 | PASS 66 checks, top1 254995 | 0.556 | 306 | 461 |
| lm32 | PASS 130 checks, top1 255008 | 1.299 | 862 | 1232 |
| lm32high | PASS 130 checks, top1 255008 | 1.276 | 863 | 1232 |
| w65 | PASS 528/528 bit-exact | see below | ~22.0K | 69,428 |

Full run: board_r21_kit/logs/20260924_203053_board/summary.txt (taken while a 4-process xsim regression loaded the machine:
w65 56.7 ms, 14.16 ms/token).

W65 timing on an idle machine (20:40, load < 1.5), same conditions back to back:

| image + kit | replay_ms | ms/token |
|---|---|---|
| r21 + r21 kit | 50.08 / 50.46 / 50.61 / 50.68 / 50.59 | 12.52 - 12.67 |
| r20 + r20 kit | 51.81 / 50.35 | 12.95 / 12.59 |

r21 equals r20 within noise: the replay is Host-bound, so the 292.7 -> 300 MHz clock and the
r21 cut latencies (TOSC->TPU weight +2, MIC local RX +1, RSPU skids, SFUC MMIO read +1 cycle)
do not show at the token level.  Under load both images read ~14.2 ms/token.
