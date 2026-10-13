# Brain MRI Tumor Segmentation

U-Net with a pretrained ResNet-34 encoder for tumor segmentation on the LGG Brain MRI dataset.

## Dataset
[LGG MRI Segmentation (Mateusz Buda / TCGA)](https://www.kaggle.com/datasets/mateuszbuda/lgg-mri-segmentation): 3,929 MRI slice and mask pairs from 110 patients with low-grade glioma. About 2% of pixels are tumor, so the data is heavily imbalanced. The dataset is not included in this repo; download it from Kaggle.

## Method
- **Model:** U-Net, ResNet-34 encoder pretrained on ImageNet (`segmentation-models-pytorch`)
- **Loss:** 0.5 × BCE + 0.5 × Dice
- **Augmentation:** horizontal/vertical flip, random 90° rotation, shift-scale-rotate, brightness/contrast, Gaussian noise (Albumentations)
- **Training:** AdamW (lr 1e-3), cosine annealing, batch size 16, 40 epochs, FP16 mixed precision, Google Colab T4 GPU
- **Input:** 256 × 256 grayscale

## Results (test set)
| Metric | Score |
|---|---|
| Dice | 0.7792 |
| IoU | 0.6382 |
| AUC-ROC | 0.9958 |

Dice and IoU are computed over all pixels pooled across the test set, not per image. AUC-ROC is inflated by the many easy background pixels, so Dice and IoU are more informative.

## Limitations
- **The split is a random slice-level split, not patient-level.** Slices from the same patient can appear in train, validation and test, so these scores may be optimistic. A patient-level split is planned.
- The model sometimes produces false positives on tumor-free slices and can miss very small tumors.
- Only 2D slices are used, with no 3D context.
- Single run with a fixed seed (42), no ablation study.

## How to run
1. Download the dataset from Kaggle and upload it to Google Drive.
2. Open `Brain_MRI_dataset.ipynb` in Google Colab with a GPU runtime.
3. Update the dataset path, then run all cells.

## Next steps
- Patient-level split and re-evaluation
- Attention gates in the skip connections
- Test-time augmentation and connected-component post-processing

## Libraries
PyTorch, segmentation-models-pytorch, Albumentations, OpenCV, NumPy, scikit-learn, Matplotlib
