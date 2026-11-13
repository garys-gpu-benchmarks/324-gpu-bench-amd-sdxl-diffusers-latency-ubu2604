# Results: 324 Stable Diffusion XL Inference Baseline

**Main goal:** Measure SDXL image-generation performance  
**Tools:** Run scripts/infer_sdxl.py --backend sdxl with real SDXL in every profile; smoke uses reduced resolution and steps

Reference results measured by the maintainer on the system shown in each section (generator TEMPLATE_00_139 · recorded 2026-11-13). Your own runs write `results/RESULTS_ACTUAL.md` in the same format.

> Smoke runs use deliberately small sizes. They check that the workload installs and runs end to end; their numbers are not performance results. Baseline and extended are.

<!-- profile:smoke -->
## smoke

- **System:** 1× AMD Instinct MI300X VF (196288 MB) · INTEL(R) XEON(R) PLATINUM 8568Y+ · Ubuntu 26.04.1 LTS · kernel 7.0.0-38-generic · driver 7.1.9.31600000
- **Run:** 2026-11-13T14:24:16Z · status **PASS** (exit 0)
- **Time (mm:ss):** setup 01:15 · benchmark 00:21 · whole run 01:49
- **Source:** run 324_20261113_142416
- **Command:** `bash run_benchmark.sh --smoke --model-name sdxl-base-1.0 --precision fp16 --scheduler euler --prompt-source synthetic --image-resolution 64 --num-images 1 --num-inference-steps 2 --guidance-scale 0 --seed 42 --prompt-len 32 --batch-size 1`

| # | Metric | Result |
|---|---|---|
| 1 | Per-image latency (per_image_latency_sec) | 0.138453539000011 |
| 2 | Aggregate throughput (images_per_sec) | 7.222639502193732 |
| 3 | Peak GPU memory, GB (peak_gpu_memory_gb) | 8.038383616 |
| 4 | Denoising step time per batch, ms (step_time_ms) | 49.621886499835455 |
| 5 | First-image latency, cold, ms (first_image_latency_ms) | 18815.662369000165 |
<!-- /profile:smoke -->
