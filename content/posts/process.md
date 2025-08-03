---
title: "Process"
date: 2025-08-03
---

The TOR public API gives a rich amount of data that is perfect for exploring latent features. This project explores a pipeline for "trust" scoring of TOR relays using a hybrid of unsupervised learning and a final linear classifier.

### Unsupervised Process:

Dimensionality Reduction:
- Linear
	- PCA (Principal Component Analysis): Projects high-dimensional data onto a lower-dimensional linear subspace (eg: 2D) by preserving maximum variance. Used as an input to t-SNE/UMAP to reduce feature noise.
- Non-Linear
	- t-SNE (t-distributed Stochastic Neighbor Embedding): Preserves local structure, meaning points close in high-dimensional space stay close in 2D. Lacks global relationship.
	- UMAP (Uniform Manifold Approximation and Projection): Preserves local and some global structure, making it well-suited for highlighting latent topology.

Clustering:
- HDBSCAN (Hierarchical Density-Based Spatial Clustering of Applications with Noise): Groups data based on density, not distance. Works well on embedded spaces (t-SNE/UMAP outputs).

Latent Pattern Extraction:
- RBM (Restricted Boltzmann Machine): A generative neural network used to uncover hidden co-activation patterns (learns which features tend to activate together) in Tor relay features. These help capture latent behavioral motifs, such as patterns of bandwidth, flags, and uptime, which are not visible through clustering alone.


#### Tag Inference (In Progress)
The above process is used to derive meaningful behavioral labels.

#### Linear Classifier (In Progress)
Once we have meaningful behavioral labels, we can train on those labels to create a decision hyperplane that can project trust/anomaly scores onto any relay.