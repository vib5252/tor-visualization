---
title: "Goal"
date: 2025-09-23
tags: ["goal"]
math: true
---
I have reached a point where our selected clusters, identified through cluster characterization (using Z-score and RBM_avg), yield a set of relays (by fingerprint) for drift analysis.

Drift in this context means tracking how the top three Z-score features of each relay’s cluster evolve across timepoints. An important element of this process is using the RBM (Restricted Boltzmann Machine) to detect transitions. For example, we can spot when a relay moves from low RBM energy (routine) to high RBM energy (anomalous), or vice versa.

The RBM provides a familiarity landscape of the network. Observing shifts in RBM energy highlights periods and features of behavioral change. These high-drift features, in turn, become candidates for training our classifier.


```code
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
     │  - Loss: L_rec = Σ w_j (x_j - x̂_j)^2  +  λ_c ‖∂z/∂x‖²        │
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
         role flips, top-3 features)
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

```