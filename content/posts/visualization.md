#### Snaphot (8-3-2025)

[View Map](/plots/Tor_Relays_Clustered_Geographically_by_Features_map.html)
A world map showing Tor relays positioned by geographic coordinates and colored by key behavioral features (such as role flags or bandwidth categories).  
This visualization helps connect physical relay distribution to behavioral roles, revealing geographic concentrations of specific types (e.g., Guard-heavy zones or Exit-dense regions). Useful for spotting regional relay patterns.

[View RBM Plot](/plots/RBM_Anomaly_Energy_Overlay_UMAP.html)
A 2D UMAP embedding of Tor relays, colored by RBM energy values representing the model's confidence or reconstruction error for each relay.  
Higher energy scores may indicate anomalous or rare behaviors, while lower scores align with well-modeled, typical relays. This plot highlights latent behavioral outliers, useful for surfacing unknown roles or transitions not captured by flags alone.

[View Cluster Plot](/plots/hdbscan_cluster_on_UMAP1_vs_UMAP2.html)
Tor relays embedded via UMAP and clustered using HDBSCAN, a density-based algorithm that assigns relays to organic clusters without needing a fixed number of groups. Each cluster reflects a natural grouping of behavioral similarity across metrics like uptime, flags, bandwidth, and role history. Relays labeled as noise are considered outliers or difficult to cluster, often worthy of further analysis or tagging.