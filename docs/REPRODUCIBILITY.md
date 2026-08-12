# Reproducibility guide

This document separates the paper's principal Qwen3-VL experiment from the task-specific ViT-Transformer baseline retained in `src/`.

## Reported evaluation set

- Benchmark: CROHME 2019
- Valid test expressions: 1,198
- Principal training data: approximately 8.9K CROHME expressions
- Ablation: CROHME plus 2,000 manually written, formula-guided expressions
- Principal metrics: strict exact match and corpus BLEU-4

The manuscript reports 1,198 valid examples after excluding one unusable record from the nominal 1,199-example test set. Use the same filtered set when reproducing the published denominators.

## Qwen3-VL configuration

The best reported model starts from a 4-bit Qwen3-VL-4B checkpoint and applies rank-16 LoRA adapters to the vision, language, attention, and MLP modules.

The archived training setup uses:

| Setting | Value |
|:--|:--|
| Per-device batch size | 2 |
| Gradient accumulation | 4 |
| Effective batch size | 8 |
| Epochs | 1 |
| Learning rate | `2e-4` |
| Warmup steps | 5 |
| Optimizer | 8-bit AdamW |
| Weight decay | 0.01 |
| Scheduler | Linear |
| Seed | 3407 |
| Evaluation/save interval | 20 steps |

Each training item is an instruction-formatted multimodal conversation: the user message contains the handwritten-expression image and a fixed request for its structural graph; the assistant message contains the linearized Symbol Label Graph target.

## Expected principal result

| Configuration | Exact match | BLEU-4 |
|:--|--:|--:|
| Qwen3-VL, CROHME only | 43.74% (524/1,198) | 77.67 |
| Qwen3-VL, CROHME + 2K | 42.32% (507/1,198) | 75.46 |

The additional data is an ablation and should not be silently merged into the principal run. Its lower result is evidence of domain mismatch, not a failed attempt to maximize the headline score.

## Baseline workflow

Install the core dependencies and run:

```bash
python src/train.py
python src/evaluate.py
```

Paths for `data/` and `models/` are resolved relative to the repository in `src/config.py`. The baseline checkpoint is not stored in Git because of its size; see `models/README.md`.

## Evaluation cautions

- Exact match is sequence-level and sensitive to every symbol and spatial-relation token.
- BLEU-4 measures token overlap; it is not the official CROHME graph metric.
- The IM2TEX and Qwen2.5-VL rows in the paper are local implementations, not copied leaderboard values.
- The study reports one principal seed and does not claim statistical significance or state-of-the-art performance.
- Dataset access and redistribution remain subject to CROHME's original academic/research terms.

## Artifacts to archive for an independent run

For each run, preserve the following together:

1. exact model and adapter identifiers;
2. environment lockfile or package snapshot;
3. train/validation/test manifests;
4. random seed and training arguments;
5. saved adapter/checkpoint;
6. raw predictions before metric computation;
7. filtering log identifying the 1,198 valid test examples;
8. metric script version and final summary.

This repository preserves the original notebooks and project utilities. The camera-ready paper in `docs/paper.pdf` is the authoritative description when a notebook and the manuscript differ.
