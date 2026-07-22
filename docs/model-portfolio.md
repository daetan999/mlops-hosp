# Model Portfolio: Specifications by Product View

> Part of the [MERIDIAN blueprint](../README.md).

## View 1 · Portfolio Financials (Revenue & Yield)

### Demand & ADR Forecasting — Temporal Fusion Transformer

- **Architecture:** PyTorch Forecasting TFT with multi-head self-attention (4 heads, d_model = 64), learning long-horizon seasonal structure while filtering daily noise.
- **Quantile loss:** outputs predictive intervals (τ ∈ {0.1, 0.5, 0.9}) rather than point estimates, so revenue managers see best/median/worst-case bands.
- **Covariates:** static attributes (brand tier, market segment), historical series (RevPAR, cancellation rates), and known-future variables (holidays, scheduled conventions).
- **Evaluation:** track MAPE and interval calibration by segment against an approved baseline; no result is bundled in the public blueprint.

### Dynamic Pricing — Deep Q-Network RL Agent

- **Algorithm:** PyTorch DQN, ε-greedy exploration in offline historical simulation.
- **State:** OTB inventory velocity, Days-To-Arrival, hourly competitor delta, TFT demand quantiles.
- **Action:** continuous rate moves inside a bounded action space (−15% … +35% of rack rate) to avert competitor-pricing feedback spirals.
- **Reward:** `R = Σ (Occupancy × ADR) − Penalty(overprice-vacancy)` over a rolling 30-day window.
- **Serving target:** define p99 latency from the consuming workflow and validate it under representative load.

## View 2 · Portfolio Management (Capital Allocation)

- **Peer clustering — t-SNE + K-Means** across cap rates, occupancy, geographic risk, and historical yield: precise, context-aware benchmarking groups.
- **Feasibility analytics — Random Forest regressors** predicting long-term IRR and yield for proposed acquisitions, trained on local hotel metrics, infrastructure pipelines, and tourism patterns.

## View 3 · Sustainability (ESG)

### Utility Leak & Anomaly Detection — LSTM Autoencoder

- **Encoder:** 3 stacked bidirectional LSTM layers compress 24-hour utility sequences (96 × 15-minute readings) into a low-dimensional latent space; a 3-layer unidirectional decoder reconstructs the signal.
- **Anomaly logic:** MSE reconstruction loss above a dynamic rolling threshold flags abnormal consumption — e.g. continuous overnight water flow indicating a hidden pipe leak — within 30 minutes.

### HVAC Thermal Load — SARIMAX + LSTM

Forecasts thermodynamic load from weather and predicted occupancy; pre-cools high-thermal-mass zones during off-peak tariffs and curtails load during peak windows.

## View 4 · Property Operations

### Predictive Maintenance — Weibull Survival Analysis

- Survival models combined with random-forest classifiers over IoT vibration/current telemetry predict Mean Time To Failure for chillers and boilers.
- **Hazard function:** `λ(t) = (β/η) · (t/η)^(β−1)` — β (shape) captures wear-out rate, η (scale) the characteristic life.
- Produces a calibrated failure-risk window for maintenance review; alert precision, lead time, intervention cost, and downtime value require validation.

### Guest Feedback & Ticket Routing — Fine-Tuned BERT

Parses reviews, booking comments, and internal logs; classifies sentiment and urgency, then routes work orders to the appropriate response group. Evaluation should compare routing accuracy, escalation rate, and resolution-time distribution with the approved baseline.
