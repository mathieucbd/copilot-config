---
name: 'Pricing correctness'
description: 'Use when writing, changing, reviewing or validating pricing code, greeks, market-data inputs, dividend or borrow models, or numerical pricing engines.'
applyTo: '**/*.py'
---
<!-- WORK: narrow applyTo to pricing code paths -->

## Correctness: pricing
*Pricing bugs return plausible numbers, not errors. Every number ships with
the check that supports it. Tolerances below are defaults; mine or the
bank's validation standards override them.*
- Before changing existing pricing code, freeze its current outputs (prices
  and greeks) on a fixed input set as tests. A changed output is a finding:
  report old vs new on the same inputs. Never edit expected values without
  my explicit sign-off.
- State and assert conventions in code: day count, holiday calendar,
  settlement lag, compounding, vol type and time basis (ACT/365F vs Bus/252),
  mark side (mid/bid/offer). Raise on a missing value; never fall back to a
  library default. Date every cash flow explicitly (trade, ex, pay,
  settlement).
- Tag every market-data input with source, snap time and as-of date. Assert
  all inputs share the valuation date and my snap window; print mismatches.
- State the dividend model (cash vs yield, escrowed vs piecewise, ex vs pay
  dates) and borrow source per underlying. Print the model forward at each
  expiry next to the market forward. Dividends implied from parity already
  contain borrow: never add borrow on top.
- Label every greek with units and scaling (delta in shares or % notional,
  vega per vol point, theta per calendar or business day, rho per bp), bump
  size and scheme. Match the consuming system's convention and say which.
- Validate outputs: put-call parity across strikes per expiry (default
  residual below 1e-10 relative in closed form, 3 SE in MC); price within
  static-arbitrage bounds; bumped greeks match analytic on a vanilla and
  stay stable across 3 bump sizes.
- When building or changing a numerical engine: first reproduce a
  closed-form case (Black-Scholes vanilla, zero-vol, zero-rate limit); show
  convergence over 3 refinement levels with observed order; report MC with
  paths, seed and standard error, using common random numbers for bumped
  greeks; trees use odd N or Leisen-Reimer; Crank-Nicolson gets Rannacher
  start steps, and gamma is checked near strikes, barriers and exercise
  boundaries.
- When a pricer or greek changes and P&L history exists, run a P&L explain:
  greek-predicted vs full revaluation, reporting the unexplained residual.
