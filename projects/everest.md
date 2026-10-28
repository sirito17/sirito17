<p align="center">
  <img src="https://capsule-render.vercel.app/api?type=rect&color=0:1f4037,100:99f2c8&height=120&text=Everest&fontSize=48&fontColor=ffffff&desc=Read-only%20day-trading%20research%20analyst&descSize=16&descAlignY=78&fontAlignY=40" alt="Everest" width="100%" />
</p>

<p align="center">
  <img src="https://img.shields.io/badge/status-archived%20(Oct%202026)-lightgrey?style=flat-square" alt="Archived" />
  <img src="https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white" alt="Python" />
  <img src="https://img.shields.io/badge/tests-486%20passing-2ea44f?style=flat-square" alt="486 tests" />
  <img src="https://img.shields.io/badge/trading%20code-none%20by%20design-critical?style=flat-square" alt="No trading code" />
  <img src="https://img.shields.io/badge/source-private%20(on%20request)-lightgrey?style=flat-square" alt="Private source" />
</p>

[← Back to profile](../README.md)

## Overview

Everest was a **research analyst, not a trader**. It screened for pre-market and intraday movers, researched why they were moving, and delivered short watchlists to Telegram. It is **read-only by construction**: no broker SDK, no order code, no trading credentials. Its reports could inform a trading system, but a report is never an order.

## Architecture

```mermaid
flowchart LR
    FMP[FMP + Yahoo<br/>gainers] --> SCR[Declarative screener<br/>gap · price · volume]
    SCR --> CAT{Catalyst evidence}
    EDGAR[SEC EDGAR filings] --> CAT
    HALT[Nasdaq T1 halts] --> CAT
    CAT -->|evidence attached| RANK[Rank<br/>RVOL · low float]
    RANK --> DIG[15-min digest<br/>max 8 rows]
    DIG --> TG[Telegram]
    RANK --> SNAP[(Alert snapshots)]
    SNAP --> GRADE[Outcome grader<br/>15/30/60m · EOD · MFE/MAE]
```

## Highlights

- **Continuous market monitoring:** a pre-market summary, polling every three minutes, a market-open summary, and an end-of-day stop. All of it is orchestrated from Windows Task Scheduler into WSL, with heartbeat-based cancellation so no orphaned processes are left behind.
- **Evidence-gated alerts:** a ticker is only delivered if it has positive catalyst evidence attached, such as a recent SEC filing (with subject and official link) or an exact Nasdaq T1 halt. Messages say *evidence*, not "proof it caused the move".
- **UX iteration from live data:** the first live session produced 72 names across 26 messages, which nobody could use. I redesigned delivery into one compact digest every 15 minutes, capped at eight rows, with detailed research only on request.
- **Outcome grading:** every alert is snapshotted, then graded at 15, 30 and 60 minutes, end of day, and on max favorable/adverse excursion, which builds an honest track record.
- **Verified indicators:** RSI, EMA, SMA, MACD and Bollinger values were checked against TradingView on real tickers.
- **Backtest engine:** cost models, chronological splits, statistical validation and run cards.
- **486 automated tests**, plus Ruff linting.

## Tech

`Python` · `SEC EDGAR` · `Financial Modeling Prep API` · `Yahoo Finance` · `Telegram Bot API` · `Windows Task Scheduler` · `WSL` · `pytest` · `Ruff`

## What I learned

- Separating research from execution at the code level (not just by policy) makes a system much easier to reason about and to trust.
- Alert fatigue is a real failure mode. A correct alert nobody reads is worth zero.
- Label evidence precisely. Correlation shown in a message should never read as causation.
