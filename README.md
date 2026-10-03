<div align="center">

# EventShift

**Reliability-Aware Residual Adaptation for Day–Night RGB–Event Semantic Segmentation**

Shao-Kai Liu<sup>1</sup> · Yun-Tze Tsai<sup>1</sup> · Chia-Yu Lin<sup>2</sup> · Chia-Ming Lee<sup>1,2</sup> · Chih-Chung Hsu<sup>1,2</sup>

<sup>1</sup> National Yang Ming Chiao Tung University · <sup>2</sup> National Cheng Kung University

[Project page](https://eventshift-seg.github.io/) · [Paper](https://eventshift-seg.github.io/assets/paper.pdf) · Checkpoints coming soon

</div>

<p align="center">
  <a href="https://eventshift-seg.github.io/">
    <img src="https://eventshift-seg.github.io/assets/method-1600.webp" alt="EventShift architecture" width="1000">
  </a>
</p>

## Overview

EventShift keeps RGB as the semantic anchor and introduces event evidence as a reliability-aware, bounded residual correction. The correction is controlled at three levels:

- **When:** an illumination-conditioned budget opens more capacity as RGB visibility weakens.
- **Where:** density, temporal balance, polarity balance, support, and multi-window edges guide spatial allocation.
- **How much:** RMS normalization and clipping cap the correction relative to the RGB feature scale.

On the controlled CoSEC19 study, EventShift improves nighttime performance by **+4.58 mIoU** while changing daytime performance by only **−0.16 mIoU**. The adapter adds **0.47M parameters** (0.22% of the 215.93M-parameter model). Full results, challenge context, figures, and limitations are available on the [project page](https://eventshift-seg.github.io/#results).

## Repository status

This repository contains the training, inference, evaluation, dataset preparation, event representation, and challenge reconstruction utilities used by the EventShift workspace.

- The paper-aligned Mask2Former pipeline is driven by `tools/training/`, `tools/export/`, `configs/`, and the vendored backends in `third_party/`.
- `eventshift/` also contains a compact native reference implementation for smoke tests and follow-up experiments; it is not a drop-in replacement for the paper's Swin-L Mask2Former model.
- Datasets and checkpoints are intentionally excluded from Git. Public checkpoint downloads are being prepared separately.
- The official 41.11 challenge result used a separate composite system. The corresponding rebuild recipe is included, but it requires external historical checkpoints and submission artifacts that are not distributed here.

## Installation

The reference environment uses Python 3.10, PyTorch 2.6, and CUDA 12.4.

```bash
git clone https://github.com/eventshift-seg/EventShift.git
cd EventShift

conda env create -f environment.yml
conda activate ebmv_seg

pip install --no-build-isolation -e third_party/detectron2
pip install -r third_party/Mask2Former/requirements.txt

cd third_party/Mask2Former/mask2former/modeling/pixel_decoder/ops
sh make.sh
cd ../../../../../..
```

If your CUDA or PyTorch version differs, rebuild Detectron2 and Multi-Scale Deformable Attention in that environment. Precompiled native modules are not committed.

## Data preparation

Datasets remain outside the repository. Paths can be supplied as command-line arguments or environment variables; `/data` is only the default convention.

```text
/data/
├── cosec/
│   ├── train/
│   └── test/<sequence>/img_co_left/
├── dsec/
├── acdc/
├── ddd17/
├── manifests/
└── splits/cosec/
```

Create CoSEC split files with:

```bash
bash scripts/prepare_data.sh \
  --cosec-root /path/to/cosec/train \
  --split-dir /path/to/splits/cosec
```

See [`data/README.md`](data/README.md) for the full layout and path overrides.

## Checkpoints

Checkpoints are not stored in Git because each Swin-L model is approximately 868 MB. After downloading a checkpoint, place it under `checkpoints/` or pass an explicit `--weights` path.

The local presets expect these filenames:

| Preset | Expected checkpoint | Purpose |
| --- | --- | --- |
| `overall` | `best_overall_b1_lambda003.pth` | selected overall validation model |
| `day` | `best_day_b0_zero_event.pth` | selected daytime validation model |
| `night` | `best_night_b4_sr025.pth` | selected nighttime validation model |

These are separate validation-selected models, not the composite official challenge submission. See [`checkpoints/README.md`](checkpoints/README.md) for the precise status.

## Inference

Once a preset checkpoint and CoSEC test data are available:

```bash
# Overall preset
bash scripts/run_inference.sh \
  --test-root /path/to/cosec/test \
  --out-dir outputs/overall

# Day or night preset
bash scripts/run_inference.sh night \
  --test-root /path/to/cosec/test \
  --out-dir outputs/night \
  --device cuda:0
```

For an arbitrary checkpoint, use the lower-level entry point:

```bash
bash scripts/infer.sh \
  --model mask2former \
  --variant best_overall_b1_lambda003 \
  --weights /path/to/model.pth \
  --test-root /path/to/cosec/test \
  --out-dir outputs/custom \
  --execute
```

Without `--execute`, `scripts/infer.sh` prints the fully resolved backend command. This is useful for checking configuration and paths before a GPU run.

## Training

Prepare the event manifest and splits first, then launch the paper-aligned Mask2Former trainer:

```bash
bash scripts/train.sh \
  --model mask2former \
  --variant best_overall_b1_lambda003 \
  --cosec-root /path/to/cosec/train \
  --cosec-manifest /path/to/cosec_train_bidir_50ms.json \
  --num-gpus 1 \
  --execute
```

The configuration stack is compositional:

```text
configs/eventshift/
├── base.yaml
├── models/                 # backend selection
├── variants/               # experiment-specific overrides
└── recipes/                # multi-model reconstruction recipes
```

## Evaluation

Evaluate indexed PNG predictions against ground-truth masks:

```bash
bash scripts/eval.sh \
  --pred outputs/custom \
  --gt /path/to/labels \
  --num-classes 19
```

The evaluator reports pooled-confusion mIoU and the number of matched images.

## Repository layout

```text
.
├── eventshift/             # native modules, event representation, config utilities
├── configs/                # EventShift, Mask2Former, and SegFormer configurations
├── tools/                  # training, inference, export, evaluation, and data tools
├── scripts/                # stable command-line entry points
├── third_party/            # vendored Mask2Former, Detectron2, and MMSegmentation code
├── data/                   # external-data documentation only
├── checkpoints/            # ignored weights plus documentation
├── environment.yml
└── requirements.txt
```

## Citation

```bibtex
@inproceedings{liu2026eventshift,
  title     = {EventShift: Reliability-Aware Residual Adaptation for Day--Night RGB--Event Semantic Segmentation},
  author    = {Liu, Shao-Kai and Tsai, Yun-Tze and Lin, Chia-Yu and Lee, Chia-Ming and Hsu, Chih-Chung},
  booktitle = {ECCV Workshop on Event-Based and Multimodal Vision (EBMV)},
  year      = {2026}
}
```

## License and acknowledgements

EventShift is released under the [MIT License](LICENSE). Vendored components retain their original licenses under their respective `third_party/` directories. This project builds on [Mask2Former](https://github.com/facebookresearch/Mask2Former), [Detectron2](https://github.com/facebookresearch/detectron2), and [MMSegmentation](https://github.com/open-mmlab/mmsegmentation).
