<div align="center">

# Parameter-Efficient Adaptation of Qwen3-VL for Handwritten Mathematical Expression Recognition

Official implementation of our Qwen3-VL approach to handwritten mathematical expression recognition (HMER) on CROHME 2019.

**Accepted and presented at the [International Symposium on Semantic Intelligence in Massive Computing (SIMC 2026)](https://www.simc-conf.org/).**

**Preprint now available on arXiv.**

[![arXiv](https://img.shields.io/badge/arXiv-2611.27208-b31b1b?logo=arxiv&logoColor=white)](https://arxiv.org/abs/2611.27208)
[![Paper](https://img.shields.io/badge/Paper-PDF-b31b1b?logo=adobeacrobatreader&logoColor=white)](https://arxiv.org/pdf/2611.27208)
[![Conference](https://img.shields.io/badge/SIMC_2026-2ea44f)](https://www.simc-conf.org/)
[![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![PyTorch](https://img.shields.io/badge/PyTorch-2.x-EE4C2C?logo=pytorch&logoColor=white)](https://pytorch.org/)

[arXiv](https://arxiv.org/abs/2611.27208) |
[Paper PDF](https://arxiv.org/pdf/2611.27208) |
[Results](#results) |
[Setup](#setup) |
[Citation](#citation)

</div>

## Abstract

Handwritten mathematical expression recognition (HMER) requires joint recovery of symbol identity and two-dimensional syntax, so a locally plausible transcription may still be structurally wrong. We study whether a compact vision-language model can be adapted to generate linearized Symbol Label Graphs (SLGs) under limited computing resources.

Starting from a 4-bit Qwen3-VL-4B checkpoint, we apply rank-16 Low-Rank Adaptation (LoRA) to the vision, language, attention, and MLP modules and train with instruction-formatted CROHME 2019 examples.

We compare this model with an internally implemented ViT-Transformer baseline, a zero-shot Qwen3-VL configuration, and additional internally evaluated alternatives. On the 1,198 valid CROHME test examples, the CROHME-only model obtains 524 exact matches (43.74%) and a corpus BLEU-4 score of 77.67, compared with 4.92% and 43.26 for the task-specific ViT baseline.

An ablation that adds 2,000 manually written, formula-guided samples decreases exact match to 42.32%, exposing a domain-alignment issue rather than an automatic benefit from more data.

The principal contribution is therefore an evidence-backed, resource-efficient formulation of HMER as multimodal structural generation, together with a controlled data-composition ablation and an analysis of the remaining gap between local token overlap and globally correct structure.

For more details, please refer to our [paper on arXiv](https://arxiv.org/abs/2611.27208).

## Key Contributions

- **Parameter-efficient adaptation:** Adapt Qwen3-VL-4B using 4-bit quantization and rank-16 LoRA under limited computing resources.
- **Structural generation:** Formulate handwritten mathematical expression recognition as the generation of linearized Symbol Label Graphs (SLGs).
- **Comparative evaluation:** Evaluate Qwen3-VL against internally implemented ViT-Transformer, Qwen2.5-VL, and IM2TEX alternatives.
- **Data-composition ablation:** Investigate the impact of incorporating 2,000 additional handwritten mathematical expression samples.
- **Structural error analysis:** Examine the gap between local token overlap and globally correct mathematical expression structure.

## Results

| Model | Training data | Exact match | BLEU-4 |
|:--|:--|--:|--:|
| ViT Hybrid baseline | CROHME (8.9K) | 4.92% | 43.26 |
| Qwen3-VL zero-shot | None | 0.00% | 5.00 |
| Qwen2.5-VL fine-tuned | CROHME (8.9K) | 28.38% | 63.00 |
| IM2TEX reimplementation | CROHME (8.9K) | 38.74% | 50.28 |
| **Qwen3-VL fine-tuned (Ours)** | **CROHME (8.9K)** | **43.74%** | **77.67** |
| Qwen3-VL fine-tuned (Ours) | CROHME + 2K | 42.32% | 75.46 |

All non-zero-shot entries are results from the authors' local implementations. BLEU-4 measures token overlap and is not the official CROHME graph metric.

## Setup

Python 3.10+ and a CUDA-capable GPU are recommended.

### 1. Clone the repository

```bash
git clone https://github.com/tuanfptu/Qwen3-VL-HMER.git
cd Qwen3-VL-HMER
```

### 2. Create a virtual environment

```bash
python -m venv .venv
```

Activate the virtual environment:

**Linux/macOS**

```bash
source .venv/bin/activate
```

**Windows PowerShell**

```powershell
.\.venv\Scripts\Activate.ps1
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

## Training and Evaluation

### ViT-Transformer Baseline

Train the baseline model:

```bash
python src/train.py
```

Evaluate the trained model:

```bash
python src/evaluate.py
```

### Qwen3-VL Fine-tuning

The Qwen3-VL experiments are preserved in the research notebooks under [`notebooks/`](notebooks/).

Configuration, data splits, training procedures, and evaluation details are documented in [`docs/REPRODUCIBILITY.md`](docs/REPRODUCIBILITY.md).

For the complete methodology and experimental analysis, please refer to the [paper](https://arxiv.org/pdf/2611.27208).

## Inference Service

Install the application dependencies and place the LoRA adapter under `models/`:

```bash
pip install -r requirements-app.txt
python apps/main.py
```

The inference service listens on:

```text
http://localhost:8080
```

Docker, Streamlit, Gradio, and browser clients are available under [`apps/`](apps/).

## Model Artifacts

Model weights are excluded from the repository because they exceed GitHub's per-file size limit.

| Artifact | Purpose | Size |
|:--|:--|--:|
| `vit_seq2seq_.pt` | ViT-Transformer checkpoint | 297 MB |
| `lora_model_qwen3vl.zip` | Qwen3-VL LoRA adapter | 165 MB |

See [`models/README.md`](models/README.md) for model placement instructions.

## Repository Structure

```text
Qwen3-VL-HMER/
├── apps/                   # Inference API and application clients
├── data/                   # CROHME data, metadata, and tokenizer
├── docs/                   # Paper and reproducibility notes
├── models/                 # Local checkpoints (Git-ignored)
├── notebooks/              # Research and fine-tuning workflows
├── src/                    # Baseline training and evaluation
├── tools/                  # Data generation and annotation utilities
├── CITATION.cff             # Machine-readable citation metadata
├── requirements.txt        # Research dependencies
├── requirements-app.txt    # Application dependencies
└── README.md
```

## Paper

**Parameter-Efficient Adaptation of Qwen3-VL for Handwritten Mathematical Expression Recognition**

Manh Tuan Ha, Xuan Bao Viet Tran, Vo Minh Dat Nguyen, Minh Nhat Vo, Quoc Trung Nguyen, and Van Bay Nguyen.

- **Conference:** International Symposium on Semantic Intelligence in Massive Computing (SIMC 2026)
- **Status:** Accepted and presented; conference proceedings forthcoming.
- **Preprint:** [arXiv:2611.27208](https://arxiv.org/abs/2611.27208)
- **Full paper:** [PDF](https://arxiv.org/pdf/2611.27208)

## Citation

If you find our work useful in your research, please consider citing our paper:

```bibtex
@misc{ha2026qwen3vlhmer,
  title         = {Parameter-Efficient Adaptation of Qwen3-VL for Handwritten Mathematical Expression Recognition},
  author        = {Manh Tuan Ha and Xuan Bao Viet Tran and Vo Minh Dat Nguyen and Minh Nhat Vo and Quoc Trung Nguyen and Van Bay Nguyen},
  year          = {2026},
  eprint        = {2611.27208},
  archivePrefix = {arXiv},
  url           = {https://arxiv.org/abs/2611.27208}
}
```

Machine-readable citation metadata is available in [`CITATION.cff`](CITATION.cff).

## Acknowledgements

This research builds upon the following models, datasets, and frameworks:

- [Qwen3-VL](https://github.com/QwenLM/Qwen3-VL)
- [CROHME](https://www.isical.ac.in/~crohme/)
- [PyTorch](https://pytorch.org/)
- [Hugging Face Transformers](https://github.com/huggingface/transformers)
- [PEFT](https://github.com/huggingface/peft)

## License

No open-source license has been selected. Copyright remains with the authors. Third-party datasets, models, and dependencies remain subject to their respective terms.

---

<div align="center">

If you find this repository useful, please consider giving it a ⭐.

</div>
