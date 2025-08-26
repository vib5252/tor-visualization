---
title: "Cluster Characterization"
date: 2025-08-25
tags: ["cluster","zscore"]
math: true
---
##### Z-Score
A Z-score tells us how many standard deviations a feature’s average (mean) is from the global mean.

We use this on our top 3 feature combos:
- A positive ↑ z-score means the feature has higher than average in the cluster.
- A negative ↓ z-score means the feature is lower than average in the cluster.

A low RBM value means that it is familiar to the neural net. It has seen it while training
A high RBM means the neural network is not familiar with this pattern.

---

#### **Threshold Algorithm for RBM and Z-score**

From our cluster characterization we want to select clusters that stand out and use those top features as an input for training our classifier.

To do this, we use a two-step process:

##### Selection Score (Feature Outlierness):

```python 
selection_score = (zscore_max - zscore_min) + zscore_mid
```

This selection score highlights clusters where multiple features are unusually high or low, or show strong contrast. We only consider clusters with a selection score above a chosen threshold.

The top three features are z-score values. Positive z-score means the feature is higher than average present in the cluster where as a negative z-score means the feature is lower than average in the cluster.

Also, its important to remember that a z-score is measured in standard deviations (std) and convey information about how far in std are we from the global mean.

```python
zscore = (cluster_mean - global_mean) / global_std
```

`cluster_mean - global_mean` is a measure of how much the cluster's mean deviates from the typical `global mean`.
`global_std` is the measure of how much the feature naturally varies globally.


##### RBM Familiarity Filter:

```python
RBM_delta = RBM_avg(cluster) - RBM_min

```
RBM We compute the average RBM energy (RBM_avg) for each cluster, representing how familiar or routine the neural network thinks this pattern is. Only clusters with an RBM value far enough from the most familiar cluster (above an RBM delta threshold) are selected.

Note: The RBM energy directly measures how well the hidden layer “explains” (reconstructs) the visible/input data.

---

In short:
Clusters are flagged if they have both an extreme combination (↑↑ or ↓↓) of feature (z-scores) deviations (high selection score) and are considered unfamiliar by the neural network (high RBM delta). These clusters, and their top features, become the focus for our next stage, training a classifier to recognize or investigate these patterns further.

*08-25-2025, 9:29pm*, Cluster Information
```python
Cluster 3:
  Predefined Tag Relationship: none
  Top Features:               zscore:
   Authority                  ↑ +(5.73)
    exit_probability          ↑ +(1.91)
    rbm_energy                ↓ -(1.48)
  RBM_avg: 13.950
  RBM_delta: 3.659
  RBM_threshold: 2
  selection_score: 9.120
  selection_score_threshold: 5

Cluster 5:
  Predefined Tag Relationship: none
  Top Features:                 zscore:
    Burst_to_Rate             ↑ +(5.74)
    Bandwidth_Ratio           ↑ +(5.71)
    StaleDesc                 ↑ +(5.61)
  RBM_avg: 12.561
  RBM_delta: 5.048
  RBM_threshold: 2
  selection_score: 5.840
  selection_score_threshold: 5

Cluster 7:
  Predefined Tag Relationship: none
  Top Features:                 zscore:
    exit_probability          ↑ +(2.47)
    V2Dir                     ↓ -(2.22)
    Exit                      ↑ +(1.67)
  RBM_avg: 14.815
  RBM_delta: 2.794
  RBM_threshold: 2
  selection_score: 6.360
  selection_score_threshold: 5

Cluster 11:
  Predefined Tag Relationship: none
  Top Features:                 zscore:
    Stable                    ↓ -(3.32)
    BadExit                   ↑ +(1.76)
    Exit                      ↑ +(1.59)
  RBM_avg: 15.381
  RBM_delta: 2.228
  RBM_threshold: 2
  selection_score: 6.670
  selection_score_threshold: 5

Cluster 14:
  Predefined Tag Relationship: none
  Top Features:                 zscore:
    MiddleOnly                ↑ +(5.63)
    BadExit                   ↑ +(3.56)
    exit_probability          ↑ +(2.66)
  RBM_avg: 13.891
  RBM_delta: 3.718
  RBM_threshold: 2
  selection_score: 6.530
  selection_score_threshold: 5
```


Here is a list of clusters highlighted by the threshold algorithm.
The [RBM](/plots/RBM_Energy_Overlay_UMAP_8252025.html) and [HDBSCAN](/plots/hdbscan_cluster_on_UMAP1_vs_UMAP2_TOPF_8252025.html) plots with UMAP (Uniform Manifold Approximation and Projection) as an embedding, tell a story about the cluster. 

A low RBM value tells us that the neural net is familiar with this pattern. Coupled with a high selection_score, this means the cluster is statistically distinct (stands out from the global average on multiple features), but its overall behavior is something the RBM has “seen before.”

In practice, this often surfaces recurring behavioral motifs in the Tor network. These are distinct, persistent patterns that may include relay groups such as:

**Cluster 3: Unusually high authority**
- **Authority** ↑ (5.73)
- **exit_probability** ↑ (1.91)
- *This cluster is dominated by relays with very high Authority and a tendency to act as exits. This combination is rare and important to notice.*

---

**Cluster 11: Exits with risk flags**
- **BadExit** ↑ (1.76)
- **Exit** ↑ (1.59)
- **Stable** ↓ (3.32)
- *These relays are flagged as both exits and bad exits, and they are unusually unstable (negative Stable z-score).*

---

**Cluster 14: Exits with risk flags (Middle Only)**
- **MiddleOnly** ↑ (5.63)
- **BadExit** ↑ (3.56)
- **exit_probability** ↑ (2.66)
- *Strong signals for bad exit status and unusual placement in the circuit (MiddleOnly), which could indicate risky or misbehaving relays.*

---

**Cluster 5: Persistent bandwidth anomalies**
- **Burst_to_Rate** ↑ (5.74)
- **Bandwidth_Ratio** ↑ (5.71)
- **StaleDesc** ↑ (5.61)
- *This group has extremely high ratios related to bandwidth and frequent stale descriptors. This is a classic sign of bandwidth “gaming” or operational misconfiguration.*

---
