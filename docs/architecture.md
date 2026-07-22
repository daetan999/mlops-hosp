# Data Pipeline and Serving Architecture

> Part of the [MERIDIAN blueprint](../README.md). All integration names are vendor-class descriptions; no client identifiers, endpoints, or production configuration appear in this document.

> **Implementation boundary:** this document records the sanitized architecture and operating contract. The public repository includes representative configurations and interfaces, not the live services, connectors, datasets, credentials, or an end-to-end deployable environment.

## 1. Ingestion & Orchestration

The predictive layer depends entirely on data freshness, quality, and consistency. MERIDIAN ingests heterogeneous, high-velocity streams from global properties and centralizes them in a dual-layer feature store.

- **Reservations & rates** — pulled in real time from the property-management system's integration platform over REST/gRPC.
- **Building telemetry** — Building Management Systems transmit continuous utility consumption and equipment vibration over **BACnet**; edge gateway controllers pre-process signals (including FFT spectral features via NumPy) and push to **Apache Kafka**, partitioned by property for ordered, replayable consumption.
- **Batch context** — **Apache Airflow** schedules daily feature ETLs, localized weather refreshes (Cooling/Heating Degree Days against an 18 °C base), competitor-rate snapshots, and training workflows. Heavy window aggregations run as **Spark SQL** window functions over 90-day partitioned tables.

## 2. Feast Feature Store — Zero Offline/Online Skew

Feast standardizes every engineered feature so the exact same definition serves both training and live inference:

| Layer | Store | Purpose | SLA |
|---|---|---|---|
| Online | Redis Enterprise | Live inference feature retrieval | workload-defined latency and freshness targets |
| Offline | Snowflake | Point-in-time-correct training sets, backfills | High-throughput batch |

Feature definitions are designed for reuse across models. Reuse rate and delivery-time change must be measured in the operating environment.

## 3. Training & Model Governance

- Every training run is tracked in **MLflow**: hyperparameters, metrics (MAPE, F1, loss curves), and artifacts serialized to **ONNX**.
- Models progress through strict lifecycle stages — `Staging → Production` — only after passing automated integration test suites.
- Training executes as GPU jobs on **AWS EKS**, rebuilt from the Feast offline store for full reproducibility.

## 4. High-Throughput GPU Serving

The documented serving design uses **Triton Inference Server** on EKS with NVIDIA T4 instances:

- **Dynamic batching:** Triton can buffer requests to form parallel batches; the batching window and utilization effect require workload testing.
- **Multi-model concurrency:** compatible models can share GPU memory; consolidation depends on memory fit, traffic, isolation, and load behavior.
- **Latency budget:** the consuming workflow defines the p99 target, which must be tested across representative and peak load.

## 5. Drift Monitoring & Closed-Loop Retraining

The monitoring design assigns **Evidently AI** checks to every governed model:

| Drift type | Statistic | Threshold | Automated response |
|---|---|---|---|
| Feature drift | Wasserstein distance | > 0.15 | Warning surfaced to dashboards |
| Target drift | Population Stability Index | > 0.20 | Webhook fires Airflow `retrain_evaluate_promote` DAG |
| Concept drift | Page-Hinkley test | > 3σ | Pause automated pricing, page data science on-call |

The retraining DAG rebuilds the training set from the offline store, evaluates the challenger against the champion, and re-enters the same MLflow promotion gates. Promotion policy should retain the human approval required by the model's risk tier. Operating-effort change is a measurement, not a bundled result.

## 6. Failure-Mode Engineering

- **Smart-meter null drops:** sensor packet loss produces null telemetry; pipelines reject null sequences and substitute rolling-median imputations so LSTM autoencoders never crash on malformed windows.
- **Cold-start properties:** newly acquired or renovated properties lack history. Static attributes can identify a comparable group, and a model can bootstrap from approved pretrained weights. Time-to-onboard and model quality require evaluation.
