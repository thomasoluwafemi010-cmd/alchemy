# ISC 1.0 — Intelligent Strategies Connoisseur Trader [MSNRUZ]

TradingView tools built from the **ISC 1.0** ebook (`ISC MSNR_Optimized.pdf`, 51 pages, msnr.uz).

The book teaches a confluence trading system — **MSNR × SMC × LiT × ICT Kill Zones × Quarterly Theory (QT) × SMT** — mostly on XAUUSD (Gold) M1–M15. This repo turns that system into code:

| File | What it is |
|---|---|
| [`pine/ISC_Confluence_Indicator.pine`](pine/ISC_Confluence_Indicator.pine) | Chart indicator: full ISC markup + confluence entry signals + alerts |
| [`pine/ISC_Strategy.pine`](pine/ISC_Strategy.pine) | Backtestable strategy: same entry engine, RR-based exits, risk % sizing |

---

## Installation (TradingView)

1. Open any chart (e.g. **XAUUSD, 15m or 1m**).
2. Press **Pine Editor** (bottom panel) → **Open** → **New blank indicator**.
3. Delete the template, paste the full contents of one `.pine` file.
4. Click **Save**, name it, then **Add to chart**.
5. For SMT divergence, set the correlated symbol in settings:
   - XAUUSD → `XAGUSD` (default)
   - EURUSD → `GBPUSD` · AUDUSD → `NZDUSD` · EURJPY → `GBPJPY` · USDCAD → `USDCHF` (book p. 46)

---

## What each module does (mapped to the book)

### 1) SMC — Market Structure (pp. 11–12)
- Labels swings **HH / HL / LH / LL** from pivot points.
- **BOS** = break of structure in trend direction (continuation).
- **CHOCH** = break against the trend (change of character → reversal signal).
- Internal trend state drives the direction filter for signals.

### 2) MSNR — Key Zones (pp. 3–5)
- Draws **support/resistance zones** from higher-order pivots (separate swing length).
- Zone state machine implements the book's flips:
  - **RBS** — Resistance Becomes Support (zone broken upward, recolored bullish)
  - **SBR** — Support Becomes Resistance (zone broken downward, recolored bearish)
- Zones expire after N bars and stay on chart (grayed) for context.

### 3) LiT — Liquidity & Inducement (pp. 13–16)
- Registers swing highs/lows as resting liquidity.
- **LS (Liquidity Sweep)**: wick runs beyond a swing level but price **closes back inside** — stop-hunt / inducement, exactly the trap the book warns about.
- The indicator tracks *sweep age*: per p. 14, *"Every POI must be preceded by inducement"* — so a signal only fires when a sweep happened within the last N bars.

### 4) ICT — Kill Zones (pp. 26–28)
- **Asia Range** session (default 01:00–05:00 London / UTC+1, as in the book): box drawn around the session high/low, with dotted liquidity lines extending right.
- **Asia LS**: when London/NY sweeps the Asia high or low and reverses (liquidity grab), it's labeled and feeds the inducement tracker.
- London (07:00–10:00) and New York (13:00–16:00) kill zones shaded on chart.
- All sessions and timezone are configurable.

### 5) QT — Quarterly Theory / AMDX (pp. 35–38)
- The book's **90-minute micro cycle**: the day repeats **A**ccumulation → **M**anipulation → **D**istribution → **X** (continue/reverse) every 90 minutes, and each 6-hour session maps to one phase (Asia=A, London=M, NY AM=D, NY PM=X).
- Phase letters print at each cycle change; current phase + session shown in the status table.
- Optional filter: only take signals in **M** or **D** phases (the book's trade windows).

### 6) SMT — Divergence (pp. 46–49)
- Compares pivots on the chart symbol vs a correlated symbol (e.g. Gold vs Silver).
- **Bullish SMT**: local symbol makes a lower low while the correlated symbol makes a higher low → *"whoever does not take liquidity owns the direction"* → direction is on the local symbol.
- Bias shown in the status table; optional filter for signals.

### 7) Confluence entry signal
**Long** = sell-side liquidity swept (inducement cleared) **→** bullish CHOCH/BOS **→** filters aligned (Kill Zone / AMDX / SMT as enabled).
**Short** = mirror logic.

Alerts included for: Long/Short entries, CHOCH both directions, liquidity sweeps, SMT divergences.

---

## Strategy version (backtesting)

Same engine, plus:

- **Entry**: on the close of the signal bar (`process_orders_on_close`).
- **Stop loss**: beyond the sweep wick + an ATR buffer (default 0.25 × ATR(14)) — the book's tight SL style (examples: 5–15 pips on gold).
- **Take profit**: RR multiple of risk (default 3.0R — book trades bank 1.5R–3.2R).
- **Position size**: fixed % risk of equity (default 1%).
- **Optional**: close early on opposite CHOCH; direction filter (Long/Short/Both); date range.
- Commission 0.02% + 2 ticks slippage are preconfigured so results aren't fantasy-level.

Suggested first test: XAUUSD 1m or 5m, defaults on, then experiment with the Kill-Zone and AMDX filters.

---

## Honest notes & limitations

- **Pivots confirm with delay** (by design). A swing is only recognized `swingLen` bars after it forms — same as a human drawing structure. No repaint tricks are used.
- **Inducement is approximated** as a liquidity sweep of a registered swing. The book's full POI/OB methodology (order blocks, quasimodo, OCL) is partly discretionary; sweeps + CHOCH capture its entry sequence.
- **SMT timing is approximate**: the correlated pair's last two pivots are compared with the local pivot event (bars may not align perfectly).
- Session/KZ/AMDX features need an **intraday chart** (M1–H1).
- The strategy is a faithful-but-simplified encoding for research/education — **not financial advice**, and past (backtested) performance never guarantees future results.

---

## Credits

- Trading concepts: **ISC 1.0 — Intelligent Strategies Connoisseur Trader**, MSNRUZ (msnr.uz).
- Implementation: Pine Script v6, built from the ebook in this repo.
