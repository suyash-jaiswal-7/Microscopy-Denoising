# Microscopy Image Denoising Using Deep Learning

A deep learning-based approach to reducing noise in fluorescence microscopy images while preserving important structural details.

## Overview

Microscopy images often contain noise that can affect the visibility and analysis of cellular structures. The goal of this project is to explore deep learning-based image denoising techniques that can recover cleaner images from noisy microscopy inputs.

We experiment with different neural network architectures and loss functions, evaluate their performance quantitatively, and compare their visual outputs.

The project currently explores three approaches:

- **E3 – Standard U-Net**
- **E4 – U-Net with Edge Loss**
- **E5 – DnCNN (Denoising Convolutional Neural Network)**

## Dataset

This project uses the **AI4Life-MDC25-Nuclei** dataset.

- Dataset: [AI4Life-MDC25-Nuclei](https://zenodo.org/records/15624741)
- Source: Zenodo
- Image type: Grayscale microscopy images
- Image dimensions: 256 × 256 pixels
- Image format: TIFF
- Image data type: 16-bit unsigned integer (`uint16`)

The `small_images` subset is used for the current experiments.

Each noisy image has a corresponding ground-truth (GT) image.

### Dataset Structure

```text
data/
└── Archive/
    └── small_images/
        ├── noisy/
        └── gt/
```

## Data Preprocessing

The preprocessing pipeline includes:

1. Loading noisy and corresponding ground-truth image pairs.
2. Matching image filenames to ensure correct input-target pairs.
3. Normalizing image intensities using training-subset percentile-based scaling.
4. Creating reproducible training, validation and test splits.

### Dataset Split

| Split | Percentage | Number of images |
|---|---:|---:|
| Training | 70% | 1,719 |
| Validation | 15% | 369 |
| Testing | 15% | 369 |
| **Total** | **100%** | **2,457** |

The split uses `random_state=42` for reproducibility.

The normalization scales used in the current experiments are:

- Noisy images: 78
- Ground-truth images: 2252

## Experiments

### E3 – Standard U-Net

A conventional encoder-decoder U-Net architecture with skip connections.

**Configuration:**
- Input channels: 1
- Output channels: 1
- Encoder-decoder architecture
- Double convolution blocks with ReLU activation
- Max pooling for downsampling
- Transposed convolutions for upsampling
- Loss function: L1 Loss
- Optimizer: Adam
- Learning rate: 0.001
- Training epochs: 10

### E4 – U-Net with Edge Loss

This experiment investigates whether incorporating edge information into the loss function can improve the preservation of structural details.

The same U-Net architecture is trained with a combined loss:

`Total Loss = L1 Loss + λ × Edge Loss`

Where:

- L1 Loss measures pixel-level reconstruction error.
- Edge Loss measures differences in horizontal and vertical image gradients.
- λ = 0.1

The model is initialized and trained independently.

### E5 – DnCNN

DnCNN is a convolutional neural network designed for image denoising through residual noise learning.

Instead of directly predicting the clean image, the network learns to estimate the noise component.

The denoised image is obtained as:

`Denoised Image = Noisy Image − Predicted Noise`

**Configuration:**
- 17 convolutional layers
- 64 feature channels
- Batch Normalization in intermediate layers
- ReLU activation
- Loss function: Mean Squared Error (MSE)
- Optimizer: Adam
- Learning rate: 0.001
- Training epochs: 10

## Evaluation Metrics

The models are evaluated on the held-out test set using:

- **PSNR (Peak Signal-to-Noise Ratio):** Measures reconstruction quality in terms of signal-to-noise ratio.
- **SSIM (Structural Similarity Index):** Measures structural similarity between the denoised output and ground truth.

Both metrics are calculated for all 369 test images and averaged across the test set.

## Experimental Results

### Quantitative Comparison

| Experiment | PSNR (dB) ↑ | SSIM ↑ |
|---|---:|---:|
| E3 – Standard U-Net | **33.5645** | **0.94884** |
| E4 – U-Net + Edge Loss | 33.1809 | 0.94486 |
| E5 – DnCNN | 32.4976 | 0.92415 |

*Results are from the current experimental setup and shared test split.*

### Initial Observations

- Standard U-Net achieved the highest PSNR and SSIM among the three evaluated experiments.
- Adding Edge Loss did not improve these two metrics under the current configuration.
- DnCNN demonstrated noise reduction, although some smoothing of image details was observed in qualitative comparisons.

These observations are specific to the current training configuration and dataset split. Further experiments are needed before drawing broader conclusions.

## Repository Structure

```text
Microscopy-Denoising/
│
├── notebooks/
│   ├── 03_unet_model_E3_E4.ipynb
│   ├── 05_dncnn_model.ipynb
│   └── 06_results_analysis.ipynb
│
├── outputs/
│   ├── splits/
│   │   ├── train.csv
│   │   ├── validation.csv
│   │   └── test.csv
│   └──
│
├── .gitignore
└── README.md
```

The notebooks contain the model implementations, training workflows, evaluations and comparative analysis.

## Running the Project

The experiments are organized as Jupyter notebooks and can be executed using Google Colab or a local Python environment with GPU support.

General workflow:

1. Clone this repository.
2. Download the dataset from the [Zenodo dataset page](https://zenodo.org/records/15624741).
3. Extract the dataset and configure the data paths as required by the notebooks.
4. Run the experiment notebooks in the appropriate order.
5. Run the results analysis notebook to compare model performance.

The notebooks contain the experiment-specific configurations and implementation details.

## Future Work

Potential directions for further experimentation include:

- Exploring additional CNN-based denoising architectures.
- Investigating transformer-based approaches.
- Improving preservation of fine cellular structures.
- Conducting more extensive hyperparameter tuning.
- Performing more robust evaluation across different acquisition conditions.
- Comparing generalization performance on independent data.

## Project Status

**Ongoing Research / Experimental Project**

This repository documents the implementation and evaluation of different deep learning approaches for microscopy image denoising. More experiments and comparative analyses may be added as the project progresses.
