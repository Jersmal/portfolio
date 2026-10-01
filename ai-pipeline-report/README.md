# Distributed Offensive-Security Code Pipeline (MITRE ATT&CK)

A final-project research pipeline studying how fast large language models can be
pushed to mass-produce offensive-security code — and why detection has to keep up.
Built on [Modal](https://modal.com/) with parallel H100 GPUs.

> This is the **write-up only**. The pipeline source is kept in a private repo and
> is available on request.

## What it does (7 stages)

1. **Ingest** live MITRE ATT&CK data (never hard-coded) + three seed datasets.
2. **Baseline generation** — one sample per technique, across independent GPU workers.
3. **Variation generation** — 10× H100 workers, multiple strategies per technique
   (obfuscation, fileless, WMI, scheduled-task, registry, API-based, and more).
4. **Selection** — ~500 samples, hash-deduplicated, balanced for coverage.
5. **Explanation** — concise, constrained inline comments on each sample.
6. **Export** — several formats for downstream use.
7. **Statistics** — coverage, language and strategy distribution, length metrics.

## Why it's interesting (engineering)

- **Parallelization win:** a sequential run of ~2 hours (which once ran 8 hours and
  burned ~$175 before crashing at iteration 87/200) was cut to minutes by batching
  across independent GPU workers.
- **Model:** `Qwen2.5-Coder-32B-Instruct` — instruct tuning made output format
  controllable through prompt engineering instead of brittle post-processing.

## Read the full report

[`PROJECT_REPORT.md`](./PROJECT_REPORT.md)

**Dataset (Hugging Face):** `pjlongCSE132/CSE132-AI-Pipeline-Code`

## Skills

Distributed/parallel GPU orchestration (Modal, H100) · prompt engineering · LLM code
generation · data-pipeline design · security-domain modeling (MITRE ATT&CK).
