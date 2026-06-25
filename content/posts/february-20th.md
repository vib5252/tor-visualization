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

This post documents the exploratory analysis that preceded the formal study. The
observations described here were made across an early 98-day window before the
formal observation period was established. The definitive findings, including formal
falsification of the relay departure hypothesis for February 20th, are published in
the paper: [Latent Geometry as a Structural Monitor](https://arxiv.org/abs/2605.20391)
(arXiv:2605.20391). The February 20th event is the only externally confirmed event
in the formal study. Everything else in this post is exploratory geometry.

---

## The Confirmed Event: February 20, 2026

On February 20, 2026, at approximately 16:00 UTC (Coordinated Universal Time), [Cloudflare](https://blog.cloudflare.com/cloudflare-outage-february-20-2026/) withdrew BGP prefixes
affecting ~1,100 BYOIP (Bring Your Own IP) customer routes globally, with 4 full prefix withdrawals
confirmed by RIPE NCC Routing Information Service (RIPE RIS) BGP routing data.
BGP (Border Gateway Protocol) is the routing system that controls how traffic moves
between internet providers. A prefix withdrawal means a block of IP addresses became
unreachable from the broader internet for the duration of the outage, approximately
six hours.

The pipeline registered a structural anomaly classified as REGIME_E (stiff-axis
fracture at the population level):

<div style="overflow-x:auto; margin-top:1rem; margin-bottom:1.5rem;">
<table style="border-collapse:collapse; width:100%; font-size:0.9em;">
<thead>
<tr style="border-bottom:2px solid #666;">
<th style="text-align:left; padding:0.4rem 0.8rem;">Channel</th>
<th style="text-align:left; padding:0.4rem 0.8rem;">Signal</th>
<th style="text-align:left; padding:0.4rem 0.8rem;">What it means</th>
</tr>
</thead>
<tbody>
<tr style="border-bottom:1px solid #444;">
<td style="padding:0.4rem 0.8rem;">Global EJT z-score</td>
<td style="padding:0.4rem 0.8rem;">−4.38</td>
<td style="padding:0.4rem 0.8rem;">The full relay population moved 4.38 standard deviations into the load-bearing structural axes simultaneously</td>
</tr>
<tr style="border-bottom:1px solid #444;">
<td style="padding:0.4rem 0.8rem;">θ (axis rotation)</td>
<td style="padding:0.4rem 0.8rem;">67.13°</td>
<td style="padding:0.4rem 0.8rem;">The shared direction between the two observers rotated nearly five times the stable background of 14.6°</td>
</tr>
<tr>
<td style="padding:0.4rem 0.8rem;">Δρ (directional dominance)</td>
<td style="padding:0.4rem 0.8rem;">−0.0017</td>
<td style="padding:0.4rem 0.8rem;">A competing direction overtook the dominant one, consistent with infrastructure stress rather than reorganization</td>
</tr>
</tbody>
</table>
</div>

Two signals fired independently. The CCA bridge detected significant observer
divergence. The global EJT (Eigendecomposition of the Jacobian Trace, which measures
how much the relay population moved into the load-bearing structural axes) fired
at z = −4.38. The per-cluster signal for the exit relay group was sub-threshold,
meaning the deformation was distributed across the full population rather than
concentrated in any single role cluster. This is consistent with a network-wide
routing disruption, not a targeted attack on one relay type.

**The relay departure hypothesis has been formally falsified.**

Forensic analysis compared the 116 relays present on February 19th but absent on
February 20th against confirmed Cloudflare IPv4 prefixes from RIPE RIS BGP routing
data. Zero of 116 departed relay IP addresses matched any Cloudflare prefix.

The geometry moved. The relays did not leave. The conclusion is connectivity
degradation without topology change: routing paths were disrupted while relays
remained listed in the Tor consensus. Standard relay-count monitoring would have
seen nothing. The geometric detector saw a 4.38 standard deviation event.

If you are new to this space: Tor is a global network of volunteer-operated relays
that anonymize internet traffic by routing it through multiple hops. OONI (Open
Observatory of Network Interference) is an independent project that monitors
whether users can successfully connect to Tor across 181 countries. The relay
population collectively forms a geometric structure. When that structure deforms,
something changed in how the network is being used. This detector measures that
deformation. It never sees who is connecting or what they are sending.
It only sees shape.

---

## The Framework

191 behavioral features per relay, per day, sourced from the public Tor Onionoo API
(Application Programming Interface). No packets inspected. No content. No individual identities.

The first observer, a geometric encoder, maps the relay population into a
32-dimensional latent space and learns which directions in that space are
load-bearing (the stiff axes) and which absorb movement freely (the soft axes).
The second observer, a thermodynamic encoder, learns the energy landscape of
relay co-activation patterns, which relays tend to behave similarly and which
diverge.

They are intentionally different architectures. When they independently agree on a
direction, that agreement is the signal. When they stop agreeing, that is the
detector firing.

Canonical Correlation Analysis (CCA) measures that agreement across sliding time
windows. Two numbers per day:

<pre style="overflow-x:auto; margin-top:1rem; margin-bottom:1rem;">
Δρ = ρ₁ − ρ₂   did one shared direction clearly dominate over the next best?
θ  = arccos(|v₁ · v₂|)   how much did that direction rotate since yesterday?
</pre>

On a stable day, θ sits around 14.6 degrees. The network drifts gently. A rotation
of 60 to 80 degrees means the two observers have fundamentally reoriented. They
are no longer describing the same structure they were describing yesterday.

The stiff subspace (k = 9 dimensions) is invariant across all 67 formal observation
windows at the 90% trace mass threshold. It was not chosen. It emerged from the
network geometry. The Monte Carlo null baseline yields ρ₁ = 0.509 across 1,000
iterations of Gaussian noise. Empirical Tor data yields ρ₁ = 0.9992. The 16.8σ
separation confirms the structural agreement is a property of the Tor relay
population, not of the pipeline architecture.

![E3 Signal W=14 with OONI](/plots/trackB_e3_w14_signal_mar23.png)
14-day window (W=14) geometric signal with OONI co-movement.

*Top: Δρ over time. Blue fill means the dominant direction is strengthening,
consistent with structural reorganization. Red fill means a competing direction
has overtaken it, consistent with infrastructure stress.*

*Middle: θ, the daily rotation of the shared direction. The near-zero collapse
after Feb-20 means both observers locked onto the same direction and stopped
moving, an unusually rigid state. The large spike at Mar-06 means they rotated
close to a right angle overnight.*

*Bottom: OONI global anomaly rate. The climb beginning Feb-21 and the simultaneous
peak on Mar-06 are visible. Green dashed markers show dates where the geometric
signal and the OONI rate moved together.*

---

## Event Classification

The formal paper defines six geometric event classifications requiring no causal
attribution:

<pre style="overflow-x:auto; margin-top:1rem; margin-bottom:1rem;">
PRECURSOR   Thermodynamic fragmentation before geometric deformation.
            The thermodynamic observer fires. The geometric observer is blind.
            Earliest detectable signal in the pipeline.

REGIME_S    Elastic population surge absorbed without structural fracture.
            Large relay influx that the network absorbs through soft axes.
            Theoretical class. Signature identified forensically within
            REGIME_E events during Feb 05-13 surge.

REGIME_D    Localized geometric deformation. Geometric observer fires.
            Thermodynamic observer quiet. Contained to one or two windows.

REGIME_E    Stiff-axis fracture at the population level.
            Global EJT z-score below -2.0. FPR (False Positive Rate) = 0.0% on 24 stable windows.
            February 20, 2026 is the only externally confirmed REGIME_E event.

REGIME_K    Administrative maintenance. Forensic checklist required to
            distinguish from hostile guard-layer activity.
            Theoretical class. Signature confirmed forensically Apr 07-08.

NORMAL      No gates fire. Population movements absorbed elastically.
</pre>

February 20th is REGIME_E. The geometry did not just shift. The full relay
population mean moved into the load-bearing structural axes simultaneously.
That is what a 4.38 standard deviation global EJT event looks like.

March 6th is REGIME_D: a localized guard cluster reorientation visible only to
the geometric observer, with no thermodynamic fragmentation. The two events look
similar at the CCA level. The event taxonomy distinguishes them.

---

## Gate Activation Log

67 observation windows. One externally confirmed event. The table below is the
detection record. Full classification plots and channel values are in the
[paper](https://arxiv.org/abs/2605.20391).

<div style="overflow-x:auto; margin-top:1rem; margin-bottom:1.5rem;">
<table style="border-collapse:collapse; width:100%; font-size:0.9em;">
<thead>
<tr style="border-bottom:2px solid #666;">
<th style="text-align:left; padding:0.4rem 0.8rem;">Date</th>
<th style="text-align:left; padding:0.4rem 0.8rem;">Classification</th>
<th style="text-align:left; padding:0.4rem 0.8rem;">Key signal</th>
<th style="text-align:left; padding:0.4rem 0.8rem;">Status</th>
</tr>
</thead>
<tbody>
<tr style="border-bottom:1px solid #444;">
<td style="padding:0.4rem 0.8rem;">Jan 23-26</td>
<td style="padding:0.4rem 0.8rem;">PRECURSOR</td>
<td style="padding:0.4rem 0.8rem;">Thermodynamic fragmentation. CV (Coefficient of Variation) reached dataset maximum of 19.3. Geometric observer blind.</td>
<td style="padding:0.4rem 0.8rem;">Investigated</td>
</tr>
<tr style="border-bottom:1px solid #444;">
<td style="padding:0.4rem 0.8rem;">Jan 27</td>
<td style="padding:0.4rem 0.8rem;">REGIME_D</td>
<td style="padding:0.4rem 0.8rem;">Internal reorganization. Geometric observer fired. Thermodynamic observer quiet.</td>
<td style="padding:0.4rem 0.8rem;">Investigated</td>
</tr>
<tr style="border-bottom:1px solid #444;">
<td style="padding:0.4rem 0.8rem;">Feb 05-13</td>
<td style="padding:0.4rem 0.8rem;">REGIME_E</td>
<td style="padding:0.4rem 0.8rem;">Coordinated residential relay surge. 7,600 single-operator relays. 72.9% rejected by Tor directory authorities. Distinguished from Feb 20 by δmg = 5.17 (population flood) vs Feb 20 δmg = 2.88 (connectivity degradation).</td>
<td style="padding:0.4rem 0.8rem;">Investigated</td>
</tr>
<tr style="border-bottom:1px solid #444;">
<td style="padding:0.4rem 0.8rem;">Feb 20</td>
<td style="padding:0.4rem 0.8rem;">REGIME_E</td>
<td style="padding:0.4rem 0.8rem;">Global EJT z = −4.38. θ = 67.13°. Δρ = −0.0017. Cloudflare BGP withdrawal confirmed by RIPE RIS. Relay departure hypothesis falsified: 0/116 departed relay IPs matched Cloudflare prefixes.</td>
<td style="padding:0.4rem 0.8rem;"><strong>CONFIRMED</strong></td>
</tr>
<tr style="border-bottom:1px solid #444;">
<td style="padding:0.4rem 0.8rem;">Mar 06</td>
<td style="padding:0.4rem 0.8rem;">REGIME_D</td>
<td style="padding:0.4rem 0.8rem;">Localized guard cluster reorientation. Geometric observer fired. Thermodynamic observer quiet. δmg = 2.55, no population flood.</td>
<td style="padding:0.4rem 0.8rem;">Investigated</td>
</tr>
<tr style="border-bottom:1px solid #444;">
<td style="padding:0.4rem 0.8rem;">Apr 03</td>
<td style="padding:0.4rem 0.8rem;">MODE_F</td>
<td style="padding:0.4rem 0.8rem;">CCA rotation elevated (θ = 109.7°) but global EJT sub-threshold. Geometric reorientation without confirmed stiff-axis fracture.</td>
<td style="padding:0.4rem 0.8rem;">Uninvestigated</td>
</tr>
<tr>
<td style="padding:0.4rem 0.8rem;">Apr 07-08</td>
<td style="padding:0.4rem 0.8rem;">REGIME_E / REGIME_K</td>
<td style="padding:0.4rem 0.8rem;">Forensic checklist confirmed administrative fleet restart. Returning relays had mean restart age of 28 days vs 603 days for non-returning cohort, a 21:1 ratio consistent with coordinated maintenance, not hostile activity.</td>
<td style="padding:0.4rem 0.8rem;">Investigated</td>
</tr>
</tbody>
</table>
</div>

## The Honest Limitation

Both observers were frozen across the observation period. Trained on early data,
never updated. As the network evolved, their representations drifted out of
distribution.

The original scoring threshold was set above the dataset maximum, so the condition
was always true and everything returned the same label. The fix was threshold
calibration: anchor to the natural scale of the data, verify against ground truth,
confirm no false positives. The calibrated threshold DELTA_RHO_THRESHOLD = 0.0016
is derived from the formal observation dataset natural scale (±0.003, std=0.00127).

<u>The classification confirms the signal is real.</u> The unstable windows across the
observation period are distributed randomly across the timeline and do not cluster
around the confirmed events. <u>The signal is not an artifact of the frozen observers.</u>
It is distinct from background.

The frozen architecture is a current limitation and a future direction. An adaptive
encoder that updates continuously as the network evolves would maintain a live
baseline rather than a static one. That is a next step, not a solved problem.

---

## Five Dates

Each confirmed detection marks a structural transition. The dates below are the
transitions and their external context. February 20th is the only externally
confirmed event. All others are geometric observations without external attribution.

<pre style="overflow-x:auto; margin-top:1rem; margin-bottom:1rem;">
December 19-20, 2025
From the earlier exploratory analysis. Predates the formal 67-window study.
Δρ = +0.0022 (dominant direction strengthened sharply).
θ = 60.78° (four times the stable background of 14.6°).
OONI anomaly rate dropped, no client-side spike.
Cause: unknown. No external attribution.

February 17, 2026
FBI breach of its Digital Collection System Network.
Pen register and trap-and-trace data exposed.
Identities of surveillance subjects exposed.
Not publicly known until March 3-5.
Same entry vector as Salt Typhoon 2024. Attribution unconfirmed.
(AP, CNN, Politico, Reuters, WSJ)

February 20, 2026
Cloudflare BGP outage. ~1,100 BYOIP prefixes withdrawn. Six hours.
See: https://blog.cloudflare.com/cloudflare-outage-february-20-2026/
REGIME_E: Global EJT z = −4.38. θ = 67.13°. Δρ = −0.0017.
Relay departure hypothesis formally falsified: 0/116 departed relay IPs
matched any Cloudflare prefix (verified against RIPE RIS BGP data).
Connectivity degradation without topology change: confirmed.
The only externally confirmed event in the formal study.

March 1, 2026
Iranian drone strikes. AWS ME-CENTRAL-1.
Two availability zones destroyed.
Tor relays on UAE AWS degraded. No geometric signature.
The detector did not fire. Not every external event deforms the network.
(Tom's Hardware, CNBC)

March 6, 2026
REGIME_D: Localized guard cluster reorientation.
θ = 80.42° (close to a right angle, nearly six times the stable background of 14.6°).
Geometric observer fired. Thermodynamic observer quiet.
OONI 18.2% globally, highest in 270,285 records across 181 countries.
Ex-Russia: 7.48%. Ramp +1.78 points confirmed.
Both peaked simultaneously. Cause: unknown. No external attribution.
</pre>

---

## What This Post Is Not Claiming

The framework did not predict the drone strike.

The framework did not detect the FBI breach.

February 20 is confirmed as a structural anomaly coinciding with a documented
Cloudflare infrastructure event. The relay departure hypothesis has been formally
falsified. The cause of the geometric shift is connectivity degradation. Whether
the FBI breach contributed is unknown and not claimed.

The Salt Typhoon connection to the February 17 breach has not been confirmed.

The OONI rise has not been confirmed as access disruption. It may be probe density.

No individuals are identified. No traffic was inspected. No content was read.

This is a geometric finding. Security implications, if any, are for others to assess.

---

## What This Post Is Claiming

The February 20th event is the first externally confirmed validation of the Drift
as Deformation framework. The detector identified a structural anomaly coinciding
with a documented infrastructure failure. The relay departure hypothesis was
formally falsified by forensic cross-reference against RIPE RIS BGP routing data.
Connectivity degradation without topology change is a real and previously invisible
failure mode. The geometric pipeline detected it. Standard relay-count monitoring
would not have.

The stiff subspace is invariant. The Monte Carlo separation is 16.8σ. The primary
detection gates (CV gate and global EJT gate) achieve 0.0% false positive rate on
24 confirmed stable windows.

The framework is open: [tor-meta-framework on GitHub](https://github.com/vib5252/tor-meta-framework).
The formal paper: [arXiv:2605.20391](https://arxiv.org/abs/2605.20391).

---

## The Open Question

What changed on February 20th?

The [Cloudflare](https://blog.cloudflare.com/cloudflare-outage-february-20-2026/) BGP withdrawal is confirmed. 
The geometric signal it produced is formally documented. The relay departure hypothesis has been falsified.
Connectivity degradation without topology change is a real and previously invisible failure mode.
That part is answered.

What remains open is what followed. The θ collapsed to 1.4° the day after the
event, the most rigid state the detector recorded across the full observation period.
OONI began a 14-day climb from that point forward. Those two signals have no
confirmed explanation.

Three days before February 20th, the FBI detected a breach of its surveillance
warrant system using the same ISP vendor backdoor technique that Salt Typhoon used
in 2024 to access federal target selection lists. That context is documented. It is
not claimed as a cause.

When a surveillance target list moves, to peer intelligence services, to the
subjects themselves, to networks that have learned they are burned, the people on
it who use Tor do not stop. They change how they use it. New entry guards. New
circuits. Changed timing. That behavioral shift at scale does not change user
volume. It changes relay co-activation geometry. That is exactly what this
detector measures. Whether that is what happened here is unknown.

March 7th did not settle. All three window scales showed θ above background the
day after the strongest signal in the dataset. The geometry was still moving.
The second post covers what happened next.

The confirmed findings are formally documented in the paper:
[Latent Geometry as a Structural Monitor](https://arxiv.org/abs/2605.20391)
(arXiv:2605.20391). The framework is open:
[tor-meta-framework on GitHub](https://github.com/vib5252/tor-meta-framework).

---

*Updated May 19, 2026. Formal study published on arXiv (arXiv:2605.20391).
67 observation windows validated. February 20 is the only externally confirmed
event. Relay departure hypothesis formally falsified. Monte Carlo separation: 16.8σ.*

---

*This post is a sequel to [The Geometry Told Us]({{< relref "posts/geometry-told-us.md" >}}),
which established that two independent observers agree on one dominant shared
direction in Tor relay behavior. The formal study confirmed ρ₁ = 0.9992, a 16.8σ
separation above the Monte Carlo null. The stiff axis. This post asks whether
that axis moves. And when it does, what was happening in the world.*

*The analysis pipeline is published at [tor-meta-framework](https://github.com/vib5252/tor-meta-framework).
No causation is claimed. No individuals are identified. No traffic was inspected.*
