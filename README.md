<div align="left"> 
<h1> 📌 Lifelong Anomaly Perception: A2T </h1>
<h3>Lifelong Anomaly Perception: Continual Auxiliary to Target Zero-Shot Anomaly Detection</h3>
</div>


<div align="center"> <img src="images/fig1.png" width="60%"> </div>

<div align="justify">

## ⭐ Abstract 
Zero-shot anomaly detection based on vision--language models is typically studied under static settings, whereas real deployments involve sequential domain shifts and continual updates. This mismatch leads to degraded transferability and unstable anomaly localization under streaming adaptation. To address this issue, we propose A2T, a continual auxiliary-to-target zero-shot anomaly detection framework under the Lifelong Anomaly Perception (LAP) setting. The key idea is to decouple semantic transfer and structural stability during continual updates. Specifically, we introduce Stream-aware Continual Prompting (SCP) to preserve transferable anomaly semantics via anchor-conditioned prompt learning, and Structure-aware Continual Stabilization (SCS) to maintain boundary-aware local consistency through patch-consistent regularization and orthogonal-constrained parameter updates. These components are unified under a continual optimization objective that jointly balances semantic alignment, pixel-level supervision, and structural consistency. Extensive experiments on 12 industrial and medical benchmarks demonstrate that A2T consistently outperforms both static zero-shot methods and their streaming extensions, achieving state-of-the-art performance in image-level anomaly detection and pixel-level segmentation, while improving continual stability and boundary quality under domain streams.

</div>

📴**Keywords**: Zero-/Few-Shot, Cross domian, Large Vision-Language Model, Anomaly Classification and Segmentation

<div align="center"> <img src="images/fig2.png " width="100%"> </div>


## 📝 ACMMM2026 Rebuttal Status 👈

We thank the reviewers for their constructive comments. Following the reviewer’s suggestion, we have completed the rebuttal-stage updates for this repository. We have added protocol clarifications, supplementary evaluation results, stage-wise continual probes, and efficiency analyses to improve the reproducibility and clarity of A2T.

### Current rebuttal-stage updates

* **Protocol clarification.**
  We have clarified that A2T follows a continual **auxiliary-to-target zero-shot** protocol. The model is updated only on the auxiliary-domain stream, while target domains are strictly used for evaluation.

* **No target-domain adaptation.**
  We explicitly state that no target-domain image, label, mask, validation set, or test sample is used for training, hyper-parameter selection, or model updating.

* **Auxiliary-domain stream.**
  We have specified that the continual stream consists of sequentially arriving auxiliary domains. At step `t`, the model only accesses the current auxiliary dataset `A_t`.

* **Zero-shot definition.**
  We have clarified that **zero-shot** refers to zero-shot generalization to unseen target domains. It does not mean that the method is training-free, since auxiliary-domain supervision is used.

* **Additional evaluation metrics.**
  Following reviewer suggestions, we have added pixel-level AP and max-F1, as well as image-level max-F1, besides the original image-level AUROC/AP and pixel-level AUROC/PRO.

* **Stage-wise continual evaluation.**
  We now provide stage-wise results after each auxiliary-domain update to show how target-domain transferability changes during continual learning.

* **Baseline protocol clarification.**
  We have clarified that training-free baselines such as WinCLIP are evaluated without adaptation, while adaptation-based baselines are evaluated under their corresponding single-adaptation or continual-extension settings.

* **Efficiency and complexity analysis.**
  We have added model size, trainable parameters, inference speed, latency, and peak memory comparisons under the same inference protocol.


### Auxiliary-to-target protocol

The auxiliary and target domains are disjoint. Target domains are used only for evaluation.

| Super-domain | Auxiliary domain | Target domain |
| ------------ | ---------------- | ------------- |
| Industrial   | MVTec            | VisA          |
| Industrial   | VisA             | MVTec         |
| Industrial   | MPDD             | BTAD          |
| Industrial   | BTAD             | MPDD          |
| Industrial   | DTD              | DAGM          |
| Industrial   | DAGM             | DTD           |
| Medical      | ClinicDB         | ColonDB       |
| Medical      | ColonDB          | ClinicDB      |
| Medical      | ISIC             | Kvasir        |
| Medical      | Kvasir           | ISIC          |
| Medical      | BrainMRI         | Br35H         |
| Medical      | Br35H            | BrainMRI      |

### Stream-Ordering Protocol under Two-Fold Auxiliary–Target Swap

We evaluate three auxiliary-domain stream orders to test the robustness of continual zero-shot anomaly detection under different domain transition patterns. We adopt a two-fold auxiliary–target swap protocol. In each fold, the auxiliary stream is used for continual training, while the paired target probes are used only for test-time evaluation.

Importantly, Fold-1 and Fold-2 are independent runs initialized from the same pretrained model. Fold-2 is not a continuation of Fold-1; it only swaps the auxiliary/target roles of the dataset pairs to reduce directional bias.

#### Fold-1: Original auxiliary-to-target direction

| Order type | Auxiliary-domain stream | Corresponding target probes |
|---|---|---|
| **Family-grouped** | MVTec -> MPDD -> DTD -> ClinicDB -> ISIC -> BrainMRI | VisA -> BTAD -> DAGM -> ColonDB -> Kvasir -> Br35H |
| **Alternating** | MVTec -> ClinicDB -> MPDD -> ISIC -> DTD -> BrainMRI | VisA -> ColonDB -> BTAD -> Kvasir -> DAGM -> Br35H |
| **Distance-based** | MPDD -> MVTec -> DTD -> ISIC -> ClinicDB -> BrainMRI | BTAD -> VisA -> DAGM -> Kvasir -> ColonDB -> Br35H |

#### Fold-2: Role-swapped auxiliary-to-target direction

| Order type | Auxiliary-domain stream | Corresponding target probes |
|---|---|---|
| **Family-grouped** | VisA -> BTAD -> DAGM -> ColonDB -> Kvasir -> Br35H | MVTec -> MPDD -> DTD -> ClinicDB -> ISIC -> BrainMRI |
| **Alternating** | VisA -> ColonDB -> BTAD -> Kvasir -> DAGM -> Br35H | MVTec -> ClinicDB -> MPDD -> ISIC -> DTD -> BrainMRI |
| **Distance-based** | VisA -> BTAD -> DAGM -> Br35H -> ColonDB -> Kvasir | MVTec -> MPDD -> DTD -> BrainMRI -> ClinicDB -> ISIC |

For every run, only the current auxiliary domain is used for model updating at each stream step. The corresponding target probes are strictly held out and are never used for training, prompt tuning, hyper-parameter selection, or model updating.

### Stage-wise zero-shot probe matrix

To directly answer the reviewer’s question about “the performance after using source dataset A and the change after adding source dataset B,” we report stage-wise zero-shot evaluation after each auxiliary-domain update.

At each step, the model is trained only on the newly arriving auxiliary source. All listed target domains are held out and used only for evaluation. The columns `Avg. old-target Δ` report the average performance change on previously evaluated target domains, compared with their performance when they first appeared.

`N/A` indicates that the corresponding metric is not applicable, e.g., image-level metrics for medical segmentation datasets with only anomalous test images, or old-target change at the first step.

#### Fold-1: Stage-wise zero-shot probe results

Auxiliary stream:

MVTec -> ClinicDB -> MPDD -> ISIC -> DTD -> BrainMRI

| Step | Added source | New held-out target | New iAUROC | New pAP | New pF1 | New PRO | Avg. old ΔiAUROC | Avg. old ΔpAP | Avg. old ΔpF1 | Avg. old ΔPRO |
| ---- | ------------ | ------------------- | ---------: | ------: | ------: | ------: | ---------------: | ------------: | ------------: | ------------: |
| 1 | MVTec | VisA | 87.90 | 26.96 | 33.60 | 88.11 | N/A | N/A | N/A | N/A |
| 2 | ClinicDB | ColonDB | N/A | 63.82 | 60.43 | 82.61 | +0.22 | -1.63 | -1.72 | -1.59 |
| 3 | MPDD | BTAD | 94.67 | 38.53 | 44.36 | 74.82 | +0.24 | -0.86 | -1.08 | +0.46 |
| 4 | ISIC | Kvasir | N/A | 82.09 | 74.66 | 77.60 | -0.42 | -3.17 | -1.67 | -2.04 |
| 5 | DTD | DAGM | 98.11 | 53.95 | 54.76 | 93.61 | +0.34 | -2.50 | -1.70 | -0.71 |
| 6 | BrainMRI | Br35H | 99.55 | N/A | N/A | N/A | -0.07 | -3.39 | -2.27 | -1.58 |

#### Fold-2: Stage-wise zero-shot probe results

Auxiliary stream:

VisA -> ColonDB -> BTAD -> Kvasir -> DAGM -> Br35H

| Step | Added source | New held-out target | New iAUROC | New pAP | New pF1 | New PRO | Avg. old ΔiAUROC | Avg. old ΔpAP | Avg. old ΔpF1 | Avg. old ΔPRO |
| ---- | ------------ | ------------------- | ---------: | ------: | ------: | ------: | ---------------: | ------------: | ------------: | ------------: |
| 1 | VisA | MVTec | 93.49 | 46.99 | 47.99 | 85.85 | N/A | N/A | N/A | N/A |
| 2 | ColonDB | ClinicDB | N/A | 79.38 | 71.83 | 88.52 | -0.79 | -2.94 | -1.93 | -2.76 |
| 3 | BTAD | MPDD | 77.13 | 24.62 | 26.61 | 83.88 | -0.45 | -1.71 | -1.52 | -0.99 |
| 4 | Kvasir | ISIC | N/A | 83.80 | 77.37 | 82.91 | -0.19 | +0.63 | +0.95 | -0.25 |
| 5 | DAGM | DTD | 98.92 | 61.59 | 58.99 | 79.02 | -0.28 | -3.17 | -1.72 | -8.44 |
| 6 | Br35H | BrainMRI | 99.55 | N/A | N/A | N/A | +0.34 | -6.14 | -4.45 | -1.86 |

These results directly instantiate the reviewer’s I/M/J/N example: after learning each auxiliary source, we evaluate its related held-out target; after adding each new source, we report both the new target performance and the average change on previously evaluated targets. This makes the transfer-forgetting dynamics explicit without using any target-domain data for training.



#### Stage-wise source-update protocol and zero-shot results

`New-target score` reports the zero-shot performance on the newly paired target after updating with the current source dataset.  
`Avg. old-target Δ (vs. prev. step)` reports the average performance change on previously evaluated target probes after adding the current source, compared with the immediately preceding step. All values are reported in percentage points and averaged over applicable metrics.

##### Fold-1: MVTec -> ClinicDB -> MPDD -> ISIC -> DTD -> BrainMRI

| Step | New source dataset used for update | Source datasets seen so far | Held-out target probes evaluated | New target probe | New-target score | Avg. old-target Δ (vs. prev. step) |
|---:|---|---|---|---|---:|---:|
| 1 | MVTec | MVTec | VisA | VisA | 87.90/89.57, 96.13/26.96/33.60/88.11 | N/A |
| 2 | ClinicDB | MVTec, ClinicDB | VisA, ColonDB | ColonDB | 89.67/63.82/60.43/82.61 | -0.79 |
| 3 | MPDD | MVTec, ClinicDB, MPDD | VisA, ColonDB, BTAD | BTAD | 94.67/96.59, 92.77/38.53/44.36/74.82 | +0.23 |
| 4 | ISIC | MVTec, ClinicDB, MPDD, ISIC | VisA, ColonDB, BTAD, Kvasir | Kvasir | 95.23/82.09/74.66/77.60 | -1.22 |
| 5 | DTD | MVTec, ClinicDB, MPDD, ISIC, DTD | VisA, ColonDB, BTAD, Kvasir, DAGM | DAGM | 98.11/92.83, 97.18/53.95/54.76/93.61 | +0.17 |
| 6 | BrainMRI | MVTec, ClinicDB, MPDD, ISIC, DTD, BrainMRI | VisA, ColonDB, BTAD, Kvasir, DAGM, Br35H | Br35H | 99.55/99.54 | -0.65 |

##### Fold-2: VisA -> ColonDB -> BTAD -> Kvasir -> DAGM -> Br35H

| Step | New source dataset used for update | Source datasets seen so far | Held-out target probes evaluated | New target probe | New-target score | Avg. old-target Δ (vs. prev. step) |
|---:|---|---|---|---|---:|---:|
| 1 | VisA | VisA | MVTec | MVTec | 93.49/97.23, 92.44/46.99/47.99/85.85 | N/A |
| 2 | ColonDB | VisA, ColonDB | MVTec, ClinicDB | ClinicDB | 96.19/79.38/71.83/88.52 | -1.68 |
| 3 | BTAD | VisA, ColonDB, BTAD | MVTec, ClinicDB, MPDD | MPDD | 77.13/82.54, 96.11/24.62/26.61/83.88 | +0.03 |
| 4 | Kvasir | VisA, ColonDB, BTAD, Kvasir | MVTec, ClinicDB, MPDD, ISIC | ISIC | 92.95/83.80/77.37/82.91 | +0.76 |
| 5 | DAGM | VisA, ColonDB, BTAD, Kvasir, DAGM | MVTec, ClinicDB, MPDD, ISIC, DTD | DTD | 98.92/99.54, 98.18/61.59/58.99/79.02 | -2.63 |
| 6 | Br35H | VisA, ColonDB, BTAD, Kvasir, DAGM, Br35H | MVTec, ClinicDB, MPDD, ISIC, DTD, BrainMRI | BrainMRI | 99.55/99.64 | -0.40 |




### Additional image- and pixel-level metrics

Following reviewer suggestions, we additionally report pixel-level AP and max-F1, besides image-level AUROC/AP and pixel-level AUROC/PRO. We also include image-level max-F1 for a more complete classification evaluation.

🟩 denotes the best overall result among compared methods.

| Method      | Domain           | iAUROC |   iAP |   iF1 | pAUROC |   PRO |   pAP |   pF1 |
| ----------- | ---------------- | -----: | ----: | ----: | -----: | ----: | ----: | ----: |
| AnomalyCLIP | Industrial Avg.  |  63.74 | 68.69 | 76.83 |  72.73 | 36.72 |  6.25 |  9.28 |
| AnomalyCLIP | Medical Avg.     |  95.98 | 98.96 | 92.64 |  73.32 | 34.39 | 31.68 | 37.50 |
| **AnomalyCLIP** | **Overall Avg.** | **71.80** | **83.83** | **80.78** | **72.97** | **35.79** | **16.42** | **20.57** |
| MVFA-AD     | Industrial Avg.  |  86.43 | 89.91 | 88.44 |  92.26 | 78.60 | 36.74 | 39.80 |
| MVFA-AD     | Medical Avg.     |  99.35 | 99.33 | 97.96 |  92.91 | 80.76 | 72.01 | 69.34 |
| **MVFA-AD** | **Overall Avg.** | **89.66** | **92.27** | **90.82** | **92.52** | **79.46** | **50.85** | **51.62** |
| AF-CLIP     | Industrial Avg.  |  90.96 | 91.85 | 88.86 |  89.22 | 74.41 | 28.79 | 31.72 |
| AF-CLIP     | Medical Avg.     |  98.98 | 98.94 | 97.66 |  93.34 | 77.35 | 75.30 | 69.86 |
| **AF-CLIP** | **Overall Avg.** | **92.97** | **93.62** | **91.06** | **90.87** | **75.59** | **47.40** | **46.98** |
| A2T         | Industrial Avg.  |  91.84 | 93.32 | 90.08 |  94.40 | 86.09 | 38.88 | 42.01 |
| A2T         | Medical Avg.     |  99.55 | 99.59 | 97.99 |  90.57 | 75.81 | 70.20 | 66.25 |
| **A2T**     | **Overall Avg.** | 🟩 **93.77** | 🟩 **94.88** | 🟩 **92.06** | 🟩 **92.87** | 🟩 **81.98** | 🟩 **51.41** | 🟩 **51.70** |


A2T achieves the best overall results across all image-level and pixel-level metrics, indicating a better balance between image-level recognition and dense anomaly localization.

### Long-term retention summary

We further summarize the first-to-final retention over all 12 held-out target probes. “First” denotes the performance when a target domain is first evaluated after its paired auxiliary source is learned, and “Final” denotes the performance after completing the full auxiliary-domain stream.

| Method      | iAUROC First → Final |   ΔiAUROC | pAP First → Final |      ΔpAP | pF1 First → Final |      ΔpF1 | PRO First → Final |      ΔPRO |
| ----------- | -------------------: | --------: | ----------------: | --------: | ----------------: | --------: | ----------------: | --------: |
| AnomalyCLIP |        88.06 → 71.80 |    -16.26 |     48.12 → 16.42 |    -31.70 |     48.39 → 20.57 |    -27.82 |     75.46 → 35.79 |    -39.67 |
| MVFA-AD     |        91.99 → 89.66 |     -2.33 |     56.21 → 50.85 |     -5.36 |     55.77 → 51.62 |     -4.16 |     84.41 → 79.46 |     -4.94 |
| AF-CLIP     |        92.90 → 92.97 |     +0.07 |     58.76 → 47.40 |    -11.37 |     56.90 → 46.98 |     -9.92 |     84.31 → 75.59 |     -8.72 |
| **A2T**     |    **93.66 → 93.77** | **+0.10** | **56.17 → 51.41** | **-4.77** | **55.06 → 51.70** | **-3.36** | **83.69 → 81.98** | **-1.72** |

Compared with AF-CLIP and MVFA-AD, A2T shows smaller long-term degradation on imbalance-sensitive pixel-level AP, max-F1, and PRO, indicating better preservation of dense anomaly localization under continual auxiliary-domain updates.


## Efficiency and Complexity Analysis

To further evaluate the deployment cost of A2T, we report model size, trainable parameters, inference speed, latency, and peak GPU memory. All methods are evaluated under the same inference protocol and input resolution. For A2T, the CLIP backbone is kept frozen, and only lightweight prompt/adaptation modules are optimized during auxiliary-domain continual learning.

| Method | Params ↓ | Trainable ↓ | Speed ↑ | Latency ↓ | Peak Mem ↓ |
|---|---:|---:|---:|---:|---:|
| **A2T full** | **431.11M** | **3.17M / 0.735%** | **8.29 img/s** | **120.70 ms/img** | **4.33 GB** |
| AnomalyCLIP | 433.50M | 5.56M / 1.281% | 6.65 img/s | 150.29 ms/img | 4.87 GB |
| AdaptCLIP zero-shot | 428.55M | 0.61M / 0.142% | 6.61 img/s | 151.34 ms/img | 4.86 GB |

**Analysis.** A2T introduces only **3.17M** trainable parameters, accounting for **0.735%** of the full model parameters. Compared with AnomalyCLIP, A2T reduces the number of trainable parameters from **5.56M** to **3.17M**, while improving inference speed from **6.65 img/s** to **8.29 img/s** and reducing latency from **150.29 ms/img** to **120.70 ms/img**. A2T also requires lower peak GPU memory, using **4.33 GB** compared with **4.87 GB** for AnomalyCLIP and **4.86 GB** for AdaptCLIP.

These results show that the proposed continual auxiliary-to-target adaptation does not rely on heavy full-model fine-tuning. Instead, A2T keeps the large pretrained vision-language backbone frozen and performs efficient adaptation through lightweight trainable modules, achieving favorable efficiency while preserving strong zero-shot transferability and continual stability.

**Measurement details.** Speed and latency are measured during inference. Peak memory denotes the maximum GPU memory usage under the same evaluation setting. Data loading time is excluded from the reported inference speed and latency.

### Code release status

The codebase is being organized for rebuttal-stage reproducibility.
We are updating the following components:

* training scripts for continual auxiliary-domain learning;
* evaluation scripts for unseen target-domain probing;
* configuration files for auxiliary-target pair construction;
* logs and scripts for additional metrics, including pixel-level AP and max-F1;
* README instructions for dataset preparation and command-line usage.

A cleaner version with complete running commands and checkpoints will be updated during the rebuttal period.

## 🚀 Get Started

⚙️ Environment
- python >= 3.8.5
- pytorch >= 1.10.0
- torchvision >= 0.11.1
- numpy >= 1.19.2
- scipy >= 1.5.2
- kornia >= 0.6.1
- pandas >= 1.1.3
- opencv-python >= 4.5.4
- pillow
- tqdm
- ftfy
- regex

### Device
Single NVIDIA A40 GPU

## 📦 Pretrained model
- CLIP: ##################################################################

    👉 Download and put it under `CLIP/ckpt` folder



## 🏥🏭 Medical and Industrial Anomaly Detection Benchmark(2D\3D)

1. We will provide the pre-processed benchmark. Please download the following dataset

    

2. Place it within the master directory `data` and unzip the dataset.

    ```
    
    ```


## 📂 File Structure
After the preparation work, the whole project should have the following structure:

```
code
├─ ckpt
│  └─ zero-shot
├─ CLIP
│  ├─ bpe_simple_vocab_16e6.txt.gz
│  ├─ ckpt
│  │  └─ ViT-L-14-336px.pt
│  ├─ clip.py
│  ├─ model.py
│  ├─ models.py
│  ├─ model_configs
│  │  └─ ViT-L-14-336.json
│  ├─ modified_resnet.py
│  ├─ openai.py
│  ├─ tokenizer.py
│  └─ transformer.py
├─ data
│  ├─ Mvtec3D
│  │  ├─ valid
│  │  └─ test
│  ├─ BrainMRI
│  │  ├─ valid
│  │  └─ test
│  ├─ Mvtec
│  │  ├─ valid
│  │  └─ test
│  ├─ ...
│  └─ Visa
│     ├─ valid
│     └─ test
├─ dataset
│  ├─ fewshot_seed
│  │  └─ Mvtec3D
│  ├─ medical_few.py
│  └─ medical_zero.py
├─ loss.py
├─ readme.md
├─ train_few.py
├─ train_zero.py
├─ test.py
└─ utils.py

```


## ⚡ Quick Start

`python test.py`

For example, to test on the BrainMRI , simply run:

`###########################`

### Training

`python train_zero.py`



## 🖼️ Visualization
<center><img src="images/fig3.png "width="70%"></center>
<center><img src="images/fig4.png "width="70%"></center>
<center><img src="images/fig5.png "width="70%"></center>

## 🙏 Acknowledgement
We borrow some codes from [OpenCLIP](https://github.com/mlfoundations/open_clip), and [April-GAN](https://github.com/ByChelsea/VAND-APRIL-GAN).

## 📬 Contact

If you have any problem with this code, please feel free to contact **** and ****.

