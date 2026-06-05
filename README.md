<div align="left"> 
<h1> 📌 Lifelong Anomaly Perception: A2T </h1>
<h3>Lifelong Anomaly Perception: Continual Auxiliary to Target Zero-Shot Anomaly Detection</h3>
</div>


<div align="center"> <img src="images/fig1.png" width="60%"> </div>

<div align="justify">

## ⭐ Abstract 
Zero-shot anomaly detection based on vision--language models is typically studied under static settings, whereas real deployments involve sequential domain shifts and continual updates. This mismatch leads to degraded transferability and unstable anomaly localization under streaming adaptation. To address this issue, we propose A2T, a continual auxiliary-to-target zero-shot anomaly detection framework under the Lifelong Anomaly Perception (LAP) setting. The key idea is to decouple semantic transfer and structural stability during continual updates. Specifically, we introduce Stream-aware Continual Prompting (SCP) to preserve transferable anomaly semantics via anchor-conditioned prompt learning, and Structure-aware Continual Stabilization (SCS) to maintain boundary-aware local consistency through patch-consistent regularization and orthogonal-constrained parameter updates. These components are unified under a continual optimization objective that jointly balances semantic alignment, pixel-level supervision, and structural consistency. Extensive experiments on 12 industrial and medical benchmarks demonstrate that A2T consistently outperforms both static zero-shot methods and their streaming extensions, achieving state-of-the-art performance in image-level anomaly detection and pixel-level segmentation, while improving continual stability and boundary quality under domain streams.

</div>

📴**Keywords**: Zero-/Few-Shot Anomaly Detection, Cross-Domain Generalization, Vision-Language Models, Continual Learning


<div align="center"> <img src="images/fig2.png " width="100%"> </div>


## 📝 ACMMM2026 Rebuttal-stage Evidence 👈

We thank the reviewers for their constructive comments. Following their suggestions, this repository is organized as a reviewer-facing evidence page. Some items are described in the submitted rebuttal as planned manuscript revisions, while the corresponding reviewer-facing evidence has already been provided here for verification. Specifically, we have provided the stage-wise source-update evaluations, old-target trajectories, additional AP/F1 metrics, long-term retention summary, LAP-vs-CAD comparison, stream-order analysis, SCS implementation details, efficiency results, and replay-free continual-learning baselines, including EWC and LwF/KD results under the same LAP stream.

**Protocol reminder.** LAP is auxiliary-supervised but target-zero-shot: the model is continually updated only on sequentially arriving auxiliary/source domains, while target domains are strictly held out and used only for zero-shot evaluation. No target image, label, mask, validation set, test sample, prompt tuning, hyper-parameter tuning, or model selection is used.

---

## Reviewer Navigation Map

| GitHub ID | Reviewer concern | Section / Table | Main takeaway |
|---|---|---|---|
| GH-1 | LAP protocol and target access | [Table GH-1](#gh-1-lap-protocol-and-target-access) | **Provided:** auxiliary-target split and target-zero-shot access constraints. |
| GH-2 | Stage-wise performance after each source | [Tables GH-2a / GH-2b](#gh-2-stage-wise-zero-shot-probe-matrix) | **Provided:** zero-shot probes after every auxiliary update. |
| GH-3 | Old-target forgetting trajectory | [Tables GH-3a / GH-3b](#gh-3-old-target-forgetting-trajectory) | **Provided:** old-target changes after adding new sources. |
| GH-4 | Additional AP/F1 metrics | [Table GH-4](#gh-4-additional-apf1-metrics) | **Provided:** image max-F1, pixel AP, and pixel max-F1. |
| GH-5 | First-to-final retention | [Table GH-5](#gh-5-first-to-final-retention) | **Provided:** long-term retention over 12 held-out target probes. |
| GH-6 | LAP vs. CAD | [Table GH-6](#gh-6-relation-to-continual-anomaly-detection) | **Provided:** data-access and evaluation-goal comparison with CAD. |
| GH-7 | Stream-order sensitivity | [Tables GH-7a / GH-7b](#gh-7-stream-order-sensitivity) | **Provided:** Family-grouped, Alternating, and Distance-based stream-order results. |
| GH-8 | SCS basis construction and storage | [Table GH-8](#gh-8-scs-basis-construction-and-storage) | **Provided:** SVD/QR basis update, energy threshold, rank cap, and replay-free storage. |
| GH-9 | Efficiency and complexity | [Table GH-9](#gh-9-efficiency-and-complexity-analysis) | **Provided:** parameters, trainable ratio, speed, latency, and peak memory. |
| GH-10 | Continual-learning baselines | [Tables GH-10a--GH-10c](#gh-10-continual-learning-baselines) | **Provided:** EWC and LwF/KD results under the same LAP stream. |



---

## Current rebuttal-stage updates

* **Protocol clarification.**  
  See [GH-1](#gh-1-lap-protocol-and-target-access). We clarify that A2T follows a continual auxiliary-to-target zero-shot protocol. The model is updated only on the auxiliary-domain stream, while target domains are strictly used for zero-shot evaluation.

* **Stage-wise continual evaluation.**  
  See [GH-2](#gh-2-stage-wise-zero-shot-probe-matrix) and [GH-3](#gh-3-old-target-forgetting-trajectory). We report stage-wise results after each auxiliary-domain update and old-target trajectories across the stream.

* **Additional evaluation metrics.**  
  See [GH-4](#gh-4-additional-apf1-metrics). Following reviewer suggestions, we report pixel-level AP and max-F1, as well as image-level max-F1.

* **Long-term retention.**  
  See [GH-5](#gh-5-first-to-final-retention). We summarize first-to-final performance changes over all 12 held-out target probes.

* **Relation to CAD.**  
  See [GH-6](#gh-6-relation-to-continual-anomaly-detection). We compare Lifelong Anomaly Perception with Continual Anomaly Detection and clarify their differences in data access and evaluation goal.

* **Stream-order analysis.**  
  See [GH-7](#gh-7-stream-order-sensitivity). We report multiple auxiliary-domain stream orders to analyze the sensitivity of LAP to domain-transition order.

* **Basis construction and overhead.**  
  See [GH-8](#gh-8-scs-basis-construction-and-storage). We provide implementation details for the historical activation basis used in SCS.

* **Efficiency and complexity.**  
  See [GH-9](#gh-9-efficiency-and-complexity-analysis). We report model size, trainable parameters, inference speed, latency, and peak memory.

* **Continual-learning baselines.**  
  See [GH-10](#gh-10-continual-learning-baselines). We include replay-free EWC and LwF/KD baselines under the same LAP stream.

---

<a id="gh-1-lap-protocol-and-target-access"></a>

## GH-1. LAP Protocol and Target Access

**Reviewer concern addressed:**  
Does A2T adapt to target/test data? Are auxiliary and target domains clearly separated?

**Answer.**  
A2T is trained only on the sequentially arriving auxiliary/source domains. Target domains are strictly held out and used only for zero-shot evaluation. No target-domain image, label, mask, validation set, test sample, prompt tuning, hyper-parameter tuning, or model selection is used.

**Table GH-1. Auxiliary-target split and access protocol.**

| Super-domain | Auxiliary domain | Target domain | Target access |
| ------------ | ---------------- | ------------- | ------------- |
| Industrial   | MVTec            | VisA          | Evaluation only |
| Industrial   | VisA             | MVTec         | Evaluation only |
| Industrial   | MPDD             | BTAD          | Evaluation only |
| Industrial   | BTAD             | MPDD          | Evaluation only |
| Industrial   | DTD              | DAGM          | Evaluation only |
| Industrial   | DAGM             | DTD           | Evaluation only |
| Medical      | ClinicDB         | ColonDB       | Evaluation only |
| Medical      | ColonDB          | ClinicDB      | Evaluation only |
| Medical      | ISIC             | Kvasir        | Evaluation only |
| Medical      | Kvasir           | ISIC          | Evaluation only |
| Medical      | BrainMRI         | Br35H         | Evaluation only |
| Medical      | Br35H            | BrainMRI      | Evaluation only |

**Key takeaway.**  
LAP is auxiliary-supervised but target-zero-shot. Auxiliary labels/masks are used for continual learning, while target domains are never used for training or model selection.

---

<a id="gh-2-stage-wise-zero-shot-probe-matrix"></a>

## GH-2. Stage-wise Zero-shot Probe Matrix

**Reviewer concern addressed:**  
What is the performance after using source dataset A, and how does it change after adding source dataset B?

At each step, the model is trained only on the newly arriving auxiliary source. All listed target domains are held out and used only for evaluation. In the probe matrix, `Avg. old-target Δ` reports the average performance change on previously evaluated target domains, compared with their performance when they first appeared.

`N/A` indicates that the corresponding metric is not applicable, e.g., image-level metrics for medical segmentation datasets with only anomalous test images, or old-target change at the first step.

### Table GH-2a. Fold-1 stage-wise zero-shot probe matrix

Auxiliary stream:

```text
MVTec -> ClinicDB -> MPDD -> ISIC -> DTD -> BrainMRI
```

| Step | Added source | New held-out target | New iAUROC | New pAP | New pF1 | New PRO | Avg. old ΔiAUROC | Avg. old ΔpAP | Avg. old ΔpF1 | Avg. old ΔPRO |
| ---- | ------------ | ------------------- | ---------: | ------: | ------: | ------: | ---------------: | ------------: | ------------: | ------------: |
| 1    | MVTec        | VisA                |      87.90 |   26.96 |   33.60 |   88.11 |              N/A |           N/A |           N/A |           N/A |
| 2    | ClinicDB     | ColonDB             |        N/A |   63.82 |   60.43 |   82.61 |            +0.22 |         -1.63 |         -1.72 |         -1.59 |
| 3    | MPDD         | BTAD                |      94.67 |   38.53 |   44.36 |   74.82 |            +0.24 |         -0.86 |         -1.08 |         +0.46 |
| 4    | ISIC         | Kvasir              |        N/A |   82.09 |   74.66 |   77.60 |            -0.42 |         -3.17 |         -1.67 |         -2.04 |
| 5    | DTD          | DAGM                |      98.11 |   53.95 |   54.76 |   93.61 |            +0.34 |         -2.50 |         -1.70 |         -0.71 |
| 6    | BrainMRI     | Br35H               |      99.55 |     N/A |     N/A |     N/A |            -0.07 |         -3.39 |         -2.27 |         -1.58 |

### Table GH-2b. Fold-2 stage-wise zero-shot probe matrix

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

**Key takeaway.**
These matrices directly show the model state after each auxiliary update: newly paired target performance is reported together with the average change on previously evaluated targets.

---

<a id="gh-3-old-target-forgetting-trajectory"></a>

## GH-3. Old-target Forgetting Trajectory

**Reviewer concern addressed:**
After adding a new source domain, how does performance on an old target change step by step?

Unlike GH-2, where `Avg. old-target Δ` summarizes the average change over previously evaluated targets, GH-3 reports direct old-target trajectories. Here, `Δ vs. prev. step` is computed as the score after adding the current source minus the score after the immediately preceding source update.

### Table GH-3a. Fold-1 old-target trajectory: VisA

In Fold-1, MVTec is first used as the source and VisA is evaluated as the paired target. From Step 2 onward, VisA becomes an old target.

| Step | New source added | Source datasets seen so far                | Target probe |  iAUROC/iAP | Δ iAUROC/iAP vs. prev. step |      pAUROC/pAP/pF1/PRO | Δ pAUROC/pAP/pF1/PRO vs. prev. step |
| ---: | ---------------- | ------------------------------------------ | ------------ | ----------: | --------------------------: | ----------------------: | ----------------------------------: |
|    1 | MVTec            | MVTec                                      | VisA         | 87.90/89.57 |                         N/A | 96.13/26.96/33.60/88.11 |                                 N/A |
|    2 | ClinicDB         | MVTec, ClinicDB                            | VisA         | 88.11/89.73 |                 +0.22/+0.16 | 95.97/25.34/31.88/86.52 |             -0.16/-1.63/-1.72/-1.59 |
|    3 | MPDD             | MVTec, ClinicDB, MPDD                      | VisA         | 88.14/89.84 |                 +0.02/+0.11 | 96.02/25.29/31.76/87.69 |             +0.06/-0.05/-0.12/+1.16 |
|    4 | ISIC             | MVTec, ClinicDB, MPDD, ISIC                | VisA         | 88.25/89.94 |                 +0.12/+0.10 | 96.01/24.72/31.05/87.72 |             -0.02/-0.57/-0.72/+0.03 |
|    5 | DTD              | MVTec, ClinicDB, MPDD, ISIC, DTD           | VisA         | 88.44/90.03 |                 +0.18/+0.09 | 95.94/24.68/30.81/87.40 |             -0.07/-0.04/-0.24/-0.32 |
|    6 | BrainMRI         | MVTec, ClinicDB, MPDD, ISIC, DTD, BrainMRI | VisA         | 87.64/89.03 |                 -0.80/-1.00 | 96.04/25.66/32.04/88.62 |             +0.10/+0.97/+1.23/+1.22 |

### Table GH-3b. Fold-2 old-target trajectory: MVTec

In Fold-2, the source-target role is swapped: VisA is first used as the source and MVTec is evaluated as the paired target. From Step 2 onward, MVTec becomes an old target.

| Step | New source added | Source datasets seen so far              | Target probe |  iAUROC/iAP | Δ iAUROC/iAP vs. prev. step |      pAUROC/pAP/pF1/PRO | Δ pAUROC/pAP/pF1/PRO vs. prev. step |
| ---: | ---------------- | ---------------------------------------- | ------------ | ----------: | --------------------------: | ----------------------: | ----------------------------------: |
|    1 | VisA             | VisA                                     | MVTec        | 93.49/97.23 |                         N/A | 92.44/46.99/47.99/85.85 |                                 N/A |
|    2 | ColonDB          | VisA, ColonDB                            | MVTec        | 92.70/96.67 |                 -0.79/-0.56 | 91.31/44.05/46.06/83.10 |             -1.12/-2.94/-1.93/-2.76 |
|    3 | BTAD             | VisA, ColonDB, BTAD                      | MVTec        | 93.03/96.84 |                 +0.34/+0.17 | 91.66/45.27/46.63/83.53 |             +0.35/+1.22/+0.57/+0.43 |
|    4 | Kvasir           | VisA, ColonDB, BTAD, Kvasir              | MVTec        | 92.07/96.44 |                 -0.96/-0.39 | 90.53/41.13/43.46/82.84 |             -1.13/-4.14/-3.17/-0.69 |
|    5 | DAGM             | VisA, ColonDB, BTAD, Kvasir, DAGM        | MVTec        | 92.65/96.58 |                 +0.57/+0.14 | 90.88/40.30/42.66/75.55 |             +0.35/-0.83/-0.80/-7.28 |
|    6 | Br35H            | VisA, ColonDB, BTAD, Kvasir, DAGM, Br35H | MVTec        | 92.83/96.56 |                 +0.18/-0.03 | 91.46/41.02/43.29/83.76 |             +0.58/+0.72/+0.63/+8.21 |

**Key takeaway.**
GH-2 and GH-3 answer the source-update question from complementary views: GH-2 reports new-target performance and average old-target change after each update, while GH-3 shows the direct step-to-step trajectory of representative old targets.

---

<a id="gh-4-additional-apf1-metrics"></a>

## GH-4. Additional AP/F1 Metrics

**Reviewer concern addressed:**
Can the evaluation include AP and max-F1 beyond AUROC/PRO?

Following reviewer suggestions, we additionally report pixel-level AP and max-F1, besides image-level AUROC/AP and pixel-level AUROC/PRO. We also include image-level max-F1 for a more complete classification evaluation.

🟩 denotes the best overall result among compared methods.

**Table GH-4. Additional image- and pixel-level metrics.**

| Method          | Domain           |       iAUROC |          iAP |          iF1 |       pAUROC |          PRO |          pAP |          pF1 |
| --------------- | ---------------- | -----------: | -----------: | -----------: | -----------: | -----------: | -----------: | -----------: |
| AnomalyCLIP     | Industrial Avg.  |        63.74 |        68.69 |        76.83 |        72.73 |        36.72 |         6.25 |         9.28 |
| AnomalyCLIP     | Medical Avg.     |        95.98 |        98.96 |        92.64 |        73.32 |        34.39 |        31.68 |        37.50 |
| **AnomalyCLIP** | **Overall Avg.** |    **71.80** |    **83.83** |    **80.78** |    **72.97** |    **35.79** |    **16.42** |    **20.57** |
| MVFA-AD         | Industrial Avg.  |        86.43 |        89.91 |        88.44 |        92.26 |        78.60 |        36.74 |        39.80 |
| MVFA-AD         | Medical Avg.     |        99.35 |        99.33 |        97.96 |        92.91 |        80.76 |        72.01 |        69.34 |
| **MVFA-AD**     | **Overall Avg.** |    **89.66** |    **92.27** |    **90.82** |    **92.52** |    **79.46** |    **50.85** |    **51.62** |
| AF-CLIP         | Industrial Avg.  |        90.96 |        91.85 |        88.86 |        89.22 |        74.41 |        28.79 |        31.72 |
| AF-CLIP         | Medical Avg.     |        98.98 |        98.94 |        97.66 |        93.34 |        77.35 |        75.30 |        69.86 |
| **AF-CLIP**     | **Overall Avg.** |    **92.97** |    **93.62** |    **91.06** |    **90.87** |    **75.59** |    **47.40** |    **46.98** |
| A2T             | Industrial Avg.  |        91.84 |        93.32 |        90.08 |        94.40 |        86.09 |        38.88 |        42.01 |
| A2T             | Medical Avg.     |        99.55 |        99.59 |        97.99 |        90.57 |        75.81 |        70.20 |        66.25 |
| **A2T**         | **Overall Avg.** | 🟩 **93.77** | 🟩 **94.88** | 🟩 **92.06** | 🟩 **92.87** | 🟩 **81.98** | 🟩 **51.41** | 🟩 **51.70** |

**Key takeaway.**
A2T achieves the best overall results across all image-level and pixel-level metrics, indicating a better balance between image-level recognition and dense anomaly localization.

---

<a id="gh-5-first-to-final-retention"></a>

## GH-5. First-to-final Retention

**Reviewer concern addressed:**
Does the model retain performance after the full auxiliary stream?

We summarize the first-to-final retention over all 12 held-out target probes. `First` denotes the performance when a target domain is first evaluated after its paired auxiliary source is learned, and `Final` denotes the performance after completing the full auxiliary-domain stream.

**Table GH-5. First-to-final retention over 12 held-out target probes.**

| Method      | iAUROC First → Final |   ΔiAUROC | pAP First → Final |      ΔpAP | pF1 First → Final |      ΔpF1 | PRO First → Final |      ΔPRO |
| ----------- | -------------------: | --------: | ----------------: | --------: | ----------------: | --------: | ----------------: | --------: |
| AnomalyCLIP |        88.06 → 71.80 |    -16.26 |     48.12 → 16.42 |    -31.70 |     48.39 → 20.57 |    -27.82 |     75.46 → 35.79 |    -39.67 |
| MVFA-AD     |        91.99 → 89.66 |     -2.33 |     56.21 → 50.85 |     -5.36 |     55.77 → 51.62 |     -4.16 |     84.41 → 79.46 |     -4.94 |
| AF-CLIP     |        92.90 → 92.97 |     +0.07 |     58.76 → 47.40 |    -11.37 |     56.90 → 46.98 |     -9.92 |     84.31 → 75.59 |     -8.72 |
| **A2T**     |    **93.66 → 93.77** | **+0.10** | **56.17 → 51.41** | **-4.77** | **55.06 → 51.70** | **-3.36** | **83.69 → 81.98** | **-1.72** |

**Key takeaway.**
Compared with AF-CLIP and MVFA-AD, A2T shows smaller long-term degradation on imbalance-sensitive pixel-level AP, max-F1, and PRO, indicating better preservation of dense anomaly localization under continual auxiliary-domain updates.

---

<a id="gh-6-relation-to-continual-anomaly-detection"></a>

## GH-6. Relation to Continual Anomaly Detection

**Reviewer concern addressed:**
How is LAP related to Continual Anomaly Detection, and what is the key difference?

LAP is related to CAD in that both study anomaly detection under sequential domain/task changes. However, LAP targets a different data-access protocol and evaluation goal. Most CAD methods focus on preserving or adapting anomaly detection performance over observed task/domain streams, whereas LAP couples continual auxiliary-domain learning with target-zero-shot transfer to unseen domains.

**Table GH-6. Comparison between LAP and CAD.**

| Setting                           | Training data                                  | Target access                                                         | Goal                                                                       | Evaluation                                                                                                            |
| --------------------------------- | ---------------------------------------------- | --------------------------------------------------------------------- | -------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| Static zero-shot AD               | One fixed auxiliary source or pretrained model | No target training data                                               | Transfer once to unseen targets                                            | Final zero-shot performance                                                                                           |
| Continual Anomaly Detection (CAD) | Sequential observed task/domain stream         | Observed domains/tasks are used for continual adaptation              | Preserve or adapt AD performance over seen domain/task streams             | Performance/forgetting on observed or previously seen tasks                                                           |
| LAP                               | Sequential auxiliary/source domains only       | No target-side training or tuning; target domains are evaluation-only | Combine continual auxiliary-domain learning with target-zero-shot transfer | Final zero-shot performance and stage-wise zero-shot probing on strictly disjoint target domains, plus Forgetting/BWT |

**Key takeaway.**
LAP does not continually adapt on target domains. Instead, labeled auxiliary/source domains arrive sequentially, and target domains are strictly held out for zero-shot evaluation. Thus, LAP evaluates not only anti-forgetting over the auxiliary stream, but also whether learned anomaly semantics and local structures remain transferable to unseen target domains.

---

<a id="gh-7-stream-order-sensitivity"></a>

## GH-7. Stream-order Sensitivity

**Reviewer concern addressed:**
Is the auxiliary-domain stream realistic, and is the method sensitive to stream ordering?

We evaluate three auxiliary-domain stream orders to test the robustness of continual zero-shot anomaly detection under different domain-transition patterns. We adopt a two-fold auxiliary-target swap protocol. In each fold, the auxiliary stream is used for continual training, while the paired target probes are used only for test-time evaluation.

Importantly, Fold-1 and Fold-2 are independent runs initialized from the same pretrained model. Fold-2 is not a continuation of Fold-1; it only swaps the auxiliary/target roles of the dataset pairs to reduce directional bias.

### Table GH-7a. Stream-order protocol under two-fold auxiliary-target swap

#### Fold-1: Original auxiliary-to-target direction

| Order type         | Auxiliary-domain stream                              | Corresponding target probes                        |
| ------------------ | ---------------------------------------------------- | -------------------------------------------------- |
| **Family-grouped** | MVTec -> MPDD -> DTD -> ClinicDB -> ISIC -> BrainMRI | VisA -> BTAD -> DAGM -> ColonDB -> Kvasir -> Br35H |
| **Alternating**    | MVTec -> ClinicDB -> MPDD -> ISIC -> DTD -> BrainMRI | VisA -> ColonDB -> BTAD -> Kvasir -> DAGM -> Br35H |
| **Distance-based** | MPDD -> MVTec -> DTD -> ISIC -> ClinicDB -> BrainMRI | BTAD -> VisA -> DAGM -> Kvasir -> ColonDB -> Br35H |

#### Fold-2: Role-swapped auxiliary-to-target direction

| Order type         | Auxiliary-domain stream                            | Corresponding target probes                          |
| ------------------ | -------------------------------------------------- | ---------------------------------------------------- |
| **Family-grouped** | VisA -> BTAD -> DAGM -> ColonDB -> Kvasir -> Br35H | MVTec -> MPDD -> DTD -> ClinicDB -> ISIC -> BrainMRI |
| **Alternating**    | VisA -> ColonDB -> BTAD -> Kvasir -> DAGM -> Br35H | MVTec -> ClinicDB -> MPDD -> ISIC -> DTD -> BrainMRI |
| **Distance-based** | VisA -> BTAD -> DAGM -> Br35H -> ColonDB -> Kvasir | MVTec -> MPDD -> DTD -> BrainMRI -> ClinicDB -> ISIC |

For every run, only the current auxiliary domain is used for model updating at each stream step. The corresponding target probes are strictly held out and are never used for training, prompt tuning, hyper-parameter selection, or model updating.

### Table GH-7b. Continual stability under three stream orderings

| Metric                  | Family-grouped |      Alternating |   Distance-based |
| ----------------------- | -------------: | ---------------: | ---------------: |
| iAUROC Forgetting / BWT |   2.39 / -1.36 |  **0.42 / 0.10** |  1.88 / **1.49** |
| iAP Forgetting / BWT    |   2.04 / -1.29 |  **0.47 / 0.20** |  2.58 / **0.28** |
| pAUROC Forgetting / BWT |   2.87 / -2.54 | **2.13 / -1.82** | 2.77 / **-1.14** |
| PRO Forgetting / BWT    |   5.42 / -3.19 |     3.87 / -1.72 |  **3.38 / 2.33** |

**Key takeaway.**
Stream ordering affects continual stability. Alternating generally yields lower forgetting, while distance-based ordering can improve backward transfer, especially for localization-related metrics.

---

<a id="gh-8-scs-basis-construction-and-storage"></a>

## GH-8. SCS Basis Construction and Storage

**Reviewer concern addressed:**
How is the historical activation basis constructed, and does SCS replay previous data?

SCS does not store or replay previous auxiliary images or masks. After each auxiliary task, selected-layer activations are summarized into compact orthonormal bases. These bases are then used to constrain future updates so that gradients do not overwrite historically important activation subspaces.

**Table GH-8. SCS basis construction and replay-free storage.**

| Item                  | Description                                                                                         |
| --------------------- | --------------------------------------------------------------------------------------------------- |
| Selected layers       | Lightweight visual adaptation layers, e.g., layers `{6, 12, 18, 24}`                                |
| Activation collection | Collect selected-layer activations from the current auxiliary domain after training                 |
| Basis construction    | Use SVD to select principal activation directions                                                   |
| Energy threshold      | Retain directions covering `τ = 0.97` activation energy                                             |
| Rank cap              | Use maximum rank cap `r_max = 256`                                                                  |
| Re-orthogonalization  | Apply QR-based re-orthogonalization when updating historical bases                                  |
| Stored information    | Compact orthonormal bases only                                                                      |
| Not stored            | No previous images, labels, or masks are stored                                                     |
| Training protocol     | Replay-free continual learning                                                                      |
| Purpose               | Preserve historical anomaly-sensitive activation subspaces while allowing current-domain adaptation |

**Key takeaway.**
SCS is replay-free: it stores compact bases instead of previous auxiliary samples, reducing memory overhead while stabilizing continual updates.

---

<a id="gh-9-efficiency-and-complexity-analysis"></a>

## GH-9. Efficiency and Complexity Analysis

**Reviewer concern addressed:**
What is the deployment cost of A2T?

To further evaluate the deployment cost of A2T, we report model size, trainable parameters, inference speed, latency, and peak GPU memory. All methods are evaluated under the same inference protocol and input resolution. For A2T, the CLIP backbone is kept frozen, and only lightweight prompt/adaptation modules are optimized during auxiliary-domain continual learning.

**Table GH-9. Efficiency and complexity comparison.**

| Method                           | Aux train epochs |    Params ↓ |        Trainable ↓ |        Speed ↑ |         Latency ↓ |  Peak Mem ↓ |
| -------------------------------- | ---------------: | ----------: | -----------------: | -------------: | ----------------: | ----------: |
| **A2T full**                     |      **2 / aux** | **431.11M** | **3.17M / 0.735%** | **8.29 img/s** | **120.70 ms/img** | **4.33 GB** |
| AnomalyCLIP                      |         15 / aux |     433.50M |     5.56M / 1.281% |     6.65 img/s |     150.29 ms/img |     4.87 GB |
| AdaptCLIP zero-shot              |         15 / aux |     428.55M |     0.61M / 0.142% |     6.61 img/s |     151.34 ms/img |     4.86 GB |
| AdaptCLIP few-shot/full (1-shot) |         15 / aux |     430.52M |     1.76M / 0.410% |     6.61 img/s |     151.26 ms/img |     5.01 GB |

**Key takeaway.**
A2T uses only 2 epochs per auxiliary domain and trains 3.17M parameters, accounting for 0.735% of the full model. It achieves faster inference and lower peak memory than the compared adaptation-based baselines.

**Measurement details.**
Speed and latency are measured during inference. Peak memory denotes the maximum GPU memory usage under the same evaluation setting. Data loading time is excluded from the reported inference speed and latency.

---

<a id="gh-10-continual-learning-baselines"></a>

## GH-10. Continual-learning Baselines

**Reviewer concern addressed:**
How does A2T compare with standard replay-free continual-learning baselines such as EWC and LwF/KD under the same LAP stream?

We add replay-free EWC and LwF/KD baselines under the same auxiliary-domain stream, target-zero-shot protocol, and evaluation setting.

### Table GH-10a. Performance comparison with EWC and LwF/KD

| Method     | Domain           |    iAUROC |       iAP |       iF1 |    pAUROC |       PRO |       pAP |       pF1 |
| ---------- | ---------------- | --------: | --------: | --------: | --------: | --------: | --------: | --------: |
| EWC        | Industrial Avg.  |     89.29 |     91.52 |     88.40 |     93.38 |     82.47 |     37.04 |     40.17 |
| EWC        | Medical Avg.     |     99.22 |     99.23 |     97.65 |     91.91 |     81.71 |     75.95 |     70.52 |
| **EWC**    | **Overall Avg.** | **91.78** | **93.45** | **90.71** | **92.79** | **82.17** | **52.60** | **52.31** |
| LwF/KD     | Industrial Avg.  |     90.94 |     91.82 |     88.64 |     95.16 |     86.61 |     42.78 |     44.83 |
| LwF/KD     | Medical Avg.     |     99.47 |     99.56 |     97.87 |     93.46 |     83.31 |     76.86 |     71.69 |
| **LwF/KD** | **Overall Avg.** | **93.07** | **93.75** | **90.94** | **94.48** | **85.29** | **56.41** | **55.57** |
| A2T        | Industrial Avg.  |     91.84 |     93.32 |     90.08 |     94.40 |     86.09 |     38.88 |     42.01 |
| A2T        | Medical Avg.     |     99.55 |     99.59 |     97.99 |     90.57 |     75.81 |     70.20 |     66.25 |
| **A2T**    | **Overall Avg.** | **93.77** | **94.88** | **92.06** | **92.87** | **81.98** | **51.41** | **51.70** |

### Table GH-10b. Forgetting and BWT comparison with EWC and LwF/KD

| Method | F_iAUROC | BWT_iAUROC |    F_iAP |  BWT_iAP | F_pAUROC | BWT_pAUROC |    F_PRO |  BWT_PRO |
| ------ | -------: | ---------: | -------: | -------: | -------: | ---------: | -------: | -------: |
| EWC    |     1.43 |      -1.61 |     0.96 |    -0.56 |     2.77 |      -2.52 |     4.40 |    -2.43 |
| LwF/KD | **0.31** |       0.07 |     0.56 |     0.00 | **0.96** |      -0.81 | **1.37** | **0.33** |
| A2T    |     0.42 |   **0.13** | **0.47** | **0.27** |     2.13 |      -1.82 |     3.87 |    -1.72 |

### Table GH-10c. Training-state overhead and inference-side cost of replay-free CL baselines

| Method | Data replay | Previous auxiliary data stored | Inference Params (M) | Trainable Params (M) | Trainable (%) | Extra training-time state | Extra state scale | Expected training memory overhead | Extra inference state |
|---|---|---|---:|---:|---:|---|---|---|---|
| EWC | No | No | 431.11 | 3.17 | 0.735 | Diagonal Fisher + previous parameter snapshot | ~2× trainable params | Low, about 2× trainable-state storage | None |
| LwF/KD | No | No | 431.11 | 3.17 | 0.735 | In-memory frozen previous-stage teacher | ~1× full model weights during training | Higher, adds one frozen teacher weight copy | None |
| A2T | No | No | 431.11 | 3.17 | 0.735 | Lightweight SCS adaptation state | Only on trainable adaptation modules | Low, only adaptation-module state | None |

**Note.**  
“Data replay” means storing and replaying previous auxiliary images, labels, or masks. All three variants are data-replay-free. EWC stores per-parameter Fisher statistics and a previous parameter snapshot. LwF/KD keeps a frozen previous-stage teacher model in memory during training for distillation; because the teacher is evaluated without gradient computation, this mainly adds one extra copy of model weights rather than a full second set of gradients and optimizer states. A2T does not store previous auxiliary data, previous-stage teachers, or full parameter snapshots; it only maintains lightweight SCS adaptation state for the trainable adaptation modules and introduces no extra inference-time state.


**Key takeaway.**
A2T achieves the best overall classification performance, with the highest iAUROC, iAP, and iF1. LwF/KD performs strongly on segmentation-oriented metrics and forgetting, but it requires an additional teacher model during training. A2T provides a strong balance between target-zero-shot transfer, continual stability, and training-state efficiency.

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

