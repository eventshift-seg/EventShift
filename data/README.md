# External data

Datasets are not copied into this repository. The scripts default to `/data`, but every path can be overridden through command-line arguments or environment variables.

## Inference layout

The preset inference wrapper expects:

```text
/data/cosec/test/<sequence>/img_co_left/
```

For example:

```text
/data/cosec/test/Day_Campus_012/img_co_left/
```

Run inference after the data and checkpoint are available:

```bash
bash scripts/run_inference.sh
```

The default output is `/data/predictions/eventshift/overall/`.

## Optional training data

Training, split generation, and extended experiments may use:

```text
/data/cosec/train/
/data/dsec/
/data/acdc/
/data/ddd17/
/data/manifests/cosec_train_bidir_50ms.json
/data/splits/cosec/
```

Create the CoSEC split files with:

```bash
bash scripts/prepare_data.sh --cosec-root /data/cosec/train
```

## Path overrides

Paths can be changed without editing the source:

```bash
TEST_ROOT=/other/cosec/test bash scripts/run_inference.sh
COSEC_ROOT=/other/cosec/train bash scripts/prepare_data.sh --cosec-root /other/cosec/train
EVENTSHIFT_COSEC_SPLIT_DIR=/other/splits bash scripts/prepare_data.sh --cosec-root /data/cosec/train
```
