# PE / Private Credit Fund — Waterfall Model

A Python implementation of a four-tier private equity waterfall model covering both **European (whole-fund)** and **American (deal-by-deal)** structures, with compounded preferred return, GP catch-up, clawback reconciliation, and fund performance metrics.

Built to demonstrate core **private credit fund accounting mechanics** as practised at fund administrators including Alter Domus, Apex Group, State Street, and Northern Trust.

---

## What It Models

| Component | Detail |
|---|---|
| Capital calls | 5 tranches across investment period; LP account tracking |
| Preferred return | 8% p.a. compounded per tranche from call date to exit |
| European waterfall | Whole-fund; 4-tier; GP carry only after all LP capital + hurdle returned |
| American waterfall | Deal-by-deal; GP earns carry per exit; clawback at fund end |
| GP catch-up | 100% catch-up; formula: `carry% / LP_carry% × preferred_return` |
| Clawback | American GP carry minus European entitlement |
| Performance | TVPI, DPI, RVPI, Fund IRR (Newton-Raphson) |

---

## Waterfall Tiers

```
Tier 1 — Return of LP Capital         (100% → LP)
Tier 2 — Preferred Return (8% p.a.)   (100% → LP)
Tier 3 — GP Catch-up (100%)           (100% → GP until GP has 20% of profits)
Tier 4 — Carried Interest Split        (80% LP  /  20% GP)
```

**Key formula — GP catch-up:**
```
Catch-up = min(Remaining, carry% / (1 − carry%) × Preferred_Return)
         = min(Remaining, 25% × Preferred_Return)
```
This ensures GP ends up with **exactly 20% of total fund profits** — provably correct.

---

## Illustrative Fund (Alter Domus Private Credit Fund I)

| Parameter | Value |
|---|---|
| Fund Size | £100,000,000 |
| LP / GP Split | 98% / 2% |
| Capital Called | 95% (5 tranches) |
| Hurdle Rate | 8% p.a. compounded |
| Carried Interest | 20% GP |
| Fund Life | 10 years |
| Portfolio | 5 deals (4 realised, 1 held) |

---

## Results

```
TVPI  2.17x   DPI  1.87x   RVPI  0.30x   Fund IRR  ≈16.6%

European Waterfall:
  LP receives  £180,220,000
  GP receives   £21,780,000  (exactly 20.0% of total profits ✓)

American Waterfall:
  GP receives   £25,000,000  (deal-by-deal)
  Clawback       £3,220,000  (GP returns to LPs at fund end)
```

---

## Usage

```bash
python pe_waterfall_model.py
```

To model a different fund, edit the parameters at the top of the file:

```python
FUND_SIZE     = 100_000_000
HURDLE_RATE   = 0.08
CARRY_PCT     = 0.20
CAPITAL_CALLS = [(1, 0.25), (2, 0.25), (3, 0.20), (4, 0.20), (5, 0.05)]
DEALS         = [("Deal Name", "Sector", invest_yr, invested, exit_yr, proceeds, status), ...]
```

No external libraries required — pure Python standard library.

---

## Key Concepts Demonstrated

- **European vs American waterfall** — timing of GP carry and LP protection mechanics
- **Clawback provision** — why American structures require GP escrow
- **Preferred return compounding** — per-tranche calculation from call date
- **TVPI = DPI + RVPI** — fund performance identity
- **J-curve** — IRR penalises slow distributions; early fees + no exits create negative IRR
- **IFRS 13 Level 3** — private equity NAV based on unobservable inputs (comparable multiples)

---

## Related Projects

- [NIFTY50 Market Risk Engine](https://github.com/vignani007/Nifty-risk) — VaR, GARCH-t, Basel III backtesting
- Fixed Income Valuation & Immunisation — bond pricing, duration matching, ALM

---

*Illustrative model for educational and interview purposes. Not financial advice.*
