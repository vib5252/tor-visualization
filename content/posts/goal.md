---
title: "Goal"
date: 2025-09-23
tags: ["goal"]
math: true
---

The diagram below outlines the evolving architecture of my Tor relay analysis pipeline.
Each stage reflects a new layer of insight, beginning with raw feature engineering and progressing through geometry extraction, unsupervised clustering, and finally interpretable and probabilistic modeling.
This chart captures the current state of the system as it grows more nuanced and capable of explaining drift, anomaly, and pattern formation in the network.

<div style="overflow-x:auto;">
<pre>
                  Raw Tor Relay Features
      (flags, CWF, uptime, restarts, geo, etc.) + weights w (Intuition 3+6)
                                   │
                                   ▼
                          ┌────────────────┐
                          │ Preprocessing  │
                          │ standardize,   │
                          │ clip outliers  │
                          └────────────────┘
                                   │
                                   ▼
     ┌───────────────────────────────────────────────────────────────┐
     │                STAGE A — Backbone Geometry                    │
     │        Contractive + Denoising Autoencoder (C/D-AE)           │  ← Intuition 7
     │  - Encoder: x → z_base (16–32 dims)                           │
     │  - Loss: L_rec = Σ w_j (x_j - x̂_j)^2  +  λ_c ‖∂z/∂x‖²         │
     └───────────────────────────────────────────────────────────────┘
                                   │
                                   ▼
                          z_base  (stable latent)
                                   │
                 ┌─────────────────┴─────────────────┐
                 │                                   │
                 ▼                                   ▼
          HDBSCAN on z_base                     UMAP(z_base)
          (clustering space)                    (one global 2D map)
                 │                                   │
                 ▼                                   ▼
        Cluster snapshots & drift            Drift arrows on map
        (centroid, size, density,            (visualization only)
         role flips, weighted features)
                                   │
                                   ▼
     ┌───────────────────────────────────────────────────────────────┐
     │        STAGE B — Interpretable Bundles (side head)            │
     │       Sparse / Winner-Take-All Autoencoder Head               │
     │  - Share the same encoder (or a small adapter)                │
     │  - Get z_sparse (few active units)                            │
     │  - Loss: α·L_rec_sparse  +  β·‖z_sparse‖₁ / k-sparse mask     │
     │  Use: z_sparse to map attention-weighted bundles & “twist”    │
     │  (Do NOT use for clustering; clustering stays on z_base)      │
     └───────────────────────────────────────────────────────────────┘
                                   │
                                   ▼
     Bundle fibers (from z_sparse) with attention weights (Intuition 3+6)
     - Explain why drift arrows bend
     - Detect bundle “twist” (orientation change over time)
                                   │
                                   ▼
     ┌───────────────────────────────────────────────────────────────┐
     │            STAGE C — Probabilistic Terrain                    │
     │       Variational Autoencoder (VAE) head (Intuition 9)        │
     │  - Turn decoder into μ, logσ²; KL warm-up (β-anneal/free bits)│
     │  - ELBO/NLL per sample → cluster-level terrain depth          │
     │  Option: keep RBM as reference chronometer (Intuition 8)      │
     │          for ΔF comparisons (disagreements = insight)         │
     └───────────────────────────────────────────────────────────────┘
                                   │
                                   ▼
     Terrain overlays & metrics:
     - VAE NLL/ELBO (primary terrain)
     - (Optional) RBM ΔF (reference)
     - Composite drift report: speed/angle, ΔNLL, bundle twist
</pre>
</div>

### Legend of Acronyms Used in the Flowchart
---

- **AE** &rarr; *Autoencoder*: a neural network that compresses input into a latent representation and reconstructs it back.

- **C/D-AE** &rarr; *Contractive / Denoising Autoencoder*:
    - **Contractive AE**: adds a penalty so the latent space changes smoothly with respect to input.
    - **Denoising AE**: trains to reconstruct clean input from noisy versions, improving robustness.

- **Sparse / WTA-AE** &rarr; *Sparse or Winner-Take-All Autoencoder*: enforces that only a small number of latent neurons activate, creating interpretable bundle features.

- **VAE** &rarr; *Variational Autoencoder*: extends AE by encoding inputs into a probability distribution, enabling sampling and likelihood estimation (**ELBO**, **NLL**).

- **RBM** &rarr; *Restricted Boltzmann Machine*: energy-based model that assigns a free-energy score to data, used as an optional plausibility reference.

- **ELBO** &rarr; *Evidence Lower Bound*: objective function used to train VAEs; balances reconstruction accuracy and regularization.

- **NLL** &rarr; *Negative Log-Likelihood*: how surprising data is under a model; lower = more plausible.

- **HDBSCAN** &rarr; *Hierarchical Density-Based Spatial Clustering of Applications with Noise*: clusters dense areas of latent space, marks sparse points as noise.

- **UMAP** &rarr; *Uniform Manifold Approximation and Projection*: nonlinear dimensionality reduction, for 2D visualization of high-dimensional data.

- **KL divergence** &rarr; *Kullback–Leibler divergence*: measure of difference between two probability distributions; used in VAEs to keep latent codes close to a prior.


