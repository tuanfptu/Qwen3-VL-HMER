<div align="center">

# Parameter-Efficient Adaptation of Qwen3-VL for Handwritten Mathematical Expression Recognition

Official research repository for our Qwen3-VL approach to handwritten mathematical expression recognition (HMER) on CROHME 2019.

[![Paper](https://img.shields.io/badge/Paper-PDF-b31b1b?logo=adobeacrobatreader&logoColor=white)](docs/paper.pdf)
[![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![Framework](https://img.shields.io/badge/PyTorch-2.x-EE4C2C?logo=pytorch&logoColor=white)](https://pytorch.org/)
[![Dataset](https://img.shields.io/badge/Dataset-CROHME_2019-4c8bf5)](#dataset)

[Paper](docs/paper.pdf) · [Demo](#demo) · [Results](#main-results) · [Installation](#installation) · [Citation](#citation)

</div>

## Overview

Handwritten mathematical expression recognition requires more than identifying individual symbols: a model must also recover two-dimensional relationships such as superscripts, subscripts, above, below, and right-neighbor links. We formulate HMER as multimodal structural generation and adapt a 4-bit Qwen3-VL-4B checkpoint with rank-16 LoRA/QLoRA adapters.

The model is instruction-tuned to generate linearized Symbol Label Graphs (SLGs). On 1,198 valid CROHME 2019 test expressions, the CROHME-only configuration achieves **43.74% exact match (524/1,198)** and **77.67 BLEU-4**. A controlled ablation with 2,000 additional handwritten samples shows that more data is not automatically better when the added handwriting distribution is misaligned with the target benchmark.

### Contributions

- A resource-efficient Qwen3-VL adaptation for image-to-SLG generation.
- A controlled comparison with zero-shot Qwen3-VL, Qwen2.5-VL, IM2TEX, and a ViT-Transformer baseline.
- An ablation on 2,000 manually written, formula-guided samples.
- Error analysis separating symbol-recognition failures from output-grammar failures.
- Training, evaluation, preprocessing, annotation, API, and interactive demo code.

## Authors

- Manh Tuan Ha ([ORCID](https://orcid.org/0009-0004-7583-1148)) — corresponding author
- Xuan Bao Viet Tran ([ORCID](https://orcid.org/0009-0000-4975-0601))
- Vo Minh Dat Nguyen ([ORCID](https://orcid.org/0009-0000-7772-8436))
- Minh Nhat Vo ([ORCID](https://orcid.org/0009-0004-9580-581X))
- Quoc Trung Nguyen ([ORCID](https://orcid.org/0000-0002-5500-3554))
- Van Bay Nguyen ([ORCID](https://orcid.org/0009-0000-7474-5193))

Affiliations: FPT University and Ho Chi Minh City Open University, Vietnam.

## Main results

All non-zero-shot entries below are results from the authors' local implementations under the protocol described in the paper. They are not copied leaderboard scores.

| Model | Training data | Exact match | BLEU-4 |
|:--|:--|--:|--:|
| ViT Hybrid baseline | CROHME (8.9K) | 4.92% (59/1,198) | 43.26 |
| Qwen3-VL zero-shot | None | 0.00% (0/1,198) | 5.00 |
| Qwen2.5-VL fine-tuned | CROHME (8.9K) | 28.38% (340/1,198) | 63.00 |
| IM2TEX reimplementation | CROHME (8.9K) | 38.74% | 50.28 |
| **Qwen3-VL fine-tuned** | **CROHME (8.9K)** | **43.74% (524/1,198)** | **77.67** |
| Qwen3-VL fine-tuned | CROHME + 2K generated | 42.32% (507/1,198) | 75.46 |

> Exact match is strict: one incorrect relation token invalidates the entire sequence. BLEU-4 measures local token overlap and is not a replacement for the official CROHME graph evaluation.

## Method

```text
handwritten expression
        │
        ▼
Qwen3-VL visual encoder + language backbone (4-bit)
        │
        ├── rank-16 LoRA adapters on vision/language attention and MLP modules
        ▼
linearized Symbol Label Graph
        │
        ▼
symbols + spatial-relation tokens
```

The repository also retains the internally implemented ViT/Hybrid encoder and autoregressive Transformer decoder used as a task-specific baseline.

## Demo

https://github.com/user-attachments/assets/4821be03-e62f-48c6-9597-fb6648b1f6e7

The interactive applications accept a handwritten formula image and return LaTeX or a linearized label graph. Browser, Streamlit, and Gradio clients are available under [`apps/web/`](apps/web/).

## Installation

### Requirements

- Python 3.10 or newer
- CUDA-capable GPU recommended
- Git

```bash
git clone https://github.com/tuanfptu/handwritten-math-recognition.git
cd handwritten-math-recognition
python -m venv .venv
```

Linux/macOS:

```bash
source .venv/bin/activate
pip install -r requirements.txt
```

Windows PowerShell:

```powershell
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

Application/API dependencies are kept separately:

```bash
pip install -r requirements-app.txt
```

## Dataset

The experiments use CROHME 2019 InkML expressions and their structural annotations. Repository-relative paths are configured in [`src/config.py`](src/config.py); the expected metadata and tokenizer are under [`data/`](data/).

The additional 2K samples are an experimental ablation, not the default training set. Dataset use remains subject to the original CROHME academic/research terms.

## Training and evaluation

Train the ViT-Transformer baseline:

```bash
python src/train.py
```

Evaluate a saved baseline checkpoint:

```bash
python src/evaluate.py
```

The Qwen3-VL experiments were run from the archived research notebooks. Follow the complete environment, checkpoint, data-split, and evaluation notes in [`docs/REPRODUCIBILITY.md`](docs/REPRODUCIBILITY.md) before comparing results.

## Run the Qwen3-VL service

Place the LoRA artifact in `models/`, set its path, and start the API:

```powershell
$env:MODEL_PATH = (Resolve-Path models\lora_model_qwen3vl.zip)
python apps/main.py
```

Or use Docker on a host with NVIDIA Container Toolkit:

```bash
cd apps
docker compose up --build
```

The service listens on `http://localhost:8080`. Start a client in a second terminal:

```bash
streamlit run apps/web/app.py
# or
python apps/web/app_gradio_dual.py
```

## Model artifacts

Weights are not committed because they exceed GitHub's per-file size limit.

| Artifact | Purpose | Approximate size |
|:--|:--|--:|
| `vit_seq2seq_.pt` | ViT + Transformer baseline checkpoint | 297 MB |
| `lora_model_qwen3vl.zip` | Qwen3-VL LoRA adapter | 165 MB |

See [`models/README.md`](models/README.md) for placement details. For public distribution, use a versioned GitHub Release or a model repository such as Hugging Face.

## Repository structure

```text
.
├── apps/          # Inference API, Docker deployment, and UI clients
├── assets/        # Demo recording and example inputs
├── data/          # CROHME metadata, samples, and tokenizer
├── docs/          # Paper, reproducibility guide, and experiment figures
├── models/        # Local checkpoints (ignored by Git)
├── notebooks/     # EDA, preprocessing, and training workflows
├── src/           # ViT baseline, training, and evaluation code
└── tools/         # Synthetic-data and annotation utilities
```

## Citation

If this repository supports your work, please cite the paper. A machine-readable citation is available in [`CITATION.cff`](CITATION.cff).

```bibtex
@inproceedings{ha2026qwen3vlhmer,
  title     = {Parameter-Efficient Adaptation of Qwen3-VL for Handwritten Mathematical Expression Recognition},
  author    = {Ha, Manh Tuan and Tran, Xuan Bao Viet and Nguyen, Vo Minh Dat and Vo, Minh Nhat and Nguyen, Quoc Trung and Nguyen, Van Bay},
  booktitle = {Proceedings of SIMC 2026},
  year      = {2026}
}
```

## Acknowledgements

This research was conducted at FPT University with collaboration from Ho Chi Minh City Open University. It builds on CROHME, Qwen3-VL, PyTorch, Hugging Face Transformers, timm, LitServe, Streamlit, and Gradio.

## License

No open-source license has been selected. Copyright remains with the authors. Third-party datasets, pretrained models, and dependencies remain subject to their respective licenses and terms of use.
