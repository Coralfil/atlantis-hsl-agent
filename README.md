# ARC Prize 2026 (ARC-AGI-3) — Hexactinellid Spicule Lattice Agent

**Team:** Coralfil AI Research Group (Roland Gordon) · **Contact:** research@coralfil.com
**Competition:** ARC Prize 2026 — ARC-AGI-3 (Kaggle, code competition, fully offline)
**Solution:** Hybrid neuro-symbolic agent — 14B AWQ-INT4 LLM hypothesis generation (Atlantis-Pyramid 14B Glyph, vLLM) verified by a deterministic 6-ray epistemic lattice ("Hexactinellid") with a Polyphonic Cadence Barrier, plus BFS/A* symbolic fallbacks.

## Hardware

| Phase | Hardware |
|---|---|
| LLM development / evaluation (pre-competition) | Lambda Labs instance: 1× NVIDIA A10 (24 GB VRAM), 30 vCPU, ~200 GB RAM |
| Competition inference (this submission) | 1× NVIDIA GPU (Kaggle RTX Pro 6000 Blackwell 96 GB or P100 16 GB), 4 vCPUs, 30 GB system RAM |

## OS / Platform

- Pre-competition: Ubuntu 22.04 (Lambda box), Python 3.12 (deadsnakes venv)
- Competition: Kaggle offline kernel image (Ubuntu, Python 3.10+), **no internet access**; all assets must be attached as Kaggle Model inputs

## Third-party software

See `requirements.txt` for exact pins. Core stack: PyTorch, vLLM (AWQ serving, 64-beam parallel hypothesis generation), Transformers, AutoAWQ (quantization), numpy/scipy (connected-component labeling, A*).

## How to run (competition inference)

This is a Kaggle Code competition: the submission is the competition kernel `coralfil/hexactinellid-spicule-lattice-arc3` (private), which runs end-to-end offline. The attached model input provides the quantized 14B AWQ-INT4 weights (`coralfil/atlantis-pyramid-v2/Transformers/14b-awq-int4/2`); the notebook executes the full pipeline — visual-topological decomposition → LLM hypothesis generation → 6-ray cadence verification → action dispatch. A pure deterministic mode (empty `model_sources`) runs with no attached model. See `entry_points.md`.

## Side effects

- The notebook writes only to `/kaggle/working/` (logs, Failure-Replay Digests). No input data is modified.
- No network calls; the pipeline fails closed if a required attachment is missing.

## Key assumptions

- The attached model variation is present at its `/kaggle/input` path (see `SETTINGS.json`).
- Output contract: one action per environment step, within the competition's per-step time budget.
- Deterministic seeds are fixed for the symbolic components; LLM sampling uses bounded beams (64) with the cadence barrier as the sole arbiter of plan acceptance.
