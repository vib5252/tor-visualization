+++
title = "What Changed on February 20th?"
date = 2026-03-07
tags = ["Geometry", "CCA", "E3", "OONI"]
math = true
weight = 1
+++

What if you could detect the echo of a real-world event: a cyberattack, an
infrastructure failure, a geopolitical shock, without reading a single packet?
No content. No identities. No traffic volume. Just the shape of how anonymous
relays behave with each other.

That is the central claim of [Drift as Deformation](https://zenodo.org/records/18463659),
a geometric framework for detecting structural change in anonymity networks. The
thesis is simple: when the Tor relay population undergoes a meaningful shift, it
does not just move. It deforms. The latent geometry changes shape. Two independent
observers, trained on the same relay population but built with different
architectures, will lose agreement on their shared direction. That loss of agreement
is the signal.

This post documents the first empirical validation of that thesis. Over 98 days,
the detector watched the Tor network without updating its observers. Three times,
the signal crossed the calibrated detection threshold. All three coincided with
documented external events. The Iranian drone strike that destroyed two AWS data
centers did not move it. Routine network churn did not move it. Only three events
did, and the detector found all three.

The February 20th event is the most important of the three. It gave the framework
something it needed: a reference event with a known external correlate that allowed
the stiff axis to be defined empirically. That reference frame now anchors a
broader research program.

---

## The Validated Detections

<div style="overflow-x:auto; margin-top:1rem; margin-bottom:1.5rem;">
<table style="border-collapse:collapse; width:100%; font-size:0.9em;">
<thead>
<tr style="border-bottom:2px solid #666;">
<th style="text-align:left; padding:0.4rem 0.8rem;">Date</th>
<th style="text-align:left; padding:0.4rem 0.8rem;">Δρ</th>
<th style="text-align:left; padding:0.4rem 0.8rem;">θ</th>
<th style="text-align:left; padding:0.4rem 0.8rem;">Label</th>
<th style="text-align:left; padding:0.4rem 0.8rem;">External signal</th>
</tr>
</thead>
<tbody>
<tr style="border-bottom:1px solid #444;">
<td style="padding:0.4rem 0.8rem;">Dec-20, 2025</td>
<td style="padding:0.4rem 0.8rem;">+0.0022</td>
<td style="padding:0.4rem 0.8rem;">60.78°</td>
<td style="padding:0.4rem 0.8rem;">primary_signal</td>
<td style="padding:0.4rem 0.8rem;">OONI anomaly rate dropped</td>
</tr>
<tr style="border-bottom:1px solid #444;">
<td style="padding:0.4rem 0.8rem;">Feb-20, 2026</td>
<td style="padding:0.4rem 0.8rem;">−0.0017</td>
<td style="padding:0.4rem 0.8rem;">66.97°</td>
<td style="padding:0.4rem 0.8rem;">primary_signal</td>
<td style="padding:0.4rem 0.8rem;"><a href="https://blog.cloudflare.com/cloudflare-outage-february-20-2026/" target="_blank">Cloudflare BGP outage</a> (~1,100 prefixes withdrawn)</td>
</tr>
<tr>
<td style="padding:0.4rem 0.8rem;">Mar-06, 2026</td>
<td style="padding:0.4rem 0.8rem;">+0.0025</td>
<td style="padding:0.4rem 0.8rem;">80.42°</td>
<td style="padding:0.4rem 0.8rem;">primary_signal</td>
<td style="padding:0.4rem 0.8rem;">OONI 18.2% global peak</td>
</tr>
</tbody>
</table>
</div>

Two distinct mechanisms are visible in the Δρ column. Dec-20 and Mar-06 show
positive Δρ: geometric deformation, structural reorganization of the relay
population. Feb-20 shows negative Δρ: a different physical mechanism, consistent
with infrastructure stress rather than reorganization. The geometry is not just
detecting that something happened. It is distinguishing how.

March 1, Iranian drone strikes on AWS ME-CENTRAL-1, stays baseline. No geometric
signature in the Tor stiff axis. The detector is not triggering on everything.

If you are new to this space: Tor is a global network of volunteer-operated relays
that anonymize internet traffic by routing it through multiple hops. The relay
population collectively forms a geometric structure. When that structure deforms,
something changed in how the network is being used. This detector measures that
deformation. It never sees who is connecting or what they are sending.
It only sees shape.

---

## The Full Timeline: 98-days

14-day window (W=14) Classification
![E3 Window Classification W=14](/plots/delta_rho_classification_w14.png)

*Left: principal angle θ: red dots are
primary_signal events; orange are unstable windows; grey is baseline.*

*Right: eigenvalue gap Δρ with calibrated threshold. The three primary_signal events
are precisely at the validated dates. The 58 unstable windows are distributed
across the timeline as background, not clustering around events.*

The 7-day window (W=7) classification shows the December precursor one day earlier:
![E3 Window Classification W=7](/plots/delta_rho_classification_w7.png)

*Left: principal angle θ: red dots are
primary_signal events; orange are unstable windows; grey is baseline.*

*Right: eigenvalue gap Δρ: blue dots above threshold are positive excursions
(structure clarifying), red dots below are negative excursions (axis inversion,
infrastructure stress). The December 19 precursor is visible one day before the
W=14 detection on December 20. W=14 amplified sub-threshold structure that W=7
registered but did not elevate to primary signal.*

---

## The Framework

191 features per relay, per day fed into the trunk encoder: bandwidth, uptime,
restart frequency, address stability, consensus weight, flag history, and related
signals. No packets inspected. No content. No individual identities.

The first observer, a Variational Autoencoder, maps the relay population into a
32-dimensional geometric space. The second, a Restricted Boltzmann Machine, learns
the energy landscape of relay co-activation patterns, which relays tend to behave
similarly and which diverge.

They are intentionally different architectures. When they independently agree on a
direction, that agreement is the signal.

Canonical Correlation Analysis measures agreement between the two observers across
sliding time windows. Two numbers per day:

<pre style="overflow-x:auto; margin-top:1rem; margin-bottom:1rem;">
Δρ = ρ₁ − ρ₂   eigenvalue gap: how much one shared direction dominates
θ  = arccos(|v₁ · v₂|)   rotation of that direction between consecutive windows
</pre>

Three window scales: W=3, W=7, W=14. W=3 drowns in noise. W=7 catches sharp point
events. W=14 catches slow structural regimes.

![E3 Signal W=14 with OONI](/plots/trackB_e3_w14_signal_mar23.png)
14-day window (W=14) geometric signal with OONI co-movement.

*Top: Δρ over time. Blue fill means the two observers are finding clearer shared
structure (the dominant axis is strengthening); red fill means the dominant axis
has inverted, a competing direction is now stronger, consistent with infrastructure
stress.*

*Middle: θ. The 1.4° collapse after Feb-20 and the 80.4° spike at Mar-06 are
visible.*

*Bottom: OONI global anomaly rate. The Feb-20 to Mar-06 ramp and simultaneous peak
are visible. Green dashed markers show co-movement dates.*

---

## The Honest Limitation

Both observers were frozen across the entire 98-day period. Trained on early data,
never updated. As the network evolved, their representations drifted out of
distribution.

The original scoring threshold was set above the dataset maximum, so the condition
was always true and everything returned the same label. The fix was threshold
calibration: anchor to the natural scale of the data, verify against ground truth,
confirm no false positives. The calibrated threshold is DELTA_RHO_THRESHOLD =
0.0016, derived from the 98-day natural scale (±0.003, std=0.00127).

<u>The Δρ classification confirms the signal is real</u>. The 58 unstable windows across
98 days are distributed randomly across the timeline and do not cluster around
the known events. The three primary_signal detections are precisely at the events.
<u>The signal is not an artifact of the frozen observers</u>. It is distinct from background.

---

## Five Dates

<pre style="overflow-x:auto; margin-top:1rem; margin-bottom:1rem;">
December 19-20, 2025
Largest internal relay-side geometric event in 98 days.
Δρ = +0.0022, θ = 60.78°. primary_signal.
OONI anomaly rate dropped, no client-side spike.
Cause: unknown.

February 17, 2026
FBI breach of its Digital Collection System Network.
Pen register and trap-and-trace data exposed.
Identities of surveillance subjects exposed.
Not publicly known until March 3-5.
Same entry vector as Salt Typhoon 2024. Attribution unconfirmed.
(AP, CNN, Politico, Reuters, WSJ)

February 20, 2026
Cloudflare BGP outage, ~1,100 BYOIP prefixes withdrawn, 6 hours.
E3 geometric trigger: Δρ = −0.0017, θ = 66.97°. primary_signal.
Axis locked to 1.4° minimum the following day.
OONI began rising. Relay-side only. Cause: unknown.

March 1, 2026
Iranian drone strikes. AWS ME-CENTRAL-1.
Two availability zones destroyed.
Tor relays on UAE AWS degraded. Baseline in Tor geometry.
(Tom's Hardware, CNBC)

March 6, 2026
E3 rank #1 in 98 days. Δρ = +0.0025, θ = 80.42°. primary_signal.
Score above noise maximum. Metric never fires on noise at W=14.
OONI 18.2% globally, highest in 270,285 records across 181 countries.
Ex-Russia: 7.48%. Ramp +1.78 points confirmed.
Both peaked simultaneously.
February 20 + 14 = March 6. Exactly.
</pre>

---

## What This Post Is Not Claiming

The framework did not predict the drone strike.

The framework did not detect the FBI breach.

February 20 has not been attributed to any single cause. The Cloudflare BGP outage
is documented and coincident. The FBI breach is documented context. Which caused
the geometric shift, or whether both contributed, is unknown.

The Salt Typhoon connection to the February 17 breach has not been confirmed.

The OONI rise has not been confirmed as access disruption. It may be probe density.

No individuals are identified. No traffic was inspected. No content was read.

This is a geometric finding. Security implications, if any, are for others to assess.

---

## What This Post Is Claiming

The February 20th event validated the core thesis of the Drift as Deformation
framework: structural change in the Tor relay network manifests as geometric
deformation of latent space, not as motion. The detector found the signal. The
calibrated threshold held across 98 days of background. The stiff axis now has
an empirical reference frame.

Three detections. Two distinct mechanisms confirmed. A research program is now
anchored to real data.

The next experiments build on this foundation. Multi-scale causal ordering tests
to understand how signals propagate across window hierarchies. Synthetic
perturbation experiments to isolate encoder contribution to the signal. Extension
of the observation window beyond 98 days. The framework is open:
[tor-meta-framework on GitHub](https://github.com/vib5252/tor-meta-framework).

---

## The Open Question

December 19-20 is unexplained. Pure relay-side, no external footprint, still
waiting.

February 20 sits at the intersection of two documented external events: a Cloudflare
BGP withdrawal that briefly disrupted routing for ~1,100 prefixes globally, and
three days earlier, a breach of the FBI's surveillance warrant system using the
same ISP vendor backdoor technique that Salt Typhoon used in 2024 to access federal
target selection lists.

The Cloudflare outage explains infrastructure stress. It is consistent with the
negative Δρ signature. It does not explain why the axis locked to its minimum value
the following day, or why OONI began a 14-day climb beginning February 21st.

When a surveillance target list moves, to peer intelligence services, to the
subjects themselves, to networks that have learned they are burned, the people on
it who use Tor do not stop. They change how they use it. New entry guards. New
circuits. Changed timing. That behavioral shift at scale does not change user
volume. It changes relay co-activation geometry. That is exactly what this
detector measures.

Whether that is what happened on February 20th is unknown.

The framework has no opinion about why. It was watching shape, not cause.

**What changed on February 20th?**

---

## What Comes Next

The geometry did not resolve after March 6. As of March 7, all three window scales
showed θ above background. The signal continued rather than recovering.

The Δρ classification has been run across the full 98-day dataset. All three
validated events detected as primary signal, Monte Carlo null distribution
established. NetBlocks country-level data needs to be correlated with December
19-20 and February 20. Observer retraining on the full 98-day range is pending.

The second post covers March 7th.

---

*Updated March 23, 2026. Δρ classification validated across full 98-day dataset.
3/3 events confirmed as primary signal. Two distinct geometric mechanisms identified.
Monte Carlo null distribution established.*

---

*This post is a sequel to [The Geometry Told Us]({{< relref "posts/geometry-told-us.md" >}}),
which established that two independent observers agree on one dominant shared
direction in Tor relay behavior. ρ₁ = 0.826. The stiff axis. This post asks whether
that axis moves. And when it does, what was happening in the world.*

*The analysis pipeline is published at [tor-meta-framework](https://github.com/vib5252/tor-meta-framework).
No causation is claimed. No individuals are identified. No traffic was inspected.*
