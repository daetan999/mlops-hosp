# PoC Playbook: Validate GPU Serving Economics

The GPU-serving figures in this repository are not measured outcomes. This playbook defines how to test whether shared serving, dynamic batching, or multi-model concurrency improves utilization and unit cost without violating quality, latency, reliability, isolation, or operational requirements.

## Frame the Hypothesis

Begin with a conditional statement:

> If the model set, traffic profile, memory fit, and latency tolerance support shared serving, then batching or concurrency may improve fleet utilization and unit cost. A representative replay must establish the magnitude and trade-offs.

The hypothesis should be rejected or reshaped when the workload is already saturated, requires strict isolation, has an ultra-low latency floor, cannot share accelerator memory, or is dominated by CPU, storage, retrieval, or network cost.

## Agree the Success Criterion

Set thresholds only after capturing the baseline. A complete criterion names:

- representative model set and traffic replay;
- baseline and proposed serving configurations;
- output-quality or parity requirement;
- p50, p95, and p99 latency thresholds;
- target load and saturation method;
- minimum observation and soak period;
- reliability, recovery, security, and isolation requirements;
- cost per 1,000 inferences and its price source;
- pass, fail, and stop conditions.

No value from the reference diagrams should be copied into a customer criterion without evidence.

## Two-Week Evaluation Shape

| Phase | Days | Activity | Exit gate |
|---|---:|---|---|
| Frame | 1–2 | record fleet, utilization, unit cost, latency, traffic, model, and control baselines | approved baseline and test plan |
| Stand up | 3–5 | deploy the candidate serving pattern and validate output parity | models serve correctly and controls are observable |
| Load | 6–9 | replay representative traffic, increase load, and vary batching or concurrency | latency, quality, throughput, and saturation evidence captured |
| Soak | 10–12 | run the agreed sustained test and exercise failure recovery | stability and recovery evidence captured |
| Decision | 13–14 | compare results, sensitivity, and operating implications | advance, reshape, or stop recommendation |

The duration is an example planning envelope, not a promise. Data access, security review, model preparation, or environment constraints can change it.

## Measurement Set

| Area | Measures |
|---|---|
| Demand | request distribution, concurrency, tokens or payload size, peak pattern |
| Performance | throughput, queueing, p50/p95/p99 latency, saturation point |
| Accelerator | utilization distribution, memory use, batch size, model residency |
| Quality | parity checks and workload-specific evaluation metrics |
| Reliability | errors, restarts, recovery time, stability during soak |
| Economics | fleet size, price source, cost per 1,000 inferences, supporting platform cost |
| Controls | isolation, identity, audit, data handling, rollback, operational ownership |

## Failure Modes to Surface

- **Already-saturated fleet:** batching may add little benefit.
- **Latency below the batching window:** dedicated serving may remain necessary.
- **Model mix exceeds shared memory:** concurrency and consolidation may be limited.
- **Non-GPU bottleneck:** retrieval, CPU processing, network, storage, or egress may dominate.
- **Quality trade-off:** quantization or model changes may fail the approved evaluation set.
- **Control mismatch:** isolation, residency, or recovery requirements may invalidate the pattern.

Reporting a failed threshold is a valid PoC outcome. It prevents an unsupported architecture or business case from progressing.

## Decision Handoff

Pass the measured baseline, configuration versions, result distributions, price sources, exceptions, and sensitivity into the capacity and TCO reviews. Keep observed results separate from estimates and illustrative examples.

- [Value-engineering method](https://github.com/daetan999/technical_resume/blob/main/docs/value-engineering.md)
- [TCO worked example](https://github.com/daetan999/technical_resume/blob/main/docs/tco-worked-example.md)

[Back to GPU Serving Validation](../README.md#gpu-serving-validation)
