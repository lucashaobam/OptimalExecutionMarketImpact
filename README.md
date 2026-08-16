# Optimal Execution: Market Impact & Almgren-Chriss

A from-scratch derivation and implementation of the **Almgren-Chriss optimal execution framework** — the mean-variance model that underlies how institutional trading desks break large orders into smaller trades to minimize market impact and timing risk.

The project builds up from first principles: VWAP as an execution benchmark, a linear market impact model, the calculus-of-variations derivation of the optimal execution trajectory, and a full pre-trade → execution → post-trade workflow, with a survey of how production systems extend the base model.

## Why this project

Optimal execution sits at the intersection of stochastic calculus, convex optimization, and market microstructure — a natural fit for a mathematical finance / quant background. The goal here was to derive the Almgren-Chriss solution rigorously (not just quote the closed-form result) and then wire it into tools that resemble what an execution or trading-strategy desk would actually use: a schedule generator, a cost-risk frontier, a lambda-selection rule tied to alpha decay, and an implementation-shortfall calculator.

## What's inside

**1. VWAP Benchmark**
Volume-weighted average price as a fair-price benchmark, and a schedule generator that distributes an order proportionally to a U-shaped intraday volume profile.

**2. Market Impact Theory**
Temporary impact (rate-dependent, reverts after trading stops) vs. permanent impact (cumulative-volume-dependent, persists) under a linear impact model, with a worked example showing why permanent impact cost compounds as an order is sliced into more pieces.

**3. The Almgren-Chriss Framework**
The mean-variance objective — expected execution cost (temporary + permanent impact) plus a risk-aversion-weighted variance (timing risk) term — subject to starting with the full position and finishing flat by the deadline.

**4. Derivation of the Optimal Trajectory**
A full Euler-Lagrange derivation of the closed-form optimal trajectory:

$$x^*(t) = Q \cdot \frac{\sinh(\kappa(T-t))}{\sinh(\kappa T)}, \qquad \kappa = \sqrt{\lambda \sigma^2 / \eta}$$

with trajectories plotted across a range of risk-aversion values $\lambda$, from risk-neutral (VWAP-like) to highly risk-averse (front-loaded, near-immediate execution).

**5. Cost-Risk Frontier**
Expected cost and standard deviation traced out across a log-spaced grid of $\lambda$, showing the efficient frontier between execution cost and timing risk.

**6. Lambda Selection and Alpha Decay**
A rule for choosing $\lambda$ from an alpha signal's half-life ($\kappa \approx 1/h$), with worked recommendations across signal types — HFT, momentum, intraday alpha, mean reversion, value, index rebalancing.

**7. Implementation Shortfall**
The standard IS measure (execution VWAP vs. decision price) and how to use realized IS to recalibrate impact parameters $\gamma$, $\eta$ over time.

**8. Complete Execution Workflow**
An end-to-end simulation: pre-trade cost/lambda estimation → simulated execution with stochastic price evolution → post-trade shortfall report.

**9. Extensions and Research Directions**
A survey of how production systems go beyond the base model: transient impact (Obizhaeva-Wang, Gatheral-Schied), stochastic control / HJB formulations (Cartea-Jaimungal), optimal limit order posting (Guéant-Lehalle-Fernandez-Tapia), multi-asset execution, and reinforcement learning approaches — each tied to a specific limitation of the Almgren-Chriss model, with references.

## Key result

The Almgren-Chriss trade-off in one line: trading fast reduces exposure to price volatility (lower variance) but pays more in temporary impact (higher expected cost); trading slow does the opposite. The risk-aversion parameter $\lambda$ — and, via the half-life rule, the trader's underlying alpha signal — determines where on that frontier the optimal strategy sits.

## Tech stack

Python · NumPy · Pandas · Matplotlib · Seaborn

The notebook is a pure simulation — all price and volume data are synthetically generated, so it runs standalone with no external data dependency or API keys.

## Structure

```
optimal_execution_market_impact.ipynb   # Full notebook: theory, derivation, implementation, workflow
```

## References

Core derivation follows Almgren & Chriss (2000), *Optimal Execution of Portfolio Transactions*, Journal of Risk. Extensions section draws on Gatheral (2010), Obizhaeva & Wang (2013), Cartea & Jaimungal (2015), and Guéant, Lehalle & Fernandez-Tapia (2012); full citations are in the notebook.
