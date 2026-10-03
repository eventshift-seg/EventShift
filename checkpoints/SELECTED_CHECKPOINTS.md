# Selected checkpoints

| Role | Run | Validation mIoU | Checkpoint | Matching config |
| --- | --- | ---: | --- | --- |
| Overall | B1 lambda=0.003, no reliability | 55.3249 | best_overall_b1_lambda003.pth | configs/selected/best_overall_b1_lambda003.yaml |
| Day | B0 zero-event | 55.1158 | best_day_b0_zero_event.pth | configs/selected/best_day_b0_zero_event.yaml |
| Night | B4 SR025 structural reliability | 51.5028 | best_night_b4_sr025.pth | configs/selected/best_night_b4_sr025.yaml |

All three are selected from their saved validation-best checkpoints. They are separate models, not the missing official 0.4111 b75 submission checkpoints.
