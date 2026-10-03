# Checkpoints

Model weights are intentionally excluded from Git. Each selected Swin-L checkpoint is approximately 868 MB and will be distributed separately.

The preset inference wrapper recognizes:

- `best_overall_b1_lambda003.pth`
- `best_day_b0_zero_event.pth`
- `best_night_b4_sr025.pth`

Place downloaded weights in this directory, or pass an explicit path through `--weights` when using `scripts/infer.sh`.

These validation-selected checkpoints are separate models. They are not the three historical weights used by the official 41.11 composite challenge submission; those historical assets are not currently distributed.

Selection details are documented in [`SELECTED_CHECKPOINTS.md`](SELECTED_CHECKPOINTS.md).
