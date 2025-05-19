# Multi-view Feature Fusion

## Overview

Here we provide instructions for multi-view feature fusion on different dataset, including ScanNet, Matterport3D, nuScenes, and Replica. This corresponds to **Section 3.1** in our [paper](https://arxiv.org/abs/2211.15654). 

**Note**: Here we provide only codes for multiview fusion with the **[OpenSeg](https://github.com/tensorflow/tpu/tree/master/models/official/detection/projects/openseg)** model. For LSeg per-pixel feature extraction and multi-view fusion, Check **[this repo](https://github.com/pengsongyou/lseg_feature_extraction)**. If you are using a custom dataset, please refer to [Customized Dataset](#customized-dataset) for guidance.


## Prerequisites

### Data preprocessing
Follow [this instruction](../preprocess/README.md) to obtain the processed 2D and 3D data.
- **3D**: Point clouds in the pytorch format `.pth`
- **2D**: RGB-D images with their intrinsic and extrinsic parameters

### Environment
You can simply activate the `openscene` conda environment, or alternatively, make sure the following package installed:
- `torch`
- `tensorflow v2` (for OpenSeg feature extraction)
- `numpy`
- `imageio`
- `tqdm`

To use **OpenSeg** as the feature extractor, you can either take the demo model inside the jupyter notebook from their [official repo](https://github.com/tensorflow/tpu/tree/master/models/official/detection/projects/openseg), or download from [here](https://drive.google.com/file/d/1DgyH-1124Mo8p6IUJ-ikAiwVZDDfteak/view?usp=sharing).

## Run the code

Take ScanNet as an example, to perform multi-view feature fusion your can run:
```bash
python scannet_openseg.py \
            --data_dir PATH/TO/scannet_processed \
            --output_dir PATH/TO/OUTPUT_DIR \
            --openseg_model PATH/TO/OPENSEG_MODEL \
            --process_id_range 0,100\
            --split train
```

where:
- `data_dir`: path to the pre-processed 2D&3D data from [here](../../openscene#datasets)
- `output_dir`: output directory to save your fused features
- `openseg_model`: path to the OpenSeg model
- `process_id_range`: only process scenes within the range
- `split`: choose from `train`/`val`/`test` data to process

[`replica_openseg.py`](./replica_openseg.py) does not have `process_id_range` and `split`.

## Customized Dataset
For your own customized dataset, one can use the provided codes as a reference. We provide [`custom_openseg.py`](./custom_openseg.py) as reference which can be used or modified for custom datasets, especially if you only care about inferecing your own point clouds without 3D distillation. If you want to do 3D distillation, please check and modify [scannet_openseg.py](./scannet_openseg.py). Alternatively you can also use [`replica_openseg.py`](./replica_openseg.py) as a base.

### Run custom_openseg.py
To run the code on your custom data, activate the environment as mentioned above and run:
```bash
python custom_openseg.py \
            --data_dir PATH/TO/preprocessed/SCENE_NAME \
            --output_dir PATH/TO/OUTPUT_DIR \
            --openseg_model PATH/TO/OPENSEG_MODEL \
```

For example:
```bash
python custom_openseg.py \
            --data_dir ~/openscene/data/preprocessed/swiss-village \
            --output_dir ~/openscene/data/custom_multiview_openseg/swiss-village \
            --openseg_model ~/openscene/models/openseg_clip
```

### Important:
This script assumes that the input images have a resolution of 1920 (width) × 1280 (height).
Since this OpenSeg model only supports images of size 640 × 640, each image is split into 6 patches, which are processed individually to avoid losing information through downscaling. The patches are then reassembled into a full feature map.
If your images have a different resolution:

- 1. Make sure to update ´img_dim´ in the script.

- 2. Modify extract_openseg_img_feature_split in [`fusion_util.py`](./fusion_util.py) accordingly

## Suggestions & Troubleshooting

- For using the OpenSeg model, you need to make sure you have an NVIDIA GPU with **>30G memory**.
- For custom datasets with high resolution images greater than 1920 x 1280 you might want to do some additional memory optimization

For additional help, please refer to the code documentation or contact the author.