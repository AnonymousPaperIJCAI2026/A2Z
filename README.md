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

## Reviewer Navigation Map

| GitHub ID | Reviewer concern | Section / Table | Main takeaway |
|---|---|---|---|
| GH-1 | LAP protocol and target access | Table GH-1 | LAP is auxiliary-supervised but target-zero-shot. |
| GH-2 | Stage-wise performance after each source | Tables GH-2a / GH-2b | Shows zero-shot probes after every auxiliary update. |
| GH-3 | Old-target forgetting trajectory | Tables GH-3a / GH-3b | Shows old-target changes after adding new sources. |
| GH-4 | Additional AP/F1 metrics | Table GH-4 | Reports iF1, pAP, and pF1. |
| GH-5 | First-to-final retention | Table GH-5 | Compares long-term drops across methods. |
| GH-6 | LAP vs. CAD | Table GH-6 | Clarifies data-access and evaluation differences. |
| GH-7 | Stream-order sensitivity | Tables GH-7a / GH-7b | Compares Family-grouped, Alternating, and Distance-based orders. |
| GH-8 | SCS basis construction and storage | Table GH-8 | Reports SVD/QR basis update and replay-free storage. |
| GH-9 | Efficiency and complexity | Table GH-9 | Reports parameters, speed, latency, and memory. |
| GH-10 | Continual-learning baselines | Tables GH-10a / GH-10b | Reports EWC and LwF/KD under the same LAP stream. |

## 📝 ACMMM2026 Rebuttal Status 👈
We thank the reviewers for their constructive comments. Following their suggestions, we have updated this repository to clarify the continual auxiliary-to-target zero-shot protocol and provide additional analyses for easier verification. Specifically, we clarify the data-access constraints of LAP, add stage-wise source-update evaluations and two-fold old-target trajectories, report additional image-/pixel-level F1 and AP metrics, and include analyses on stream order, long-term retention, baseline fairness, implementation details, and efficiency. These updates aim to make the A2T setting, evaluation protocol, and deployment cost clearer.

### Current rebuttal-stage updates

* **Protocol clarification.**  
  We clarify that A2T follows a continual **auxiliary-to-target zero-shot** protocol. The model is updated only on the auxiliary-domain stream, while target domains are strictly used for zero-shot evaluation.

* **No target-domain adaptation.**  
  No target-domain image, label, mask, validation set, test sample, prompt tuning, hyper-parameter tuning, or model selection is used for training or model updating.

* **Auxiliary-domain stream.**  
  The continual stream consists of sequentially arriving auxiliary/source domains. At step `t`, the model only accesses the current auxiliary dataset `A_t`; previous auxiliary images/masks are not replayed.

* **Zero-shot definition.**  
  Here, **zero-shot** means zero-shot generalization to unseen target domains. It does not mean training-free inference, since auxiliary-domain supervision is used during continual learning.

* **Relation to CAD.**  
  We add a discussion comparing Lifelong Anomaly Perception (LAP) with Continual Anomaly Detection (CAD), highlighting their differences in data access, target-domain exposure, and evaluation goal.

* **Stage-wise continual evaluation.**  
  We provide stage-wise results after each auxiliary-domain update to show how target-domain transferability changes during continual learning.

* **Additional evaluation metrics.**  
  Following reviewer suggestions, we report pixel-level AP and max-F1, as well as image-level max-F1, in addition to the original image-level AUROC/AP and pixel-level AUROC/PRO.

* **Baseline protocol clarification.**  
  We clarify that training-free baselines such as WinCLIP are evaluated without adaptation, while adaptation-based baselines are evaluated under their corresponding single-adaptation or continual-extension settings.

* **Stream-order analysis.**  
  We include results under multiple auxiliary-domain stream orders to analyze the sensitivity of LAP to task/domain ordering.

* **Basis construction and overhead.**  
  We provide implementation details for the historical activation basis used in SCS, including SVD-based direction selection, QR-based re-orthogonalization, rank cap, and storage overhead.

* **Efficiency and complexity analysis.**  
  We report model size, trainable parameters, inference speed, latency, and peak memory under the same inference protocol.


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


## Relation to Continual Anomaly Detection

We thank the reviewer for pointing out the connection between Lifelong Anomaly Perception (LAP) and Continual Anomaly Detection (CAD). LAP is related to CAD in that both study anomaly detection under sequential domain/task changes. However, LAP targets a different data-access protocol and evaluation goal. Most CAD methods focus on preserving or adapting anomaly detection performance over observed task/domain streams, whereas LAP couples continual auxiliary-domain learning with target-zero-shot transfer to unseen domains.

The key difference is that LAP does not continually adapt on target domains. Instead, labeled auxiliary/source domains arrive sequentially, and target domains are strictly held out for zero-shot evaluation. Thus, LAP evaluates not only anti-forgetting over the auxiliary stream, but also whether the learned anomaly semantics and local structures remain transferable to unseen target domains.


| Setting | Training data | Target access | Goal | Evaluation |
|---|---|---|---|---|
| Static zero-shot AD | One fixed auxiliary source or pretrained model | No target training data | Transfer once to unseen targets | Final zero-shot performance |
| Continual Anomaly Detection (CAD) | Sequential observed task/domain stream | Observed domains/tasks are used for continual adaptation | Preserve or adapt AD performance over seen domain/task streams | Performance/forgetting on observed or previously seen tasks |
| LAP | Sequential auxiliary/source domains only | No target-side training or tuning; target domains are evaluation-only | Combine continual auxiliary-domain learning with target-zero-shot transfer | Final zero-shot performance and stage-wise zero-shot probing on strictly disjoint target domains, plus Forgetting/BWT |

This distinction is important because a method that performs well in CAD may still rely on target/task exposure during continual adaptation, while LAP requires the model to remain target-zero-shot throughout the auxiliary stream. In our protocol, each auxiliary dataset is visited once, previous auxiliary images/masks are not replayed, and all target datasets are used only for evaluation. Therefore, LAP provides a complementary benchmark to CAD: it measures whether continual learning improves long-term zero-shot anomaly transfer rather than only preserving performance on observed domains.


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


### Stage-wise zero-shot probe matrix and old-target trajectory

To directly answer the reviewer’s question about “the performance after using source dataset A and the change after adding source dataset B,” we report stage-wise zero-shot evaluation after each auxiliary-domain update.

At each step, the model is trained only on the newly arriving auxiliary source. All listed target domains are held out and used only for evaluation. No target-domain images are used for training, prompt tuning, hyper-parameter selection, or model updating.

In the probe matrix, `Avg. old-target Δ` reports the average performance change on previously evaluated target domains, compared with their performance when they first appeared. This measures cumulative retention/forgetting after each new auxiliary source is added.

`N/A` indicates that the corresponding metric is not applicable, e.g., image-level metrics for medical segmentation datasets with only anomalous test images, or old-target change at the first step.

For compactness, the probe matrix reports image-level iAUROC and pixel-level pAP/pF1/PRO. The following trajectory tables further provide full iAUROC/iAP and pAUROC/pAP/pF1/PRO changes for representative old targets in the two-fold swap setting.

#### Fold-1: Stage-wise zero-shot probe results

Auxiliary stream:

```text
MVTec -> ClinicDB -> MPDD -> ISIC -> DTD -> BrainMRI
````

| Step | Added source | New held-out target | New iAUROC | New pAP | New pF1 | New PRO | Avg. old ΔiAUROC | Avg. old ΔpAP | Avg. old ΔpF1 | Avg. old ΔPRO |
| ---- | ------------ | ------------------- | ---------: | ------: | ------: | ------: | ---------------: | ------------: | ------------: | ------------: |
| 1    | MVTec        | VisA                |      87.90 |   26.96 |   33.60 |   88.11 |              N/A |           N/A |           N/A |           N/A |
| 2    | ClinicDB     | ColonDB             |        N/A |   63.82 |   60.43 |   82.61 |            +0.22 |         -1.63 |         -1.72 |         -1.59 |
| 3    | MPDD         | BTAD                |      94.67 |   38.53 |   44.36 |   74.82 |            +0.24 |         -0.86 |         -1.08 |         +0.46 |
| 4    | ISIC         | Kvasir              |        N/A |   82.09 |   74.66 |   77.60 |            -0.42 |         -3.17 |         -1.67 |         -2.04 |
| 5    | DTD          | DAGM                |      98.11 |   53.95 |   54.76 |   93.61 |            +0.34 |         -2.50 |         -1.70 |         -0.71 |
| 6    | BrainMRI     | Br35H               |      99.55 |     N/A |     N/A |     N/A |            -0.07 |         -3.39 |         -2.27 |         -1.58 |

#### Fold-2: Stage-wise zero-shot probe results

Auxiliary stream:

```text
VisA -> ColonDB -> BTAD -> Kvasir -> DAGM -> Br35H
```

| Step | Added source | New held-out target | New iAUROC | New pAP | New pF1 | New PRO | Avg. old ΔiAUROC | Avg. old ΔpAP | Avg. old ΔpF1 | Avg. old ΔPRO |
| ---- | ------------ | ------------------- | ---------: | ------: | ------: | ------: | ---------------: | ------------: | ------------: | ------------: |
| 1    | VisA         | MVTec               |      93.49 |   46.99 |   47.99 |   85.85 |              N/A |           N/A |           N/A |           N/A |
| 2    | ColonDB      | ClinicDB            |        N/A |   79.38 |   71.83 |   88.52 |            -0.79 |         -2.94 |         -1.93 |         -2.76 |
| 3    | BTAD         | MPDD                |      77.13 |   24.62 |   26.61 |   83.88 |            -0.45 |         -1.71 |         -1.52 |         -0.99 |
| 4    | Kvasir       | ISIC                |        N/A |   83.80 |   77.37 |   82.91 |            -0.19 |         +0.63 |         +0.95 |         -0.25 |
| 5    | DAGM         | DTD                 |      98.92 |   61.59 |   58.99 |   79.02 |            -0.28 |         -3.17 |         -1.72 |         -8.44 |
| 6    | Br35H        | BrainMRI            |      99.55 |     N/A |     N/A |     N/A |            +0.34 |         -6.14 |         -4.45 |         -1.86 |

These results directly instantiate the reviewer’s source-update question: after learning each auxiliary source, we evaluate its related held-out target; after adding each new source, we report both the new target performance and the average change on previously evaluated targets. This makes the transfer-forgetting dynamics explicit without using any target-domain data for training.

---

#### Two-fold old-target trajectory after each source update

To further show the direct effect of adding each new source dataset, we report representative old-target trajectories in both folds. Unlike the probe matrix above, here `Δ vs. prev. step` is computed as the score after adding the current source minus the score after the immediately preceding source update.

##### Fold-1 old-target example: VisA

In Fold-1, MVTec is first used as the source and VisA is evaluated as the paired target. From Step 2 onward, VisA becomes an old target, and the table reports how its performance changes after each newly added source dataset.

| Step | New source added | Source datasets seen so far                | Target probe |  iAUROC/iAP | Δ iAUROC/iAP vs. prev. step |      pAUROC/pAP/pF1/PRO | Δ pAUROC/pAP/pF1/PRO vs. prev. step |
| ---: | ---------------- | ------------------------------------------ | ------------ | ----------: | --------------------------: | ----------------------: | ----------------------------------: |
|    1 | MVTec            | MVTec                                      | VisA         | 87.90/89.57 |                         N/A | 96.13/26.96/33.60/88.11 |                                 N/A |
|    2 | ClinicDB         | MVTec, ClinicDB                            | VisA         | 88.11/89.73 |                 +0.22/+0.16 | 95.97/25.34/31.88/86.52 |             -0.16/-1.63/-1.72/-1.59 |
|    3 | MPDD             | MVTec, ClinicDB, MPDD                      | VisA         | 88.14/89.84 |                 +0.02/+0.11 | 96.02/25.29/31.76/87.69 |             +0.06/-0.05/-0.12/+1.16 |
|    4 | ISIC             | MVTec, ClinicDB, MPDD, ISIC                | VisA         | 88.25/89.94 |                 +0.12/+0.10 | 96.01/24.72/31.05/87.72 |             -0.02/-0.57/-0.72/+0.03 |
|    5 | DTD              | MVTec, ClinicDB, MPDD, ISIC, DTD           | VisA         | 88.44/90.03 |                 +0.18/+0.09 | 95.94/24.68/30.81/87.40 |             -0.07/-0.04/-0.24/-0.32 |
|    6 | BrainMRI         | MVTec, ClinicDB, MPDD, ISIC, DTD, BrainMRI | VisA         | 87.64/89.03 |                 -0.80/-1.00 | 96.04/25.66/32.04/88.62 |             +0.10/+0.97/+1.23/+1.22 |

##### Fold-2 old-target example: MVTec

In Fold-2, the source-target role is swapped: VisA is first used as the source and MVTec is evaluated as the paired target. From Step 2 onward, MVTec becomes an old target, and the table reports its change after adding each new source dataset.

| Step | New source added | Source datasets seen so far              | Target probe |  iAUROC/iAP | Δ iAUROC/iAP vs. prev. step |      pAUROC/pAP/pF1/PRO | Δ pAUROC/pAP/pF1/PRO vs. prev. step |
| ---: | ---------------- | ---------------------------------------- | ------------ | ----------: | --------------------------: | ----------------------: | ----------------------------------: |
|    1 | VisA             | VisA                                     | MVTec        | 93.49/97.23 |                         N/A | 92.44/46.99/47.99/85.85 |                                 N/A |
|    2 | ColonDB          | VisA, ColonDB                            | MVTec        | 92.70/96.67 |                 -0.79/-0.56 | 91.31/44.05/46.06/83.10 |             -1.12/-2.94/-1.93/-2.76 |
|    3 | BTAD             | VisA, ColonDB, BTAD                      | MVTec        | 93.03/96.84 |                 +0.34/+0.17 | 91.66/45.27/46.63/83.53 |             +0.35/+1.22/+0.57/+0.43 |
|    4 | Kvasir           | VisA, ColonDB, BTAD, Kvasir              | MVTec        | 92.07/96.44 |                 -0.96/-0.39 | 90.53/41.13/43.46/82.84 |             -1.13/-4.14/-3.17/-0.69 |
|    5 | DAGM             | VisA, ColonDB, BTAD, Kvasir, DAGM        | MVTec        | 92.65/96.58 |                 +0.57/+0.14 | 90.88/40.30/42.66/75.55 |             +0.35/-0.83/-0.80/-7.28 |
|    6 | Br35H            | VisA, ColonDB, BTAD, Kvasir, DAGM, Br35H | MVTec        | 92.83/96.56 |                 +0.18/-0.03 | 91.46/41.02/43.29/83.76 |             +0.58/+0.72/+0.63/+8.21 |

Together, the probe matrix and two old-target trajectories answer the reviewer’s concern from two complementary views. The probe matrix reports the stage-wise performance after each newly added auxiliary source and the cumulative average change on old targets. The trajectory tables show the direct step-to-step change of a previously evaluated target after each new source is added. Both folds confirm that the model is updated only on auxiliary sources, while targets remain held out for zero-shot evaluation.

**Analysis.** The matrix directly clarifies that LAP performs continual updates on source/auxiliary datasets, not on target/test data. For instance, in Fold-1, after training only on MVTec, the held-out VisA target obtains 87.90 iAUROC and 26.96/33.60/88.11 pAP/pF1/PRO. After adding the next source ClinicDB, the model is updated only on ClinicDB, while VisA remains test-only; its change is +0.22 iAUROC and -1.63/-1.72/-1.59 pAP/pF1/PRO. The later steps repeat this evaluation for all old targets, making the transfer-forgetting dynamics explicit. The Fold-2 MVTec trajectory further verifies the same protocol under source-target role swap. Thus, all target domains remain zero-shot probes, and the reported pAP/pF1 metrics address the reviewer’s pixel-level evaluation concern.





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

| Method | Aux train epochs | Params ↓ | Trainable ↓ | Speed ↑ | Latency ↓ | Peak Mem ↓ |
|---|---:|---:|---:|---:|---:|---:|
| **A2T full** | **2 / aux** | **431.11M** | **3.17M / 0.735%** | **8.29 img/s** | **120.70 ms/img** | **4.33 GB** |
| AnomalyCLIP | 15 / aux | 433.50M | 5.56M / 1.281% | 6.65 img/s | 150.29 ms/img | 4.87 GB |
| AdaptCLIP zero-shot | 15 / aux | 428.55M | 0.61M / 0.142% | 6.61 img/s | 151.34 ms/img | 4.86 GB |
| AdaptCLIP few-shot/full (1-shot) | 15 / aux | 430.52M | 1.76M / 0.410% | 6.61 img/s | 151.26 ms/img | 5.01 GB |

**Analysis.** A2T uses only **2 epochs per auxiliary domain**, while the compared adaptation-based baselines use **15 epochs per auxiliary domain**, indicating a substantially lower continual-update cost. A2T introduces **3.17M** trainable parameters, accounting for only **0.735%** of the full model parameters, while keeping the CLIP backbone frozen. Although AdaptCLIP zero-shot has fewer trainable parameters, A2T achieves better deployment efficiency under the same inference protocol, improving inference speed from **6.65 img/s** for AnomalyCLIP and **6.61 img/s** for AdaptCLIP to **8.29 img/s**, and reducing latency from about **150 ms/img** to **120.70 ms/img**. A2T also uses the lowest peak GPU memory among the compared methods, requiring **4.33 GB** compared with **4.87 GB** for AnomalyCLIP, **4.86 GB** for AdaptCLIP zero-shot, and **5.01 GB** for AdaptCLIP few-shot/full. These results show that A2T does not rely on heavy full-model fine-tuning; instead, it performs efficient continual auxiliary-domain adaptation through lightweight trainable modules while maintaining favorable inference cost.

These results show that the proposed continual auxiliary-to-target adaptation does not rely on heavy full-model fine-tuning. Instead, A2T keeps the large pretrained vision-language backbone frozen and performs efficient adaptation through lightweight trainable modules, achieving favorable efficiency while preserving strong zero-shot transferability and continual stability.

**Measurement details.** Speed and latency are measured during inference. Peak memory denotes the maximum GPU memory usage under the same evaluation setting. Data loading time is excluded from the reported inference speed and latency.


## Continual Adaptation Performance and Forgetting Analysis with EWC and LwF/KD

### Performance Metrics

| Method | Domain | iAUROC | iAP | iF1 | pAUROC | PRO | pAP | pF1 |
|---|---|---:|---:|---:|---:|---:|---:|---:|
| EWC | Industrial Avg. | 89.29 | 91.52 | 88.40 | 93.38 | 82.47 | 37.04 | 40.17 |
| EWC | Medical Avg. | 99.22 | 99.23 | 97.65 | 91.91 | 81.71 | 75.95 | 70.52 |
| **EWC** | **Overall Avg.** | **91.78** | **93.45** | **90.71** | **92.79** | **82.17** | **52.60** | **52.31** |
| LwF/KD | Industrial Avg. | 90.94 | 91.82 | 88.64 | 95.16 | 86.61 | 42.78 | 44.83 |
| LwF/KD | Medical Avg. | 99.47 | 99.56 | 97.87 | 93.46 | 83.31 | 76.86 | 71.69 |
| **LwF/KD** | **Overall Avg.** | **93.07** | **93.75** | **90.94** | **94.48** | **85.29** | **56.41** | **55.57** |
| A2T | Industrial Avg. | 91.84 | 93.32 | 90.08 | 94.40 | 86.09 | 38.88 | 42.01 |
| A2T | Medical Avg. | 99.55 | 99.59 | 97.99 | 90.57 | 75.81 | 70.20 | 66.25 |
| **A2T** | **Overall Avg.** | **93.77** | **94.88** | **92.06** | **92.87** | **81.98** | **51.41** | **51.70** |

### Forgetting and BWT Metrics

| Method | F_iAUROC | BWT_iAUROC | F_iAP | BWT_iAP | F_pAUROC | BWT_pAUROC | F_PRO | BWT_PRO |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| EWC | 1.43 | -1.61 | 0.96 | -0.56 | 2.77 | -2.52 | 4.40 | -2.43 |
| LwF/KD | **0.31** | 0.07 | 0.56 | 0.00 | **0.96** | -0.81 | **1.37** | **0.33** |
| A2T | 0.42 | **0.13** | **0.47** | **0.27** | 2.13 | -1.82 | 3.87 | -1.72 |

### Efficiency and Complexity Metrics

| Method | Total Params (M) | Trainable Params (M) | Trainable (%) | Inference Speed (img/s) | Inference Time (ms/img) | Peak Inference Memory (GB) | Extra Training State |
|---|---:|---:|---:|---:|---:|---:|---|
| EWC | 431.11 | 3.17 | 0.735 | 8.29 | 120.70 | 4.33 | Fisher + parameter means |
| LwF/KD | 431.11 | 3.17 | 0.735 | 8.29 | 120.70 | 4.33 | Teacher model during training |
| A2T | 431.11 | 3.17 | 0.735 | 8.29 | 120.70 | 4.33 | None |

A2T achieves the best overall classification performance, with the highest iAUROC, iAP, and iF1 scores. LwF/KD performs better on segmentation-oriented metrics and shows the lowest forgetting on most metrics, suggesting stronger retention through teacher distillation. However, LwF/KD requires an additional teacher model during training, while EWC stores Fisher information and parameter means. In contrast, A2T uses the same inference architecture and does not introduce extra continual-learning state, offering a favorable trade-off between classification performance, forgetting control, and training-time complexity.

### Code release status

The codebase is being organized for reproducibility. We are preparing the following components:

* training scripts for continual auxiliary-domain learning;
* evaluation scripts for unseen target-domain probing;
* configuration files for auxiliary-target pair construction;
* logs and scripts for additional metrics, including pixel-level AP and max-F1;
* README instructions for dataset preparation and command-line usage.

To ensure a clean and reproducible release, the complete codebase, running commands, and checkpoints will be publicly released upon acceptance.

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

