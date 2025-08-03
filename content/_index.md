---
title: "Anomaly Detection and Trust Projection in TOR"
date: 2025-08-03
type: "page"
---

This project started as a sandbox to learn data science algorithms with a passion backed by my years of security research inspecting telemetry.

More specifically, it arose from a desire to understand the concept by applying mathematics to tease out underlying patterns. Patterns of expectation, such as "The Weak Law of Large Numbers," still blow my mind how this abstract form converges.

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


### Tag Inference (In Progress)
The above process is used to derive meaningful behavioral labels.

### Linear Classifier (In Progress)
Once we have meaningful behavioral labels, we can train on those labels to create a decision hyperplane that can project trust/anomaly scores onto any relay.

---

### Plots

[View Geo Map](/plots/Tor_Relays_Clustered_Geographically_by_Features_map.html)  
A world map showing Tor relays positioned by geographic coordinates and colored by key behavioral features (such as role flags or bandwidth categories).  
This visualization helps connect physical relay distribution to behavioral roles, revealing geographic concentrations of specific types (e.g., Guard-heavy zones or Exit-dense regions). Useful for spotting regional relay patterns.

[View RBM Plot](/plots/RBM_Anomaly_Energy_Overlay_UMAP.html)  
A 2D UMAP embedding of Tor relays, colored by RBM energy values representing the model's confidence or reconstruction error for each relay.  
Higher energy scores may indicate anomalous or rare behaviors, while lower scores align with well-modeled, typical relays. This plot highlights latent behavioral outliers, useful for surfacing unknown roles or transitions not captured by flags alone.

[View Cluster Plot](/plots/hdbscan_cluster_on_UMAP1_vs_UMAP2.html)  
Tor relays embedded via UMAP and clustered using HDBSCAN, a density-based algorithm that assigns relays to organic clusters without needing a fixed number of groups. Each cluster reflects a natural grouping of behavioral similarity across metrics like uptime, flags, bandwidth, and role history. Relays labeled as noise are considered outliers or difficult to cluster, often worthy of further analysis or tagging.