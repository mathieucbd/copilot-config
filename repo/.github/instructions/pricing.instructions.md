---
name: 'Pricing correctness'
description: 'Use when writing, changing, reviewing or validating pricing code, greeks, market-data inputs, dividend or borrow models, or numerical pricing engines.'
applyTo: '**/*.py'
---
<!-- WORK: narrow applyTo to pricing code paths -->
<!-- Lives in the desk repo at .github/instructions/pricing.instructions.md (docs/SOURCES.md 1.7, 7.2).
     Ported from correctness-pricing.md. Trailing comments are rule IDs (README mapping). -->

# Pricing correctness

## Checks and tolerances
- Every pricing number ships with the check that supports it. <!-- PR-1 -->
- Tolerances below are defaults. My validation standards and the bank's override them: <!-- WORK: bank validation standards and tolerances -->. <!-- PR-2 -->

## Changing existing pricing code
- Before changing existing pricing code, freeze its current outputs (prices and greeks) on a fixed input set as tests. <!-- PR-3 -->
- A changed output is a finding: report old vs new on the same inputs. Never edit expected values without my explicit sign-off. <!-- PR-3 -->

## Conventions and dates
- State and assert conventions in code: day count, holiday calendar, settlement lag, compounding, vol type and time basis (ACT/365F vs Bus/252), mark side (mid/bid/offer). <!-- PR-4 -->
- Raise on a missing convention value. Never fall back to a library default. <!-- PR-4 -->
- Date every cash flow explicitly: trade, ex, pay, settlement. <!-- PR-5 -->

## Market data
- Tag every market-data input with source, snap time and as-of date. <!-- PR-6 -->
- Assert that all inputs share the valuation date and my snap window. Print mismatches. <!-- PR-6 -->

## Dividends and borrow
- State the dividend model (cash vs yield, escrowed vs piecewise, ex vs pay dates) and the borrow source per underlying. <!-- PR-7 -->
- Print the model forward at each expiry next to the market forward. <!-- PR-7 -->
- Dividends implied from parity already contain borrow. Never add borrow on top. <!-- PR-8 -->

## Greeks
- Label every greek with units and scaling (delta in shares or % notional, vega per vol point, theta per calendar or business day, rho per bp), bump size and scheme. <!-- PR-9 -->
- Match the consuming system's convention and say which convention it is. <!-- PR-9 -->

## Output validation
- Put-call parity holds across strikes per expiry: residual below 1e-10 relative in closed form, within 3 standard errors in Monte Carlo. <!-- PR-10 -->
- Prices lie within static-arbitrage bounds. <!-- PR-10 -->
- Bumped greeks match analytic greeks on a vanilla and stay stable across 3 bump sizes. <!-- PR-10 -->

## Numerical engines (building or changing one)
- First reproduce a closed-form case: Black-Scholes vanilla, zero-vol limit, zero-rate limit. <!-- PR-11 -->
- Show convergence over 3 refinement levels, with the observed order. <!-- PR-11 -->
- Report Monte Carlo with number of paths, seed and standard error. Use common random numbers for bumped greeks. <!-- PR-11 -->
- Trees use odd N or Leisen-Reimer. Crank-Nicolson gets Rannacher start steps. <!-- PR-11 -->
- Check gamma near strikes, barriers and exercise boundaries. <!-- PR-11 -->

## P&L explain
- When a pricer or greek changes and P&L history exists, run a P&L explain: greek-predicted vs full revaluation, reporting the unexplained residual. <!-- PR-12 -->
