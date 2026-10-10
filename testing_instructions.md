# Testing Instructions for MFSE-MobileNetV2

This document describes how to prepare model inputs, load the trained weights,
and run inference with MFSE-MobileNetV2. The repository provides the model
definition and trained state dictionary for review and research use.

## 1. Required files

Place the following files in the same directory:

```text
model.py
MFSE-MobileNetV2_best.pth
testing_instructions.md
```

## 2. Environment

The model requires Python and the following packages:

```text
torch
torchvision
numpy
```

Install the required packages using:

```bash
pip install torch torchvision numpy
```

## 3. Model inputs

The model accepts three inputs:

1. A co-registered image tensor with shape `(N, 5, 32, 32)`, where `N` is
   the batch size. The five channels must be arranged in the following order:

   ```text
   green, red, red-edge, near-infrared, nDSM
   ```

2. One normalized leaf area index (LAI) value for each image patch.

3. One encoded phenological-stage value for each image patch.

The LAI and phenological-stage tensors may have shape `(N,)` or `(N, 1)`.

## 4. Input preprocessing

All input layers must be spatially co-registered. Each multisource image patch
must be resized to 32 × 32 pixels before inference.

The nDSM channel is normalized separately within each image patch:

```text
nDSM_normalized = (nDSM - nDSM_min) / (nDSM_max - nDSM_min)
```

After the five channels have been stacked, each channel is standardized using:

```text
image_normalized = (image - 0.485) / 0.229
```

The LAI value is normalized using:

```text
LAI_normalized = (LAI - 0.3) / 5.7
```

The normalized LAI value was rounded to two decimal places during dataset
preparation.

The numerical phenological-stage code is encoded using:

```text
phenology_encoded = phenology_code / 10
```

The encoded phenological-stage value was also rounded to two decimal places.
The same preprocessing rules used during training must be applied to test
samples. Parameters must not be refitted using the test data.

## 5. Loading the weights and running inference

The following example assumes that the three required files are in the current
working directory and that the inputs have already been preprocessed.

```python
import torch

from model import MFSEMobileNetV2


device = torch.device("cuda" if torch.cuda.is_available() else "cpu")

model = MFSEMobileNetV2(num_channels=5).to(device)

state_dict = torch.load(
    "MFSE-MobileNetV2_best.pth",
    map_location=device,
)
model.load_state_dict(state_dict, strict=True)
model.eval()

# Example batch containing one preprocessed sample.
# Replace these tensors with the actual preprocessed test inputs.
image = torch.zeros((1, 5, 32, 32), dtype=torch.float32, device=device)
lai = torch.tensor([0.50], dtype=torch.float32, device=device)
phenology = torch.tensor([0.60], dtype=torch.float32, device=device)

with torch.no_grad():
    prediction = model(image, lai, phenology)

print("Predicted AGB:", prediction.squeeze().item())
```

The example values `0.50` and `0.60` represent an already normalized LAI value
and an already encoded phenological-stage value, respectively. They are not raw
field measurements.

## 6. Output

The model returns one above-ground biomass (AGB) prediction for each input
sample.  The prediction is in the same unit as the training target, reported as g/m² in the manuscript.

## 7. Notes

- Use `model.eval()` during testing.
- Run inference inside `torch.no_grad()`.
- Do not change the order of the five image channels.
- Do not refit the preprocessing parameters using test samples.
- Ensure that the model definition and weight file are from the same release.

For questions about the model or unavailable test data, contact:

Zhe Liu  
College of Land Science and Technology  
China Agricultural University  
Email: liuz@cau.edu.cn
