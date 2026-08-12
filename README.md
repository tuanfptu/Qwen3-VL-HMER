<div align="center">

# Parameter-Efficient Adaptation of Qwen3-VL for Handwritten Mathematical Expression Recognition

Official implementation of our Qwen3-VL approach to handwritten mathematical expression recognition on CROHME 2019.

**Accepted at the [International Symposium on Semantic Intelligence in Massive Computing (SIMC 2026)](https://www.simc-conf.org/).**

[![Paper](https://img.shields.io/badge/Paper-PDF-b31b1b?logo=adobeacrobatreader&logoColor=white)](docs/paper.pdf)
[![Conference](https://img.shields.io/badge/SIMC_2026-Accepted-2ea44f)](https://www.simc-conf.org/)
[![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![PyTorch](https://img.shields.io/badge/PyTorch-2.x-EE4C2C?logo=pytorch&logoColor=white)](https://pytorch.org/)

[Paper](docs/paper.pdf) | [Results](#results) | [Setup](#setup) | [Citation](#citation)

</div>

## Overview

We formulate handwritten mathematical expression recognition (HMER) as multimodal structural generation. A 4-bit Qwen3-VL-4B model is adapted with rank-16 LoRA/QLoRA to generate linearized Symbol Label Graphs (SLGs), including both mathematical symbols and their spatial relations.

On 1,198 valid CROHME 2019 test expressions, the best configuration achieves **43.74% exact match (524/1,198)** and **77.67 BLEU-4**. Adding 2,000 manually written samples reduces performance slightly, highlighting the importance of domain alignment in data augmentation.

## Results

| Model | Training data | Exact match | BLEU-4 |
|:--|:--|--:|--:|
| ViT Hybrid baseline | CROHME (8.9K) | 4.92% | 43.26 |
| Qwen3-VL zero-shot | None | 0.00% | 5.00 |
| Qwen2.5-VL fine-tuned | CROHME (8.9K) | 28.38% | 63.00 |
| IM2TEX reimplementation | CROHME (8.9K) | 38.74% | 50.28 |
| **Qwen3-VL fine-tuned** | **CROHME (8.9K)** | **43.74%** | **77.67** |
| Qwen3-VL fine-tuned | CROHME + 2K | 42.32% | 75.46 |

All non-zero-shot entries are results from the authors' local implementations. BLEU-4 measures token overlap and is not the official CROHME graph metric.

## Setup

Python 3.10+ and a CUDA-capable GPU are recommended.

```bash
git clone https://github.com/tuanfptu/handwritten-math-recognition.git
cd handwritten-math-recognition
python -m venv .venv
```

```bash
# Linux/macOS
source .venv/bin/activate

# Windows PowerShell
.\.venv\Scripts\Activate.ps1

pip install -r requirements.txt
```

## Training and evaluation

Run the ViT-Transformer baseline:

```bash
python src/train.py
python src/evaluate.py
```

The Qwen3-VL experiments are preserved in the research notebooks. Configuration, data-split, and evaluation details are documented in [`docs/REPRODUCIBILITY.md`](docs/REPRODUCIBILITY.md).

## Inference service

Install the application dependencies and place the LoRA adapter under `models/`:

```bash
pip install -r requirements-app.txt
python apps/main.py
```

The service listens on `http://localhost:8080`. Docker, Streamlit, Gradio, and browser clients are available under [`apps/`](apps/).

## Model artifacts

Model weights are excluded because they exceed GitHub's per-file size limit.

| Artifact | Purpose | Size |
|:--|:--|--:|
| `vit_seq2seq_.pt` | ViT-Transformer checkpoint | 297 MB |
| `lora_model_qwen3vl.zip` | Qwen3-VL LoRA adapter | 165 MB |

See [`models/README.md`](models/README.md) for placement instructions.

## Repository structure

```text
apps/       Inference API and clients
data/       CROHME data, metadata, and tokenizer
docs/       Paper and reproducibility notes
models/     Local checkpoints (Git-ignored)
notebooks/  Research workflows
src/        Baseline training and evaluation
tools/      Data-generation and annotation utilities
```

## Citation

```bibtex
@inproceedings{ha2026qwen3vlhmer,
  title     = {Parameter-Efficient Adaptation of Qwen3-VL for Handwritten Mathematical Expression Recognition},
  author    = {Ha, Manh Tuan and Tran, Xuan Bao Viet and Nguyen, Vo Minh Dat and Vo, Minh Nhat and Nguyen, Quoc Trung and Nguyen, Van Bay},
  booktitle = {Proceedings of the International Symposium on Semantic Intelligence in Massive Computing (SIMC 2026)},
  year      = {2026}
}
```

Machine-readable metadata is available in [`CITATION.cff`](CITATION.cff).

## License

No open-source license has been selected. Copyright remains with the authors. Third-party datasets, models, and dependencies remain subject to their respective terms.
