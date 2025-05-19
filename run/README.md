# Label Generation & Scene Understanding

## Overview

If you want to test the computed features in the interactive demo or generate semantic labels for your custom dataset you can follow the instructions below. You can generate results for either either the fusion, distill or ensemble method as outlined in the paper.

## Compute Features

Before computing the features for either fusion distill or ensemble method, first you have to obtain the multi-view fused features for your custom dataset. If you have not already, follow the [feature fusion instruction](./scripts/feature_fusion/README.md).

Then you can simply run the process to compute the features based on either either fusion, distill or ensemble method:
´´´bash
sh run/inference.sh \
       EXP_NAME \
       CONFIG.yaml \
       SCENE_NAME \
       METHOD
´´´
where:
- `EXP_NAME`: the directory where the computed multi-view fused feature for this scene is saved
- `CONFIG.yaml `:should be ´custom_openseg_pretrained.yaml´
- `SCENE_NAME `: the name of the scene
- `METHOD`: either fusion, distill or ensemble

For example:
´´´bash
sh run/inference.sh ~/openscene/data/custom_multiview_openseg/swiss-village config/custom/custom_openseg_pretrained.yaml swiss-village fusion
´´´

## Interactive Demo

Now you should find the computed features in form of a .npy file inside ´/openscene/data/custom_multiview_openseg/saved_features´
Rename this file to RP_SCENE_NAME (e.g. RP_swiss-village) and move it to the 
To now run the interactive demo:
- set up the demo following [this instruction](./demo).
- now place the renamed .npy file inside ´demo/openscene_features´
- inside ´demo/region_segmentations´ there should already be a corresponding .ply file that was created during the [custom Data Preprocessing]
- Now, make sure you are under ´demo/´, and you can simply run to have fun with the interactive demo:

´´´bash
./run_demo
´´´


## Generate Semantic Labels

If you want to generate semantic labels for each point in the point cloud make sure to adjust the ´CUSTOM_LABELS_18´ and the ´CUSTOM_COLOR_MAP_18´ inside [`label_constants.py`](./label_constants.py) to your specific usecase or dataset, as it will define the classes used to segment the model. 

You can find the results inside ´/openscene/data/custom_multiview_openseg/SCENE_NAME´ in form of a .ply file.






