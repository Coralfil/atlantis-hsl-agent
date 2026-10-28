# Entry Points

The submission is a single Kaggle competition kernel (`coralfil/hexactinellid-spicule-lattice-arc3`), executed top-to-bottom by the Kaggle runner. There is no separate train/predict split: the solution performs **no training at inference time** (the neural component is a previously fine-tuned, AWQ-quantized 14B model; a pure deterministic mode runs with no model attached).

## 1. `prepare_data` — stage 1 of the kernel

- Reads competition environments from `COMPETITION_DATA_DIR` (see `SETTINGS.json`).
- Extracts static topological anchors per environment (background masks, boundary walls, key–door configurations) into the lattice's annotation set Ω.
- No external data is used (competition rules; fully offline).

## 2. Inference pipeline — stages 2–5 of the kernel (the "predict" entry point)

Executed per environment step:

1. **Visual-Topological Decomposition** — 4-connectivity connected-component labeling (< 2 ms): entities, bounding boxes, centroids.
2. **LLM Hypothesis Generation** — 14B AWQ-INT4 (Atlantis-Pyramid 14B Glyph) via vLLM (`vllm serve ${MODEL_DIR} --max-model-len 8192`), 64 parallel beams propose candidate action sequences / macros.
3. **6-Ray Epistemic Verification** — each candidate is projected onto the corroborate/contradict/promote/demote/fuse/annotate rays; the Polyphonic Cadence Barrier rejects any plan predicted to collide or cycle **before** environment dispatch (a strict contraction mapping — converges, no cyclic entrapment).
4. **Symbolic Fallback** — BFS frontier explorer reaches unvisited cells/targets when neural hypotheses exhaust the latency budget; A* heuristics for gravity-routing.
5. **Action Dispatch + Logging** — accepted action is dispatched; trajectory autopsies, collision heatmaps, and harmonic-integral decay curves are appended to `LOG_DIR` (Failure-Replay Digests).

## Reproducing results

Run the kernel end-to-end in the competition environment (offline). Deterministic components (spatial primitives, planners, verifier) are seed-fixed; LLM outputs vary within the beam budget, and the cadence barrier (not the sampler) is the acceptance authority, so results are reproducible within the documented margins.
