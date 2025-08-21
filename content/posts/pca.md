---
title: "Principal Component Analysis"
date: 2025-08-21
draft: false
tags: ["pca"]
---
PCA (Principal Component Analysis): Projects high-dimensional data onto a lower-dimensional linear subspace (eg: 2D) by preserving maximum variance. Used as an input to t-SNE/UMAP to reduce feature noise.

Here is the PCA analysis of some important features:

[HSDir: Hidden Service Directory](/plots/PCA_HSDir_PCA1_PCA2_08212025.html)
When a Tor relay receives the HSDir flag, it means that the relay acts as a directory node for onion (hidden) services.
The purpose of HSDir is to facilitate the discovery of onion services while preserving anonymity. To receive the HSDir flag, a relay must be stable, have sufficient bandwidth, and usually must have been running for at least 96 hours.

[Exit](/plots/PCA_Exit_PCA1_PCA2_08212025.html)
Exit relays are the final hop in the TOR circuit before traffic reaches the public internet.
Attackers often seek to operate exit relays to exploit users, the Exit flag is a key feature to identify and monitor anomaly detection.

[Day Since Restart](/plots/PCA_days_since_restart_PCA1_PCA2_08212025.html)
This feature captures temporal behavior. Malicious relays often have irregular restart patterns or short uptimes.