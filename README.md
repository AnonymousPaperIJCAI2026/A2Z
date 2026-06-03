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


## 📝 ACM Rebuttal Status

This repository is currently being updated for the ACM rebuttal period.
We are actively improving the reproducibility, protocol clarity, and supplementary experimental results of A2T.

### Current rebuttal-stage updates

* **Protocol clarification.**
  We clarify that A2T follows a continual **auxiliary-to-target zero-shot** protocol. The model is updated only on the auxiliary-domain stream, while target domains are strictly used for evaluation.

* **No target-domain adaptation.**
  No target-domain image, label, mask, validation set, or test sample is used for training, hyper-parameter selection, or model updating.

* **Auxiliary-domain stream.**
  The continual stream consists of sequentially arriving auxiliary domains. At step `t`, the model only accesses the current auxiliary dataset `A_t`.

* **Zero-shot definition.**
  The term **zero-shot** refers to zero-shot generalization to unseen target domains. It does not mean that the method is training-free, since auxiliary-domain supervision is used.

* **Additional evaluation metrics.**
  Following reviewer suggestions, we additionally report pixel-level AP and max-F1, besides image-level AUROC/AP and pixel-level AUROC/PRO.

* **Stage-wise continual evaluation.**
  We provide stage-wise results after each auxiliary-domain update to show how target-domain transferability changes during continual learning.

* **Baseline protocol clarification.**
  Training-free baselines such as WinCLIP are evaluated without adaptation. Adaptation-based baselines are evaluated under their corresponding single-adaptation or continual-extension settings.

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

### Example continual stream

One representative auxiliary-domain stream is:

```bash
MVTec -> MPDD -> DTD -> ClinicDB -> ISIC -> BrainMRI
```

The corresponding target probes are:

```bash
VisA, BTAD, DAGM, ColonDB, Kvasir, Br35H
```

The model is evaluated on these target probes after each auxiliary-domain update, but the target data are never used for optimization.

### Additional pixel-level metrics

| Method  | Domain          | pAUROC |   PRO |   pAP |   pF1 |
| ------- | --------------- | -----: | ----: | ----: | ----: |
| AF-CLIP | Industrial Avg. |  89.22 | 74.41 | 28.79 | 31.72 |
| AF-CLIP | Medical Avg.    |  93.34 | 77.35 | 75.30 | 69.86 |
| AF-CLIP | Overall Avg.    |  90.87 | 75.59 | 47.40 | 46.98 |
| A2T     | Industrial Avg. |  94.40 | 86.09 | 38.88 | 42.01 |
| A2T     | Medical Avg.    |  90.57 | 75.81 | 70.20 | 66.25 |
| A2T     | Overall Avg.    |  92.87 | 81.98 | 51.41 | 51.70 |

### Stage-wise retention

T1 denotes the performance immediately after learning the first auxiliary domain.
T6 denotes the performance after completing the full six-domain auxiliary stream.

| Method  | Earliest target | Image AUROC T1 | Image AUROC T6 |     Δ | Pixel AP T1 | Pixel AP T6 |     Δ | Pixel max-F1 T1 | Pixel max-F1 T6 |
| ------- | --------------- | -------------: | -------------: | ----: | ----------: | ----------: | ----: | --------------: | --------------: |
| AF-CLIP | VisA            |          88.48 |          87.42 | -1.06 |       26.95 |       19.28 | -7.67 |           33.61 |           23.75 |
| AF-CLIP | MVTec           |          92.35 |          91.71 | -0.64 |       47.15 |       37.30 | -9.85 |           47.81 |           39.58 |
| A2T     | VisA            |          87.90 |          87.64 | -0.25 |       26.96 |       25.66 | -1.31 |           33.60 |           32.04 |
| A2T     | MVTec           |          93.49 |          92.83 | -0.66 |       46.99 |       41.02 | -5.98 |           47.99 |           43.29 |

### Code release status

The codebase is being organized for rebuttal-stage reproducibility.
We are updating the following components:

* training scripts for continual auxiliary-domain learning;
* evaluation scripts for unseen target-domain probing;
* configuration files for auxiliary-target pair construction;
* logs and scripts for additional metrics, including pixel-level AP and max-F1;
* README instructions for dataset preparation and command-line usage.

A cleaner version with complete running commands and checkpoints will be updated during the rebuttal period.





## 🖼️ Visualization
<center><img src="images/fig3.png "width="70%"></center>
<center><img src="images/fig4.png "width="70%"></center>
<center><img src="images/fig5.png "width="70%"></center>

## 🙏 Acknowledgement
We borrow some codes from [OpenCLIP](https://github.com/mlfoundations/open_clip), and [April-GAN](https://github.com/ByChelsea/VAND-APRIL-GAN).

## 📬 Contact

If you have any problem with this code, please feel free to contact **** and ****.

