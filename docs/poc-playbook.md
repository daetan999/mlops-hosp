# PoC Playbook — Proving the Serving-Economics Win on Your Cluster

> The serving-economics numbers in this repo (utilization ~5% → 80%+, hosting −58%,
> 10× headroom at p99 < 150 ms) are the *outcome*. This playbook is how a sales
> engineer **proves them on a customer's own cluster** — time-boxed, falsifiable, and
> measured on the customer's traffic, not a vendor benchmark.

---

## The 60-second version

*"Your GPU bill is high because most of your fleet is idle — single-tenant pods sized
for peak, running at single-digit utilization. We put your models behind one
dynamically batched serving layer on a shared GPU pool. Batching packs your bursty
requests into dense GPU work, so utilization climbs, the fleet shrinks, and cost per
inference falls — while p99 stays under 150 ms because the batch window is capped at a
few milliseconds. Same traffic, a fraction of the hardware, and 10× the headroom. Give
me two weeks and your own traffic replay and I'll show it on your cluster, with a
per-inference cost number your CFO can bank."*

## Why a PoC, and why this shape

A PoC that can't fail can't sell. Three rules:

1. **Falsifiable** — a single pass/fail success criterion agreed *before* we start.
2. **Customer's inputs** — their models, their traffic replay, their instance types.
   The resulting number is theirs to defend internally, not mine to assert.
3. **Time-boxed** — two weeks. If it needs a quarter, it isn't a PoC, it's a migration.

## The success criterion (agreed up front)

> On the customer's own traffic replay, serve their production model set on a shared,
> dynamically batched GPU pool at **p99 < 150 ms** while sustaining **10× current peak
> QPS** over a **72-hour soak**, at a **per-1K-inference cost ≤ 50% of today's**.

Every threshold is measured. If one misses, we report it and diagnose — the PoC has
done its job either way.

## The two-week plan

| Phase | Days | Activity | Exit gate |
|---|---|---|---|
| **0 · Frame** | 1–2 | Capture baseline: current GPU count, utilization, per-inference cost, p99, traffic profile | Signed-off baseline + success criterion |
| **1 · Stand up** | 3–5 | Deploy Triton (or equiv.) with dynamic batching + multi-model concurrency on a shared pool; load customer models | Models serving correctly, parity on outputs |
| **2 · Load** | 6–9 | Replay real traffic at 1× then 10× peak; tune batch window / instance mix | p99 and throughput targets met at 10× |
| **3 · Soak** | 10–12 | 72-hour sustained run; capture utilization, cost, latency histograms | Stability + cost target held over soak |
| **4 · Business case** | 13–14 | Turn measured deltas into the TCO model and the CFO one-liner | Written business case + expansion path |

## What we measure (and put on one dashboard)

- **GPU utilization** — steady-state and under load, before vs. after.
- **Cost per 1K inferences** — the number that survives scrutiny.
- **p99 / p50 latency** — histograms, not averages, under 1× and 10× load.
- **Throughput headroom** — max QPS before p99 breaches the SLA.
- **Fleet size** — GPU count for equivalent served load.

## Failure modes I'll surface honestly

A credible SE names where the pitch *doesn't* apply:

- **Traffic isn't burstable / already saturated** — if the fleet is genuinely busy,
  batching yields little; the win is smaller and I'll say so.
- **Latency floor below the batch window** — ultra-low-latency paths (< 20 ms) may not
  tolerate batching; those stay on a dedicated lane.
- **Model mix too heavy for shared memory** — very large models limit concurrency; the
  consolidation ratio drops.
- **Non-GPU bottlenecks** — if the real cost is data egress or CPU pre/post-processing,
  GPU consolidation won't move the bill, and the discovery should have caught it.

Calling these out *before* the customer finds them is what earns the technical win.

## From PoC to signed business case

The measured deltas feed straight into the portfolio's value-engineering artifacts:

- **[Value-engineering playbook](https://github.com/daetan999/technical_resume/blob/main/docs/value-engineering.md)** — the full discovery→TCO→close motion.
- **[TCO worked example](https://github.com/daetan999/technical_resume/blob/main/docs/tco-worked-example.md)** — a fictional prospect taken end-to-end, with the cost waterfall.

The serving economics that make this PoC land are documented in the
[README's GPU Serving Economics section](../README.md#gpu-serving-economics--the-tco-story).
