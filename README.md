# MFSE-MobileNetV2-model-weight
Model weights and testing instructions for MFSE-MobileNetV2
# MFSE-MobileNetV2 Model Weights

This repository provides the trained model weights, normalization parameters,
and testing instructions for MFSE-MobileNetV2, a lightweight network developed
for maize above-ground biomass estimation using multisource UAV data and
agronomic priors.

## Files

- `MFSE-MobileNetV2_best.pth`: Trained model state dictionary.
- `Parameters.json`: Parameters used during training.
- `testing_instructions.md`: Instructions for preparing inputs and testing the model.

## Model inputs

The model requires the following inputs:

1. A co-registered 32 × 32-pixel image patch with five channels:
   - Green
   - Red
   - Red-edge
   - Near-infrared
   - Digital surface model

2. A normalized leaf area index value.

3. An encoded phenological-stage value.

The channel order must be:

`green, red, red-edge, near-infrared, DSM`

## Testing

Instantiate the MFSE-MobileNetV2 architecture described in Section 2.3 of the
manuscript and load the supplied state dictionary. Set the model to evaluation
mode and perform inference without gradient calculation.

## Data availability

The field observations and processed UAV data are available from the
corresponding author upon reasonable request because collaborating institutions
are still using part of the dataset.

## Contact

Zhe Liu  
College of Land Science and Technology  
China Agricultural University  
Email: liuz@cau.edu.cn
