# StegGPT++

**An Advanced Linguistic Steganography Framework Powered by Large Language Models**

StegGPT++ is a research-grade, end-to-end framework for hiding secret messages inside natural-looking, AI-generated text. It combines modern cryptography (ECC/ECIES), adaptive compression, probabilistic roulette-wheel encoding, and LLM-based text generation into a single integrated 11-module pipeline — with built-in steganalysis resistance testing and a comprehensive evaluation suite.

> Version 2.0.0 · Python · Research / Academic Project

---

## ✨ Key Features

- **Complete 11-Module Pipeline** — from raw secret message to fluent stego text and back, with every stage modular and independently testable.
- **Strong Security by Design** — secrets are encrypted with Elliptic Curve Cryptography (SECP256R1 / ECIES with HKDF key derivation) *before* embedding, so even a fully broken embedding scheme never exposes plaintext.
- **Adaptive Compression** — automatic selection between Brotli and Deflate to maximize embedding capacity.
- **Roulette-Wheel Encoding** — dynamic, seeded probabilistic mapping of the encrypted bitstream onto tokens, avoiding the fixed statistical fingerprints of classical bin-based methods.
- **LLM-Powered Stego Generation** — GPT-2 backend (via Hugging Face Transformers) with adaptive token reweighting; optional OpenAI API backend.
- **Error Correction** — optional Hamming (7,4) coding for single-bit error recovery during extraction.
- **DL-Based Steganalysis Detector** — a BERT/RoBERTa classifier (Module 10) used adversarially to verify that generated stego text evades deep-learning detection.
- **Adversarial Robustness Testing** — automated perturbation attacks (character/word-level noise) to measure extraction reliability under hostile channel conditions.
- **Rich Evaluation Suite** — capacity (bits-per-word), fluency (n-gram perplexity), linguistic diversity, Shannon character/word entropy, and Cohen's effect size between cover and stego text.

---

## 🏗️ System Architecture

```
                          EMBEDDING PIPELINE
┌──────────────┐   ┌──────────────┐   ┌──────────────────┐
│ 1. Input &   │──▶│ 2. Compression│──▶│ 3. ECC Encryption │
│ Preprocessing│   │ (Brotli/      │   │ (SECP256R1 /      │
│ (→ bitstream)│   │  Deflate)     │   │  ECIES + HKDF)    │
└──────────────┘   └──────────────┘   └────────┬─────────┘
                                               ▼
┌──────────────┐   ┌──────────────┐   ┌──────────────────┐
│ 6. Adaptive  │◀──│ 5. Stego Text │◀──│ 4. Roulette-Wheel │
│ Token        │   │ Generation    │   │ Encoding          │
│ Reweighting  │   │ (GPT-2 / LLM) │   │ (bit → token map) │
└──────┬───────┘   └──────────────┘   └──────────────────┘
       ▼
┌──────────────┐        ┌─ 7. Hamming (7,4) Error Correction
│  STEGO TEXT  │ ◀──────┤
└──────┬───────┘        └─ (optional reliability layer)
       │
       ▼                  EXTRACTION & VALIDATION
┌──────────────┐   ┌──────────────┐   ┌──────────────────┐
│ 8. Extraction│──▶│ 9. Evaluation │──▶│ 10. Steganalysis  │
│ (reverse     │   │ & Metrics     │   │ Detector          │
│  pipeline)   │   │ (PPL/entropy) │   │ (BERT/RoBERTa)    │
└──────────────┘   └──────────────┘   └────────┬─────────┘
                                               ▼
                                    ┌──────────────────┐
                                    │ 11. Adversarial   │
                                    │ Robustness Tests  │
                                    └──────────────────┘
```

### Module Inventory

| # | Module | File | Responsibility |
|---|--------|------|----------------|
| 1 | Input & Preprocessing | `modules/input_preprocessing.py` | Cleans, normalizes (Unicode), and converts the secret message into a bitstream |
| 2 | Compression | `modules/compression.py` | Brotli / Deflate compression with automatic best-algorithm selection |
| 3 | ECC Encryption | `modules/encryption.py` | ECIES encryption over the P-256 curve with HKDF key derivation |
| 4 | Roulette-Wheel Encoding | `modules/encoding.py` | Seeded probabilistic mapping of encrypted bits to synthetic tokens |
| 5–6 | Stego Generation + Token Reweighting | `modules/stego_generation.py` | LLM text generation (GPT-2 / OpenAI) with adaptive token reweighting |
| 7 | Error Correction | `modules/error_correction.py` | Hamming (7,4) single-bit error correction |
| 8 | Extraction | `modules/extraction.py` | Reverse pipeline: stego text → tokens → bits → decrypt → decompress → secret |
| 9 | Evaluation & Metrics | `modules/evaluation.py` | Capacity, perplexity, entropy, diversity, and security metrics |
| 10 | Steganalysis Detector | `modules/steganalysis_detector.py` | BERT/RoBERTa deep-learning detector for stego vs. cover classification |
| 11 | Adversarial Robustness | `modules/adversarial_robustness.py` | Perturbation attacks and robustness scoring |

---

## 📂 Project Structure

```
R&D/
├── steggpt_integrated.py        # Main entry point — integrated 11-module pipeline & CLI
├── config.json                  # Central configuration (curve, backend, sampling, paths)
├── modules/                     # The 11 pipeline modules (see table above)
├── utils/                       # Shared utilities (config loader, logger)
├── keys/                        # Generated ECC key pairs (PEM) — DO NOT commit real keys
├── output/                      # Stego text, token mappings, metadata, extraction results
├── logs/                        # Run logs
├── analysis/                    # Research analysis documents & accuracy measurement scripts
├── Paper/                       # IEEE journal paper drafts and format analysis
├── FInal_Journal_paper/         # Final journal paper materials
├── Report/                      # Report templates
├── img/, images/                # Architecture diagrams and figures
├── steggpt_plus_journal_figures/# Publication-ready figures
└── archive/                     # Older experiments, scripts, and paper versions
```

The various `create_*.py` / `generate_*.py` scripts in the root directory produce the figures, tables, slides, and documents used in the accompanying research paper and presentation.

---

## 🚀 Getting Started

### Prerequisites

- Python 3.10+
- (Optional) CUDA-capable GPU for faster LLM inference

### Installation

```bash
# 1. Clone / copy the project, then create a virtual environment
python -m venv .venv

# 2. Activate it
#    Windows (PowerShell):
.venv\Scripts\Activate.ps1
#    Linux / macOS:
source .venv/bin/activate

# 3. Install dependencies
pip install torch transformers cryptography brotli numpy nltk \
            colorama scikit-learn matplotlib openai
```

### Usage

**Interactive mode** (guided menus for embedding, extraction, and evaluation):

```bash
python steggpt_integrated.py
```

**Embed a secret message from the command line:**

```bash
python steggpt_integrated.py --embed "Meet at dawn" --backend gpt2
```

**Extract a secret from a stego text file:**

```bash
python steggpt_integrated.py --extract output/stego_text.txt
```

**Other options:**

| Flag | Description | Default |
|------|-------------|---------|
| `--config` | Path to configuration file | `config.json` |
| `--backend` | LLM backend for generation (`gpt2`, OpenAI models) | `gpt2` |
| `--debug` | Enable verbose debug logging | off |

---

## ⚙️ Configuration

All pipeline behavior is controlled through [`config.json`](config.json). Notable options:

| Key | Purpose | Default |
|-----|---------|---------|
| `ecc_curve` | Elliptic curve for ECIES encryption | `SECP256R1` |
| `compression_algorithm` | `brotli`, `deflate`, or `auto` | `auto` |
| `roulette_seed` | Seed for deterministic roulette-wheel encoding | `42` |
| `default_backend` | LLM used for stego text generation | `gpt2` |
| `temperature` / `top_p` | LLM sampling parameters | `0.8` / `0.9` |
| `hamming_code` | Error correction scheme | `7,4` |
| `error_correction_enabled` | Toggle Hamming coding | `false` |
| `steganalysis_enabled` | Run DL steganalysis on generated text | `true` |
| `use_gpu` | Enable GPU acceleration | `true` |
| `openai_api_key` | API key for the optional OpenAI backend | `null` |

---

## 📊 Evaluation Metrics

Every embedding run can produce a full metrics report covering:

- **Capacity** — embedding rate (bits per word / per token)
- **Fluency** — n-gram perplexity of stego vs. cover text
- **Imperceptibility** — Cohen's effect size between cover and stego distributions
- **Security** — Shannon character & word entropy, statistical detectability
- **Diversity** — type-token ratio and linguistic variety measures
- **Robustness** — extraction success rate under adversarial perturbation
- **Detectability** — accuracy / precision / recall / F1 of the BERT/RoBERTa steganalysis detector

Detailed methodology and results are documented in the [`analysis/`](analysis/) directory.

---

## 🔐 Security Notes

- Secret messages are **encrypted before embedding** — confidentiality does not depend on the secrecy of the embedding algorithm (Kerckhoffs's principle).
- ECC key pairs are generated into [`keys/`](keys/). **Never commit production private keys** to version control; the included keys are for demo/debug purposes only.
- This project is intended for **academic research and authorized security study** of linguistic steganography and steganalysis.

---

## 📄 Research & Publications

This codebase supports the StegGPT++ research paper. Supporting materials:

- [`Paper/`](Paper/) — IEEE journal paper drafts and formatting analysis
- [`FInal_Journal_paper/`](FInal_Journal_paper/) — final journal submission materials
- [`analysis/`](analysis/) — module inventories, accuracy formula derivations, comparison analyses, and defense Q&A content
- `Slide_01` … `Slide_13` (`.docx`) — presentation deck covering each module and the overall architecture

---

## 🛠️ Tech Stack

| Category | Technology |
|----------|------------|
| Language | Python 3 |
| Deep Learning | PyTorch, Hugging Face Transformers (GPT-2, BERT/RoBERTa) |
| Cryptography | `cryptography` (ECC P-256, ECIES, HKDF, AES) |
| Compression | Brotli, zlib (Deflate) |
| NLP / Metrics | NLTK, scikit-learn, NumPy |
| Visualization | Matplotlib |
| CLI | argparse, colorama |

---

## 📜 License

This project is part of an academic R&D effort. Contact the authors for licensing and reuse permissions.

## 🙏 Acknowledgements

Built on the shoulders of the open-source community — particularly the Hugging Face Transformers, PyTorch, and Python `cryptography` projects.
