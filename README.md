# MERIDIAN — Enterprise MLOps Platform Blueprint

[![Python](https://img.shields.io/badge/python-3.11%2B-blue)](#)
[![PyTorch](https://img.shields.io/badge/PyTorch-2.x-EE4C2C)](#)
[![MLflow](https://img.shields.io/badge/MLflow-registry-0194E2)](#)
[![Triton](https://img.shields.io/badge/Triton-inference-76B900)](#)
[![Kubernetes](https://img.shields.io/badge/EKS-GPU%20serving-326CE5)](#)

> Part of the [technical project portfolio](https://github.com/daetan999/technical_resume). Supporting material: [value-engineering playbook](https://github.com/daetan999/technical_resume/blob/main/docs/value-engineering.md).

## Overview

MERIDIAN is a sanitized architecture blueprint for operating multiple machine-learning workloads on a shared data, feature, training, serving, and monitoring platform.

The project covers eight model families across demand forecasting, constrained reinforcement-learning pricing, anomaly detection, predictive maintenance, asset clustering, feasibility analysis, and NLP ticket routing. The public repository retains representative interfaces, configuration shapes, model skeletons, deployment manifests, diagrams, and operational controls while excluding proprietary logic and production data.

## Public-Portfolio Boundary

- Client names, property identifiers, datasets, endpoints, credentials, and internal codenames are removed or replaced.
- Code is illustrative where production implementation cannot be published.
- Financial impact figures are aggregated or modeled and are labelled as such.
- Architecture and control patterns are retained to show the design decisions behind the system.

## Platform Architecture

![MERIDIAN platform architecture](docs/assets/platform-architecture.svg)

The platform is organized around a common operating path:

1. Kafka and Airflow ingest operational, telemetry, and external signals.
2. Feast provides Redis-backed online features and Snowflake point-in-time training data.
3. PyTorch training jobs register evaluated artifacts through MLflow.
4. ONNX models are promoted to NVIDIA Triton on EKS.
5. Evidently monitors feature, target, and concept drift and routes each class to a different response.

## Core Components

| Layer | Implementation | Purpose |
|---|---|---|
| Streaming and orchestration | Kafka · Airflow | Event movement, batch processing, and retraining workflows |
| Feature management | Feast · Redis · Snowflake | Shared online/offline feature contract and training-serving consistency |
| Training and registry | PyTorch · MLflow · ONNX | Reproducible training, evaluation gates, and portable artifacts |
| Model serving | NVIDIA Triton · EKS | Dynamic batching, multi-model concurrency, and horizontal scaling |
| Monitoring | Evidently | Drift detection, automated retraining triggers, and halt conditions |

## MLOps Lifecycle

![Continuous MLOps lifecycle](docs/assets/mlops-lifecycle.svg)

- Unit and integration checks precede registry submission.
- Registry promotion separates staging and production-ready artifacts.
- EKS rollouts use staged deployment rather than immediate fleet replacement.
- Drift tiers distinguish warnings, automated retraining, and halt-and-page events.
- Challengers re-enter the same evaluation gates before promotion.

## GPU Serving Economics

The serving design consolidates a linear single-tenant CPU pattern onto shared, dynamically batched GPU infrastructure.

![GPU serving economics](docs/assets/gpu-finops-tco.svg)

| Measure | Before | Shared GPU design |
|---|---|---|
| Deployment pattern | One CPU instance per property model | 100+ model deployments on a shared T4 cluster |
| Compute utilization | Approximately 5% | More than 80% in the modeled serving scenario |
| Peak handling | Per-instance overprovisioning | Up to 10× traffic headroom in load testing assumptions |
| Inference service level | Not standardized | p99 below 150 ms target |
| Hosting economics | Baseline | Approximately 58% lower modeled hosting cost |

The associated [`docs/poc-playbook.md`](docs/poc-playbook.md) defines how to test utilization, throughput, latency, reliability, and cost per inference against an agreed baseline.

## Model Portfolio

| Product area | Representative models | Operational use |
|---|---|---|
| Portfolio financials | Temporal Fusion Transformer · bounded-action DQN | Demand forecasting and controlled rate recommendations |
| Portfolio management | t-SNE · K-Means · Random Forest | Peer grouping and feasibility analysis |
| Sustainability | LSTM autoencoders · SARIMAX | Meter anomalies and thermal-load forecasting |
| Property operations | Weibull survival · Random Forest · BERT | Failure risk and work-order routing |

![Technical-to-financial transmission](docs/assets/value-transmission.svg)

The value-transmission model links technical measures such as forecast error, downtime, inference utilization, and response time to corresponding revenue, operating-cost, or capital-expenditure hypotheses. Detailed assumptions are documented in [`docs/business-impact.md`](docs/business-impact.md).

## Reliability Controls

| Control | Implementation |
|---|---|
| Training-serving consistency | Shared Feast definitions and point-in-time offline retrieval |
| Model promotion | Evaluation and registry gates before deployment |
| Drift response | Warning, retrain, and halt tiers with separate thresholds |
| Cold-start handling | Cluster-based transfer from comparable properties |
| Telemetry gaps | Input validation and rolling-median substitution |
| Serving acceptance | Explicit p99 latency, utilization, throughput, and cost criteria |

## Repository Map

```text
docs/                  Architecture, model portfolio, business-impact assumptions
pipelines/airflow/     Feature-engineering and retraining DAG shapes
feature_store/         Feast entity and feature-view definitions
models/                Representative model skeletons and configuration
serving/               Triton batching configuration and EKS manifest
monitoring/            Drift thresholds and monitor service structure
```

## Deep-Dive Documentation

- [`docs/architecture.md`](docs/architecture.md) — data, feature, training, and serving architecture
- [`docs/model-portfolio.md`](docs/model-portfolio.md) — model specifications and mathematics
- [`docs/business-impact.md`](docs/business-impact.md) — assumptions linking model metrics to business outcomes
- [`docs/poc-playbook.md`](docs/poc-playbook.md) — measurable GPU-serving evaluation plan

## License

Released under the MIT License.
