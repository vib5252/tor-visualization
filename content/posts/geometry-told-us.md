---
title: "What the Geometry Actually Told Us"
date: 2026-02-26
tags: ["Geometry", "Stability", "PCA", "CCA", "Code"]
math: true
weight: 1
---

In November I wrote that geometry turns motion into something interpretable. That post was written from intuition, before any real measurement had been done. It was a claim about what geometry *should* be able to do.

This post is what happened when I actually did it.

---

## Where the intuition came from

Some of it came from walking with my cat.

I'd watch clouds on those walks and find myself noticing things I couldn't quite articulate yet. The low ones held together. Dense and fluffy, they moved as units, keeping their shape against the wind, internal structure intact. The high ones were different. Thin and threadlike, they sheared apart, filaments stretching and dissolving. Same sky, same wind, completely different response. The interesting thing wasn't how far they moved. It was how they changed while moving.

Height and density weren't incidental. Low clouds are dense, anisotropic, dominated by a preferred direction. High clouds are diffuse, nearly isotropic. Any axis is as good as any other, which means no axis anchors them. The wind doesn't need to be strong to pull them apart.

The circular clouds taught me something else. An elongated cloud tracks cleanly. You can follow it. A nearly round cloud is slippery. Under gentle wind it seems to spin in place, the long axis swapping with the short one, orientation becoming ambiguous. I later recognized this as eigenvalue gap instability: when the top two principal values are close, the data does not privilege one direction over the other, so small perturbations can rotate the basis dramatically.

The other observation came from a sail shade in the yard. Water dripping off it never falls randomly. It finds grooves, follows streaks, converges along paths of least resistance into thin lines of flow. The first time I really looked at it I thought: that's gradient descent. The water isn't computing anything. It's just obeying local slope. But the result is that scattered drops trace the manifold structure of the surface, revealing geometry through accumulation.

I didn't write any of this down as math at the time. But when the Q1-Q4 diagnostics came back, I recognized what I was looking at.

---

## Q1: How many dimensions does this actually live in?

22 features. But 95% of the variance lives in 6 principal components. All measurements were performed in standardized feature space.

That compression alone isn't surprising. High-dimensional relay data usually has latent structure. What was surprising was how unevenly that compression distributed across feature groups.

`network_allocation` collapses to essentially one dimension. One axis captures it almost completely. It's the sail shade with a single dominant groove, everything flows one way.

`role_probability` is the opposite. It resists compression. It spreads across all three components and doesn't surrender to a simpler description. I expected it to collapse the way `network_allocation` did. It didn't.

That asymmetry matters. Not every feature group lives in the same kind of space.

![Q1 cumulative variance](/plots/q1_cumulative_variance.png)

---

## Q2: What shape is that space?

Even knowing the dimension, the shape varies enormously across feature groups.

I measured local anisotropy, how stretched vs. spherical the local geometry is at each point. The results fell into three tiers:

<pre style="overflow-x:auto; margin-top:1rem; margin-bottom:1rem;">
Tier 1 — Manifold-like (extreme anisotropy)
  role_probability       ratio_median 15,150   topshare 0.9998
  bandwidth_performance  ratio_median  5,344   topshare 0.9992

Tier 2 — Moderate structure
  network_allocation, temporal, geo_as

Tier 3 — Binary / isotropic (no dominant axis)
  attack_surface, authority_verdicts, relay_capability
</pre>

Tier 1 features behave like low-dimensional manifolds embedded in higher-dimensional space. Nearly all the local variance lives in one direction. The fluffy cloud. Tier 3 features are effectively discrete. Their variance does not organize along a dominant continuous axis. The threadlike cloud, or no cloud at all.

The lesson from Q2: you can't apply the same analysis to all feature groups and expect consistent results. The geometry of `role_probability` and the geometry of `attack_surface` are different kinds of objects.

![Q2 anisotropy distribution](/plots/q2_directional_dominance.png)

---

## Q3: Is the structure density or correlation?

For `network_allocation`, the answer was clear: it's correlation. The first principal component loads at 1.0. The features don't cluster in dense pockets. They align along a shared axis. It's a line, not a cloud.

For `role_probability` and `bandwidth_performance`, the answer was ambiguous. Strong local geometry, but no clean global structure. I spent time wondering if this was a threshold problem, maybe I'd drawn the wrong boundary, or the scale was off. It wasn't. The ambiguity is real. Those feature groups have coherent local neighborhoods that don't resolve into a single global shape.

That finding is meaningful on its own. Not everything has to resolve. Some structure is genuinely local.

---

## Q4: Did any of this hold over time?

This is where the intuition met its hardest test.

I ran the diagnostics across 94 snapshots from November 2025 through February 2026. The question was simple: does the geometric structure stay stable, or does it deform?

<div style="overflow-x:auto; margin-top:1rem; margin-bottom:1rem;">
<pre>
Snapshots analyzed:  94
Stable days:         38  (40%)
Unstable days:       56  (60%)
Mean stability:       0.560
</pre>
</div>

60% unstable. The mean stability score barely clears 0.5. And then, starting February 9th, something worse: 13 consecutive degenerate days. CCA (Canonical Correlation Analysis, which measures how well two independent representations of the same data agree) collapsed completely. It never recovered.

![Q4 stability over time](/plots/q4_stability_over_time.png)

What actually happened on February 9th matters, because the honest version is more instructive than the dramatic one.

That day I made a deliberate decision: fix the CCA projection at 16 dimensions, train it once, and freeze it across time. The decision itself wasn't wrong. What failed was how the training data was constructed. CCA requires paired samples, RBM latent vectors and VAE latent vectors, drawn from the same relays at the same snapshot. Instead, due to missing historical RBM inputs, the training script silently reused February 9th RBM data for all historical dates. VAE data varied across dates. RBM data was effectively constant.

When one side of a correlation has zero variance, correlation becomes undefined. The optimizer had nothing to work with. All 16 canonical correlations came back exactly zero. Not small. Zero. Then I froze that degenerate projection and kept applying it to new data for 13 days.

The geometry wasn't collapsing. The alignment layer was already collapsed, locked to a basis trained on corrupted inputs.

What matters is what happened next. I could have patched the bug and moved on. Instead I treated it as a signal. If the alignment layer is this fragile, what assumptions was it making? That question is what produced the Q1-Q4 meta-framework. The collapse didn't reveal a geometric singularity. It revealed that alignment methods assume variance, and when that assumption breaks, agreement scores become meaningless without warning.

---

## The rank-1 truth

Before the collapse, CCA produced one striking result.

Two independent observers, the RBM and the VAE, were trained separately on the same relay data. They share no weights, no architecture, no explicit coordination. When I asked how much they agreed on the latent structure, the answer was: almost entirely on one dimension, and almost not at all on any other.

<p style="margin-bottom:0.5rem">First canonical correlation: <strong>0.826</strong>.<br>Second canonical correlation: near noise.</p>
<div style="overflow-x:auto; margin-top:0; margin-bottom:1rem;">
<pre>
Dimension 1:  ████████████████████  0.826  ← shared
Dimension 2:  ░░░░                         ← noise
Dimension 3:  ░░░                          ← noise
...
</pre>
</div>

One stiff axis. Fifteen elastic ones.

Two models, independently trained, converging on a single shared structure. That's not a coincidence. That's signal.

---

## Why geometry has to come before alignment

February 9th clarified something I hadn't thought through carefully enough.

CCA measures agreement between representations. But agreement requires something stable to agree *about*. When variance collapses on one side of the pairing, there is nothing for the models to lock onto. The score doesn't just drop. It becomes undefined, and undefined is not the same as zero.

I had been thinking of CCA as a diagnostic tool. It is. But it assumes the geometry is already well-defined. It doesn't warn you when that assumption breaks. It just returns a number.

The lesson is sequencing. You can't measure agreement before you understand structure. Alignment cannot precede geometric understanding.

Drift is not motion. It's deformation of latent geometric structure. And a relay that looks surprising in one representation might just be registering that deformation, not that the relay itself has changed. Surprising is not the same as anomalous.

---

## The open question

The one stiff axis remains unidentified. But now I know what it isn't.

My leading hypothesis was `consensus_weight`, the Tor network's explicit capacity allocation signal. A single authoritative number the network computes for each relay. If any feature drives a shared latent dimension across independent models, it seemed like the obvious candidate.

I ran the test. I removed `consensus_weight` and `consensus_weight_fraction` from the feature set entirely, retrained both models from scratch on the reduced features, and measured the geometric impact.

<p style="margin-bottom:0.5rem">The shared dimension didn't collapse. It got stronger.</p>
<div style="overflow-x:auto; margin-top:0; margin-bottom:1rem;">
<pre>
Baseline (with consensus_weight):    pc1_global = 0.36
E1       (without consensus_weight): pc1_global = 0.27
Effective dimensionality (dim_95):   9 → 9  (unchanged)
</pre>
</div>

Removing the two features cost one independent dimension of information. They were nearly perfectly collinear, eigenvalue ratio ~10¹⁵, essentially the same number stored twice. The geometry was not organized around them. `consensus_weight` was the loudest voice in the room, but not the one the models were listening to.

The audit also surfaced something worth noting: `consensus_weight` correlates at 0.94 with `advertised_bandwidth` and `observed_bandwidth`. Its signal was already present in the data. Removing it didn't create a void. It reduced redundancy.

So the stiff axis is something more fundamental. Something the two models find independently, even after the dominant capacity signal is gone.

This changes the question. Not what is the most prominent feature, but what does a relay actually *do* over time that two independent observers consistently agree on.

A snapshot shows position. What I think the models are converging on is something closer to trajectory, the behavioral history of a relay, not its current state. A relay that has held the same flags for two years, maintained stable bandwidth, stayed consistently online. That relay has a different geometric signature than one that appeared last week with identical current values.

The network treats `consensus_weight` as ground truth for capacity allocation. But the models may be finding something the network doesn't explicitly label: that reliable relays are, in a precise mathematical sense, tonal. Their history predicts their future. Unreliable ones are noise.

That's the next experiment. Not removing features, but adding history: lifespan, uptime consistency, bandwidth stability over time. Turning the snapshot into a film.

The sail doesn't fight the wind. It reads it. I think the stiff axis is the wind.

---

## The Meta-Framework

The Q1–Q4 diagnostic framework is now public.

[tor-meta-framework on GitHub](https://github.com/vib5252/tor-meta-framework)

The repo includes the full pipeline — fetch, contract, Q1–Q4 diagnostics, and the VAE+RBM worked example. If you're exploring the Tor network or want to run the same geometric diagnostics on your own network data, that's the starting point.