<p align="center">
  <img src="https://capsule-render.vercel.app/api?type=rect&color=0:232526,100:414345&height=120&text=TrashPanda&fontSize=48&fontColor=ffffff&desc=Crypto%20bot%20on%20a%20hardened%20Raspberry%20Pi&descSize=16&descAlignY=78&fontAlignY=40" alt="TrashPanda" width="100%" />
</p>

<p align="center">
  <img src="https://img.shields.io/badge/status-archived%20(Oct%202026)-lightgrey?style=flat-square" alt="Archived" />
  <img src="https://img.shields.io/badge/Freqtrade-1E88E5?style=flat-square" alt="Freqtrade" />
  <img src="https://img.shields.io/badge/Raspberry%20Pi%20400-A22846?style=flat-square&logo=raspberrypi&logoColor=white" alt="Raspberry Pi" />
  <img src="https://img.shields.io/badge/mode-dry--run-blue?style=flat-square" alt="Dry run" />
  <img src="https://img.shields.io/badge/source-private%20(on%20request)-lightgrey?style=flat-square" alt="Private source" />
</p>

[← Back to profile](../README.md)

## Overview

TrashPanda ran the open-source **Freqtrade** engine in dry-run mode on a dedicated Raspberry Pi 400. The infrastructure was the easy part. The interesting part is the **research**, which ended in a clear, evidence-based decision to stop.

## Infrastructure

- Dedicated always-on Pi 400, reachable over **Tailscale**.
- **SSH hardened:** key-only auth. I found a config drop-in that was silently ignored because of file ordering, fixed it, and verified the result with `sshd -T`.
- Safe relocation and power-cycle procedure, with a database integrity check and throttling check afterwards.
- A local live display of bot status on an attached monitor.

## The research

1. **Pre-registered splits:** train, validation and holdout periods were fixed before any testing. The holdout was never touched.
2. **Defining the bar:** with 0.80% round-trip costs, an entry signal had to raise the take-profit-first rate by **+8.8 percentage points** to break even. The first candidate managed +0.16 pp.
3. **Exit geometry refuted:** I tested and rejected the hypothesis that better exits could rescue the strategy.
4. **Power analysis:** I pooled two years of data across BTC, ETH and SOL. Outcomes were highly correlated (ρ ≈ 0.65), so 117 trade episodes were worth only **about 51 independent ones**. The minimum detectable lift was about **15.8 pp**, almost twice the 8.8 pp bar.

**Conclusion:** no entry rule could be confirmed on this dataset, and adding more coins wouldn't help because they move together. Only years more data could. I parked the strategy work instead of tuning until something looked good by chance.

## Tech

`Freqtrade` · `Python` · `Raspberry Pi OS` · `Tailscale` · `SSH` · `SQLite` · `Kraken historical data`

## What I learned

- Statistical power is a design requirement, not an afterthought. Check it *before* you get attached to results.
- Correlated samples shrink your effective sample size, often dramatically.
- "We can't know yet" is a legitimate, valuable result.
