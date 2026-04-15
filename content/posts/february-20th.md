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

This is an important point. In most systems, two models disagreeing is a problem.
Here it is the measurement. When the network is stable, both observers agree on
which direction matters. When something structural happens, they stop agreeing.
The bigger the disagreement, the more significant the shift. The detector is not
looking for anomalies in traffic. It is listening for the moment two independent
mathematical lenses stop seeing the same thing.

This post documents the first empirical validation of that thesis. Over 98 days,
the detector watched the Tor network without updating its observers. Three times,
the signal crossed the calibrated detection threshold. All three coincided with
documented external events. The Iranian drone strike that destroyed two AWS data
centers did not move it. Routine network churn did not move it. Only three events
crossed the threshold, and the detector found all three.

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
<th style="text-align:left; padding:0.4rem 0.8rem;">Δρ (did one direction clearly dominate?)</th>
<th style="text-align:left; padding:0.4rem 0.8rem;">θ (how much did that direction rotate?)</th>
<th style="text-align:left; padding:0.4rem 0.8rem;">Label</th>
<th style="text-align:left; padding:0.4rem 0.8rem;">External signal</th>
</tr>
</thead>
<tbody>
<tr style="border-bottom:1px solid #444;">
<td style="padding:0.4rem 0.8rem;">Dec-20, 2025</td>
<td style="padding:0.4rem 0.8rem;">+0.0022</td>
<td style="padding:0.4rem 0.8rem;">60.78°</td>
<td style="padding:0.4rem 0.8rem;">primary_signal (validated detection)</td>
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

To read this table: θ is how much the shared direction between the two observers
rotated compared to the previous window. On a stable day, that rotation is small,
around 14 degrees on average, the kind of gentle drift you would expect from a
live network with thousands of relays coming and going. A rotation of 60, 67, or
80 degrees means the two observers have fundamentally reoriented. They are no
longer describing the same structure they were describing yesterday. Something in
the relay population changed enough to move both of them.

Two distinct mechanisms are visible in the Δρ column. Δρ measures how much one
shared direction dominates over the next best alternative. Dec-20 and Mar-06 show
positive Δρ: the dominant direction strengthened, consistent with structural
reorganization of the relay population. Feb-20 shows negative Δρ: the dominant
direction weakened and a competing direction became stronger, consistent with
infrastructure stress rather than reorganization. The geometry is not just
detecting that something happened. It is distinguishing how.

March 1, Iranian drone strikes on AWS ME-CENTRAL-1, stays baseline. No geometric
signature in the Tor stiff axis. The detector is not triggering on everything.

If you are new to this space: Tor is a global network of volunteer-operated relays
that anonymize internet traffic by routing it through multiple hops. OONI (Open
Observatory of Network Interference) is an independent project that monitors
whether users can successfully connect to Tor across 181 countries. BGP (Border
Gateway Protocol) is the routing system that controls how traffic is directed
across the internet. The relay population collectively forms a geometric structure.
When that structure deforms, something changed in how the network is being used.
This detector measures that deformation. It never sees who is connecting or what
they are sending. It only sees shape.

---

## The Full Timeline: 98-days

W= refers to the sliding window size in days used to compute each measurement.
A larger window smooths over short bursts and catches slower structural shifts.

14-day window (W=14) Classification
![E3 Window Classification W=14](/plots/delta_rho_classification_w14.png)

*Left: principal angle θ. Red dots are primary_signal events: validated detections
where both Δρ and θ crossed the calibrated threshold simultaneously. Orange dots
are unstable windows where the CCA eigenvalue gap compressed toward zero, making
the directional measurement unreliable. These are not detections. They are periods
where the compass needle had no strong preferred direction. Grey dots are stable
background. The orange windows are distributed randomly across 98 days and do not
cluster around the validated events, which means the instability is not driving
the signal.*

*Right: eigenvalue gap Δρ with calibrated threshold. The three primary_signal
events are precisely at the validated dates.*

The 7-day window (W=7) classification shows the December precursor one day earlier:
![E3 Window Classification W=7](/plots/delta_rho_classification_w7.png)

*Left: principal angle θ at W=7. Same color scheme. The December 19 precursor
appears one day before the W=14 detection on December 20, showing that the signal
was building before the longer window caught it.*

*Right: eigenvalue gap Δρ at W=7. Blue dots above threshold are positive excursions
where the dominant direction strengthened. Red dots below are negative excursions
where a competing direction overtook it. W=14 amplified sub-threshold structure
that W=7 registered but did not elevate to primary_signal.*

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
direction, that agreement is the signal. When they stop agreeing, that is the
detector firing.

Canonical Correlation Analysis (CCA) measures that agreement across sliding time
windows. It produces two numbers per day:

<pre style="overflow-x:auto; margin-top:1rem; margin-bottom:1rem;">
Δρ = ρ₁ − ρ₂   how much one shared direction dominates over the next best
θ  = arccos(|v₁ · v₂|)   how much that direction rotated since yesterday
</pre>

On a stable day, θ sits around 14 degrees. The network drifts gently. Both
observers track it together. When something structural happens, θ jumps. A 60
degree rotation means the shared direction has fundamentally reoriented overnight.
An 80 degree rotation, which happened on March 6th, is close to a right angle.
The two observers were pointing in almost entirely different directions compared
to the day before.

Three window scales: W=3, W=7, W=14. W=3 drowns in noise. W=7 catches sharp
point events. W=14 catches slow structural regimes, month-long changes in how
the relay population organizes itself.

![E3 Signal W=14 with OONI](/plots/trackB_e3_w14_signal_mar23.png)
14-day window (W=14) geometric signal with OONI co-movement.

*Top: Δρ over time. Blue fill means the dominant direction is strengthening. Red
fill means a competing direction has overtaken it, consistent with infrastructure
stress.*

*Middle: θ, the daily rotation of the shared direction. The near-zero collapse
after Feb-20 means both observers locked onto the same direction and stopped
moving, an unusually rigid state. The 80 degree spike at Mar-06 means they
rotated almost a full right angle overnight, the strongest structural shift in
98 days.*

*Bottom: OONI global anomaly rate. The climb beginning Feb-21 and the simultaneous
peak on Mar-06 are visible. Green dashed markers show dates where the geometric
signal and the OONI rate moved together.*

---

## The Honest Limitation

Both observers were frozen across the entire 98-day period. Trained on early data,
never updated. As the network evolved over 98 days, their internal representations
drifted out of distribution. They were seeing the network through a lens calibrated
to November 2025.

The original scoring threshold was set above the dataset maximum, so the condition
was always true and everything returned the same label. The fix was threshold
calibration: anchor to the natural scale of the data, verify against ground truth,
confirm no false positives. The calibrated threshold is DELTA_RHO_THRESHOLD =
0.0016, derived from the 98-day natural scale (±0.003, std=0.00127).

<u>The Δρ classification confirms the signal is real</u>. The 58 unstable windows across
98 days are distributed randomly across the timeline and do not cluster around
the known events. The three primary_signal detections are precisely at the events.
<u>The signal is not an artifact of the frozen observers</u>. It is distinct from background.

The frozen architecture is a current limitation and also a future direction. An
adaptive encoder that updates continuously as the network evolves would maintain
a live baseline rather than a static one. That would make the detector sensitive
to slower structural changes that the current frozen architecture misses, and
would remove the drift problem entirely. That is a next step, not a solved problem.

---

## Five Dates

Each validated detection marks a structural transition. The network entered a
different geometric state. December 20th was one transition. February 20th was
another. March 6th was the peak of what February 20th started. The dates below
are the transitions and their external context.

<pre style="overflow-x:auto; margin-top:1rem; margin-bottom:1rem;">
December 19-20, 2025
Largest internal relay-side geometric event in 98 days.
Δρ = +0.0022 (dominant direction strengthened sharply).
θ = 60.78° (four times the stable background of 14°).
primary_signal. The relay population structurally reorganized.
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
E3 geometric trigger (combined Δρ and θ detection signal):
Δρ = −0.0017 (competing direction overtook the dominant one).
θ = 66.97° (nearly five times the stable background).
primary_signal. Infrastructure stress signature.
The following day, θ collapsed to 1.4°, the lowest in 98 days.
Both observers locked onto the same rigid direction and stopped moving.
OONI began climbing. Relay-side only. Cause: unknown.

March 1, 2026
Iranian drone strikes. AWS ME-CENTRAL-1.
Two availability zones destroyed.
Tor relays on UAE AWS degraded. Baseline in Tor geometry.
The geometry did not move. Not every external event deforms the network.
(Tom's Hardware, CNBC)

March 6, 2026
E3 rank #1 in 98 days.
Δρ = +0.0025 (strongest dominant direction in the dataset).
θ = 80.42° (close to a right angle, nearly six times the stable background).
primary_signal. Score above noise maximum at W=14.
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
negative Δρ signature. It does not explain why θ collapsed to 1.4° the following
day, the most rigid state the detector recorded in 98 days, or why OONI began a
14-day climb from that point forward.

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

After a primary_signal event, you would expect θ to return toward the stable
background of around 14 degrees. The axis shifted, the trigger fired, and
normally the network settles. The geometry finds a new resting position.

March 7th did not settle. All three window scales showed θ above background.
The geometry was still moving the day after the strongest signal in 98 days.
That means either the network was still reorganizing, or the February 20th
transition opened a regime that had not yet resolved, or both. The second post
covers what happened next.

The Δρ classification has been run across the full 98-day dataset. All three
validated events detected as primary_signal, Monte Carlo null distribution
established. NetBlocks country-level data needs to be correlated with December
19-20 and February 20. Observer retraining on the full 98-day range is pending.

---

*Updated March 23, 2026. Δρ classification validated across full 98-day dataset.
3/3 events confirmed as primary_signal. Two distinct geometric mechanisms identified.
Monte Carlo null distribution established.*

---

*This post is a sequel to [The Geometry Told Us]({{< relref "posts/geometry-told-us.md" >}}),
which established that two independent observers agree on one dominant shared
direction in Tor relay behavior. ρ₁ = 0.826. The stiff axis. This post asks whether
that axis moves. And when it does, what was happening in the world.*

*The analysis pipeline is published at [tor-meta-framework](https://github.com/vib5252/tor-meta-framework).
No causation is claimed. No individuals are identified. No traffic was inspected.*
