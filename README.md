# Egocentric-Native Compression

Compressing head-mounted factory video for Physical AI. 15 approaches tested exhaustively. One works: **pixel binning**.

## Results

| Configuration | 8hr Size | Compression | Upload (1K @ 500Mbps) |
|---|---|---|---|
| Current product (456x256 30fps) | 2,071 MB | 1x | 9.2 hours |
| **2x2 Pixel Binning, CRF 28, 10-bit** | **823 MB** | **2.5x** | **3.7 hours** |
| **4x4 Pixel Binning, CRF 23, 10-bit** | **423 MB** | **4.9x** | **1.9 hours** |

## What Doesn't Work (14 approaches)

- Neural Super-Resolution (ESRGAN, SwinIR) — +0.21 dB, marginal
- IMU Motion Prediction — explains 1% of pixel motion
- ROI QP Maps (Zipstream-style) — 43% larger than uniform CRF
- Background Prior Compositing — camera moves too much
- Chroma Separation — larger than full-color H.265
- Per-Shot Adaptive CRF — I-frame overhead kills savings
- Global Motion Compensation — 12% larger than raw H.265
- Temporal Noise Reduction — 2.8% saving
- Adaptive GOP Length — 1.3% saving
- IMU Adaptive Frame Dropping — 17% theoretical, not worth complexity
- VQ-VAE Patch Codebook — 17.9 dB, too low quality
- Cycle-Aware Compression — workers not periodic at signal level
- Background Subtraction — 5% gain, codec already handles it
- Motion-Based ROI Detection — targets wrong regions

## Why

H.265 is already extremely optimized internally. The only lever external to the codec is **reducing the pixel count** before encoding. The device (ARM Cortex-A76 + H.265 HW encoder) has no GPU/NPU for neural approaches.

## Dataset

100 workers from [Egocentric-100K](https://huggingface.co/datasets/builddotai/Egocentric-100K). Download:
```bash
gsutil -m cp -r gs://build-ai-egocentric-native-compression/worker_001 .
```

## Website

View the visual comparisons and full results at the [project page](https://egocentric-compression.onrender.com).

## Organization

- `scripts/` — All experiment scripts
- `results/` — Experiment outputs and measurements
- `papers/` — Reference research papers
- `CLAUDE.md` — Comprehensive project documentation

Built by [Build AI](https://www.egocentric.org/compression).
