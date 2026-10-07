# SPEC.md: "The Human How"; Exact technical requirements, environment setup, implementation details, etc.

## Execution Description

Runs scripts/infer_sdxl.py --backend sdxl. Every profile loads stabilityai/stable-diffusion-xl-base-1.0; smoke uses reduced resolution and steps. image_resolution, num_images, num_inference_steps, precision, scheduler, guidance_scale, and seed come from the runner. This is not TorchBench run.py. Sweep dimensions: model_name, precision, scheduler, prompt_source, image_resolution, num_images, num_inference_steps, guidance_scale.

## Parameters

| Parameter | CLI Flag | Tested Values | Default | Description |
| --- | --- | --- | --- | --- |
| model_name | `--model-name` | smoke=sdxl-base-1.0, baseline=sdxl-base-1.0, extended=sdxl-base-1.0 | sdxl-base-1.0 | From Parameter list; see Execution Description With Parameters. |
| precision | `--precision` | smoke=fp16, baseline=fp16, extended=fp16 | fp16 | From Parameter list; see Execution Description With Parameters. |
| scheduler | `--scheduler` | smoke=euler, baseline=euler, extended=euler | euler | From Parameter list; see Execution Description With Parameters. |
| prompt_source | `--prompt-source` | smoke=synthetic, baseline=synthetic, extended=synthetic | synthetic | From Parameter list; see Execution Description With Parameters. |
| image_resolution | `--image-resolution` | smoke=64, baseline=512, extended=1024 | 512 | From Parameter list; see Execution Description With Parameters. |
| num_images | `--num-images` | smoke=1, baseline=132, extended=233 | 132 | From Parameter list; see Execution Description With Parameters. |
| num_inference_steps | `--num-inference-steps` | smoke=2, baseline=30, extended=50 | 30 | From Parameter list; see Execution Description With Parameters. |
| guidance_scale | `--guidance-scale` | smoke=0, baseline=0, extended=0 | 0 | From Parameter list; see Execution Description With Parameters. |
| seed | `--seed` | smoke=42, baseline=42, extended=42 | 42 | From Parameter list; see Execution Description With Parameters. |

## Invocation

```bash
Run scripts/infer_sdxl.py --backend sdxl. Every profile loads real SDXL; smoke uses reduced resolution and steps
```

## Raw Output Format

infer_sdxl.py CSV, one row per image. Baseline and extended also include first_image_latency_ms

sample_index,status,latency_ms,per_image_latency_sec,images_per_sec,gpu_mem_gb,step_time_ms,first_image_latency_ms,error_message
0,ok,800,0.8,1.25,8.0,16,1200,

## Metrics

- **#1: Per-image latency** — stored as `per_image_latency_sec`.
- **#2: Aggregate throughput** — stored as `images_per_sec`.
- **#3: Peak VRAM** — stored as `gpu_mem_gb`.
- **#4: Denoising step time per batch, ms** — stored as `step_time_ms`.
- **#5: First-image latency, cold, ms** — stored as `first_image_latency_ms`.

## Framework

Runs scripts/infer_sdxl.py --backend sdxl. Every profile loads stabilityai/stable-diffusion-xl-base-1.0; smoke uses reduced resolution and steps. image_resolution, num_images, num_inference_steps, precision, scheduler, guidance_scale, and seed come from the runner.

## Installation and Execution Summary

Run scripts/infer_sdxl.py --backend sdxl. Every profile loads stabilityai/stable-diffusion-xl-base-1.0; smoke uses reduced resolution and steps. Parse latency_ms, images_per_sec, and gpu_mem_gb, to measure SDXL image-generation performance

## Platform Portability

- **AMD (primary):** ```bash
Run scripts/infer_sdxl.py --backend sdxl. Every profile loads real SDXL; smoke uses reduced resolution and steps
```
- **NVIDIA:** Primary target is AMD ROCm. NVIDIA notes in this section are reference only and are not the execution path.

## Model Context Protocols

- **Active:** None

## Execution-Loop Validation Contract

EXECUTION CHAIN: `run_benchmark.sh` ➔ raw output ➔ `scripts/parse_results.py` ➔ `results/benchmark.db` ➔ `scripts/validate_results.py`

This benchmark uses a lightweight, SQLite-integrated execution loop for result validation. All validation is performed by `scripts/validate_results.py`.

### Validation script usage

```bash
export BENCHMARK_PYTHON=/usr/bin/python3.13  # optional; select the installed interpreter

# After a live run:
".venv/bin/python" scripts/validate_results.py --db results/benchmark.db

# CI / no-GPU path (seeds fixture and validates it):
".venv/bin/python" scripts/validate_results.py --seed-fixture --quiet

# Override DB path via environment variable:
BENCHMARK_DB=tests/fixtures/benchmark.db \
  ".venv/bin/python" scripts/validate_results.py
```

### Run artifact contract

infer_sdxl.py CSV, one row per image. Baseline and extended also include first_image_latency_ms

sample_index,status,latency_ms,per_image_latency_sec,images_per_sec,gpu_mem_gb,step_time_ms,first_image_latency_ms,error_message
0,ok,800,0.8,1.25,8.0,16,1200,

```bash
bash run_benchmark.sh --help
bash run_benchmark.sh --profile smoke --validate
bash run_benchmark.sh --profile baseline --validate
bash run_benchmark.sh --profile extended --validate
```
`run_benchmark.sh --help` prints usage and exits. The harness calls `scripts/ensure_setup.sh` when `.setup_state` is absent.

### Required integrity checks (built into `validate_results.py`)

1. Latest run exists and `runs.status = 'ok'`.
2. `run.error_message` is NULL.
3. `started_at` and `finished_at` are valid ISO-8601 UTC strings.
4. All required aggregate metrics in `runs` are non-NULL and finite.
5. All required aggregate metrics are physically sensible (positive values). Runs scripts/infer_sdxl.py --backend sdxl. Every profile loads stabilityai/stable-diffusion-xl-base-1.0; smoke uses reduced resolution and steps. image_resolution, num_images, num_inference_steps, precision, scheduler, guidance_scale, and seed come from the runner.
6. At least 2 sample rows exist for the latest `run_id` (sweep coverage).
7. No sample has `status = 'error'`.
8. Runs scripts/infer_sdxl.py --backend sdxl. Every profile loads stabilityai/stable-diffusion-xl-base-1.0; smoke uses reduced resolution and steps. image_resolution, num_images, num_inference_steps, precision, scheduler, guidance_scale, and seed come from the runner.

### Baseline / Threshold configuration (`config/benchmark_config.yaml`)

Expected ranges and gates live in `config/benchmark_config.yaml` under `baselines:` or `thresholds:`. To update them, edit that file — never edit validation code directly.

Threshold key suffixes encode comparison direction when `thresholds:` is present: `_min` → observed value must be ≥ threshold. `_max` → observed value must be ≤ threshold. Informational `baselines:` ranges are not pass/fail gates.
