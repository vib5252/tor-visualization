---
title: "Feature Drift"
date: 2025-08-27
tags: ["drift"]
math: true
---
I have reached a point where our selected clusters, identified through cluster characterization (using Z-score and RBM_avg), yield a set of relays (by fingerprint) for drift analysis.

Drift in this context means tracking how the top three Z-score features of each relay’s cluster evolve across timepoints. An important element of this process is using the RBM (Restricted Boltzmann Machine) to detect transitions. For example, we can spot when a relay moves from low RBM energy (routine) to high RBM energy (anomalous), or vice versa.

The RBM provides a familiarity landscape of the network. Observing shifts in RBM energy highlights periods and features of behavioral change. These high-drift features, in turn, become candidates for training our classifier.


```code
Timepoints (network snapshots at different dates)
   ↓
HDBSCAN Clustering (groups relays in embedding space)
   ↓
Cluster Characterization
   (compute per-cluster Z-scores and RBM_avg for all features)
   ↓
Select Clusters of Interest
   (based on high Z-scores, high or changing RBM_avg)
   ↓
Extract Relay Fingerprints
   (collect all relays in selected clusters)
   ↓
Track Drift Over Time
   (observe how the top three Z-score features of each cluster change across timepoints,
    and monitor RBM energy transitions from low to high or vice versa)
   ↓
Identify High-Drift Features
   (features with the largest changes or most associated with RBM energy transitions)
   ↓
Use High-Drift Features as Input for:
    → Training a Linear Classifier (e.g., LogisticRegression)
    → Training a Random Forest Classifier
    → Feeding to RBM for further unsupervised anomaly detection
    → Feeding to UMAP/t-SNE for latent visualization

```