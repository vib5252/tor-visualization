---
title: "Stage A — Backbone Geometry"
date: 2025-09-28
tags: ["stageA"]
math: true
---
In this stage, raw Tor relay features are processed by a contractive + denoising autoencoder (C/D-AE) to produce a robust latent representation of the network’s state.

The autoencoder learns to compress the input into a lower-dimensional space ($z_\mathrm{base}$) while resisting overfitting and smoothing out noise. This geometric “backbone” becomes the foundation for downstream clustering (HDBSCAN), visualization (UMAP), and all subsequent anomaly and drift analysis.

---

### **Sensitivity and the Jacobian Matrix**

Unlike a plain autoencoder, a **contractive autoencoder** doesn’t just reconstruct the input—it also learns to **control the sensitivity** of its latent space to small changes in the input features.

The **loss function** combines two terms:

* **Reconstruction error**: Measures how well the autoencoder can recover the original input from the compressed latent space.
* **Contractive penalty**: Involves the **Jacobian matrix** of the encoder and directly penalizes excessive sensitivity.

The training objective is:

$$
\mathcal{L}_{rec} = \sum_j w_j (x_j - \hat{x}_j)^2 + \lambda_c \left| \frac{\partial z}{\partial x} \right|^2
$$
Here:
* $x_j$ — the $j$‑th input feature value
* $\hat{x}_j$ — the reconstructed value of feature $j$ (output from the autoencoder)
* $w_j$ — weighting factor for feature $j$ in the reconstruction loss (to emphasize or de-emphasize certain features)
* $\lambda_c$ — hyperparameter controlling the strength of the contractive penalty (how much we penalize sensitivity)
* $z$ — the latent (encoded) representation learned by the autoencoder
* $\frac{\partial z}{\partial x}$ — the **Jacobian matrix** of the encoder; describes how each element of the latent code $z$ changes in response to small changes in each input $x$
* $\left| \cdot \right|^2$ — squared Frobenius norm (sum of the squares of all elements in the Jacobian), used to penalize sensitivity

---
The **Jacobian matrix** quantifies how much each dimension of the latent space $z$ changes as you vary each input $x$. By penalizing its squared norm, we discourage mappings where small input changes cause big jumps in the latent space.
**This shrinks the sensitivity of the latent space**, making it stable and robust to minor fluctuations in Tor relay data.

The autoencoder learns a latent geometry that’s not only a compressed version of the input, but also **insensitive to minor variations**. This is critical for stable downstream clustering and drift detection, ensuring meaningful, reliable groupings.

The output $z_\mathrm{base}$ serves as the “map” for unsupervised discovery of meaningful clusters and changes in network behavior.





