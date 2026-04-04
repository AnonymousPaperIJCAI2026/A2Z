<div align="left"> 
<h1> 📌 SADC-Net </h1>
<h3>SADC-Net: Cross-Domain Anomaly Detection with Semantic Anchors and Drift Control</h3>
</div>


<div align="center"> <img src="images/1.png" width="60%"> </div>

<div align="justify">

## ⭐ Abstract 
Cross-domain anomaly detection is critical in real-world inspection, where a model adapted on an auxiliary domain must generalize to unseen target domains while producing reliable pixel-level anomaly maps. However, adapting vision–language models in this setting often suffers from two issues: (i) alignment drift and score mis-calibration, which destabilize the semantic reference of normality and degrade cross-domain detection; and (ii) a localization gap under domain shift, where dense similarity-based maps become noisy and inconsistent. We propose ContinueCLIP, a parameter-efficient framework that preserves the pre-trained cross-modal geometry by freezing the CLIP backbone, while enabling robust transfer via lightweight trainable components. ContinueCLIP stabilizes zero-shot calibration with shared semantic anchors, absorbs domain variations with minimal perturbation, and enhances dense localization through multi-level token aggregation coupled with a consistency constraint. When a few normal target samples are available, an optional non-parametric memory further improves score stability.

</div>

📴**Keywords**: Zero-/Few-Shot, Cross domian, Large Vision-Language Model, Anomaly Classification and Segmentation

<div align="center"> <img src="images/2.png " width="100%"> </div>


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
<center><img src="images/3.png "width="70%"></center>
<center><img src="images/4.png "width="70%"></center>
<center><img src="images/5.png "width="70%"></center>

## 🙏 Acknowledgement
We borrow some codes from [OpenCLIP](https://github.com/mlfoundations/open_clip), and [April-GAN](https://github.com/ByChelsea/VAND-APRIL-GAN).

## 📬 Contact

If you have any problem with this code, please feel free to contact **** and ****.

