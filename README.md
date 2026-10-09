# Stable Diffusion XL Inference Baseline Benchmark

[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](LICENSE)
[![CI](https://github.com/garys-gpu-benchmarks/324-gpu-bench-amd-sdxl-diffusers-latency-ubu2604/actions/workflows/ci.yml/badge.svg)](https://github.com/garys-gpu-benchmarks/324-gpu-bench-amd-sdxl-diffusers-latency-ubu2604/actions/workflows/ci.yml)

Target: Ubuntu 26.04 · AMD · see Hardware Requirements. This is a host benchmark, not a laptop `pip install` project.

## Quick Start

```bash
git clone https://github.com/garys-gpu-benchmarks/324-gpu-bench-amd-sdxl-diffusers-latency-ubu2604.git
cd 324-gpu-bench-amd-sdxl-diffusers-latency-ubu2604
sudo bash setup.sh --assume-yes
bash run_benchmark.sh --profile smoke --validate
```
Results are written to `results/benchmark.db` and `results/summary.json`.

This workload is executed on the validation host after the repository is copied there. `setup.sh` and `run_benchmark.sh` do not open an outbound SSH session.

Prerequisites: Ubuntu 26.04; AMD; Python 3.14.4; root or sudo for `setup.sh`. Framework: Bash, SQLite, Python, PyYAML, ROCm Runtime, PyTorch-ROCm, Hugging Face diffusers, Stable Diffusion XL. Set HF_TOKEN when the model license requires a Hugging Face token. This is a host benchmark, not a laptop `pip install` project.

```mermaid
flowchart LR
  setup.sh --> run_benchmark.sh --> parse_results.py --> results/benchmark.db
```

## 1. Overview

Runs scripts/infer_sdxl.py --backend sdxl. Every profile loads stabilityai/stable-diffusion-xl-base-1.0; smoke uses reduced resolution and steps. image_resolution, num_images, num_inference_steps, precision, scheduler, guidance_scale, and seed come from the runner. This is not TorchBench run.py. Sweep dimensions: model_name, precision, scheduler, prompt_source, image_resolution, num_images, num_inference_steps, guidance_scale.

## 2. What It Validates

- Validates SDXL image-generation latency, images/s, and peak VRAM. Every profile loads the real SDXL pipeline; smoke uses reduced resolution and steps
- #1: Per-image latency (per_image_latency_sec); is present and physically sensible.
- #2: Aggregate throughput (images_per_sec); is present and physically sensible.
- #3: Peak VRAM (gpu_mem_gb); is present and physically sensible.
- #4: Denoising step time per batch, ms (step_time_ms); is present and physically sensible.
- #5: First-image latency, cold, ms (first_image_latency_ms) is present and physically sensible.

## 3. Metrics Captured

- **#1: Per-image latency** — stored as `per_image_latency_sec`.
- **#2: Aggregate throughput** — stored as `images_per_sec`.
- **#3: Peak VRAM** — stored as `gpu_mem_gb`.
- **#4: Denoising step time per batch, ms** — stored as `step_time_ms`.
- **#5: First-image latency, cold, ms** — stored as `first_image_latency_ms`.

## 4. Hardware Requirements

### Supported environment

- OS: Ubuntu 26.04
- GPU vendor: AMD
- Framework family: Bash, SQLite, Python, PyYAML, ROCm Runtime, PyTorch-ROCm, Hugging Face diffusers, Stable Diffusion XL
- Python: Python 3.14.4

### Reference validation environment

The tables below describe the machine used to generate the reference results. They are not a requirement that every user buy that exact cloud instance.

### System

Ubuntu 26.04 / AMD / Bash, SQLite, Python, PyYAML, ROCm Runtime, PyTorch-ROCm, Hugging Face diffusers, Stable Diffusion XL

### GPU

Ubuntu 26.04 / AMD / Bash, SQLite, Python, PyYAML, ROCm Runtime, PyTorch-ROCm, Hugging Face diffusers, Stable Diffusion XL

## 5. Software Requirements

| Component | Version |
|---|---|
| OS | Ubuntu 26.04 |
| Kernel | kernel 7.0.0 |
| Python | Python 3.14.4 |
| ROCm | ROCm 7.14 |
| rocBLAS | rocBLAS 5.2.0 |

Runs scripts/infer_sdxl.py --backend sdxl. Every profile loads stabilityai/stable-diffusion-xl-base-1.0; smoke uses reduced resolution and steps. image_resolution, num_images, num_inference_steps, precision, scheduler, guidance_scale, and seed come from the runner.

## 6. Installation

```bash
Run scripts/infer_sdxl.py --backend sdxl. Every profile loads real SDXL; smoke uses reduced resolution and steps
```

## 7. Running the Benchmark

```bash
Run scripts/infer_sdxl.py --backend sdxl. Every profile loads real SDXL; smoke uses reduced resolution and steps
```

**Validating results separately:**

```bash
export BENCHMARK_PYTHON=/usr/bin/python3.13  # optional
python3 -m venv .venv
source ".venv/bin/activate"
".venv/bin/python" scripts/validate_results.py
```

## 8. Output

### `results/benchmark.db` (SQLite)

infer_sdxl.py CSV, one row per image. Baseline and extended also include first_image_latency_ms

sample_index,status,latency_ms,per_image_latency_sec,images_per_sec,gpu_mem_gb,step_time_ms,first_image_latency_ms,error_message
0,ok,800,0.8,1.25,8.0,16,1200,

```bash
Run scripts/infer_sdxl.py --backend sdxl. Every profile loads real SDXL; smoke uses reduced resolution and steps
```

### `results/summary.json`

Consolidated metrics from the most recent run — suitable for CI artifact upload or dashboard ingestion.

### `results/raw/<timestamp>.txt`

infer_sdxl.py CSV, one row per image. Baseline and extended also include first_image_latency_ms

sample_index,status,latency_ms,per_image_latency_sec,images_per_sec,gpu_mem_gb,step_time_ms,first_image_latency_ms,error_message
0,ok,800,0.8,1.25,8.0,16,1200,

## 9. Baselines / Thresholds

Expected ranges and gates live in `config/benchmark_config.yaml` under `baselines:` or `thresholds:`. To update them, edit that file — never edit validation code directly.

## 10. Troubleshooting

**`setup.sh` missing collector**
Create cannot finish without `scripts/collect_workload.py`.

**`self_check` overlay rewritten**
Do not overwrite files listed in `results/overlay_lock.json`.

**Remote SSH drop during setup**
Reconnect and resume `bash setup.sh --assume-yes`. Do not wipe `.venv` or `.cache`.

## 11. NVIDIA H100 Coding Differences

Primary target is AMD ROCm. NVIDIA notes in this section are reference only and are not the execution path.

## Repository layout

```text
.
├── setup.sh
├── run_benchmark.sh
├── benchmark_specification.json
├── .github/workflows/      # thin CI callers (see Continuous Integration)
├── config/
├── scripts/
├── src/
├── tests/
├── docs/
├── results/
└── LICENSE
```

## Continuous Integration

| Workflow | Runs on | When | What it does |
|---|---|---|---|
| [CI](.github/workflows/ci.yml) | GitHub-hosted runner | every pull request, and every push to `main` | shellcheck, ruff, `bash -n`, `compileall`, `run_benchmark.sh --help`, specification schema, the results validator on a seeded fixture, required files, and actionlint. No GPU and no benchmark run. |
| [GPU Smoke Benchmark](.github/workflows/gpu-smoke.yml) | self-hosted runner labeled `gpu`, `amd`, `ubu2604` | only when started by hand: **Actions → GPU Smoke Benchmark → Run workflow** (choose `smoke`, `baseline` or `extended`) | Verifies the pre-provisioned GPU stack, records `results/environment.json` (driver, runtime, kernel, GPU), runs the profile with `--validate`, shows headline metrics on the run page, and uploads the results. |

Both files are short callers. The steps themselves live once, for every workload in the suite, in [`garys-gpu-benchmarks/shared-workflows`](https://github.com/garys-gpu-benchmarks/shared-workflows), pinned at `@v1`. The GPU workflow is never triggered by pull requests, so code from a fork cannot run on the GPU host.

### Running it as part of the AMD Ubuntu 26.04 bundle

This repository is one of the 32 workloads in [`bundle-amd-ubuntu-2604`](https://github.com/garys-gpu-benchmarks/bundle-amd-ubuntu-2604), which holds them as git submodules. To put the whole bundle on a GPU host and run this workload from it:

```bash
git clone --recurse-submodules https://github.com/garys-gpu-benchmarks/bundle-amd-ubuntu-2604 /opt/benchmarks
cd /opt/benchmarks/324-gpu-bench-amd-sdxl-diffusers-latency-ubu2604
bash run_benchmark.sh --profile smoke --validate
```
