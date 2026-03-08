+++
title = "What Changed on February 20th?"
date = 2026-03-07
tags = ["Geometry", "CCA", "E3", "OONI"]
math = true
weight = 1
+++

On February 17, 2026, the FBI detected a breach of its Digital Collection System
Network — the internal system used to manage wiretapping and foreign intelligence
surveillance warrants. According to a notification sent to Congress and reviewed by
the Associated Press, the breached system contained pen register and trap-and-trace
surveillance returns, and personally identifiable information pertaining to subjects
of FBI investigations. The attacker's techniques were described as sophisticated.
The breach was not publicly disclosed until March 3-5.

Three days later, on February 20th, the geometry of the Tor network changed.

This post is about what that means — and why the honest answer is: I don't know yet.

*W=14 days — Feb 20 trigger (θ=67°), Mar 6 rank #1 (θ=80.4°):*
![E3 Signal W=14](/plots/e3_w14_signal.png)

---

## The Framework

For the past 98 days, two independent machine learning observers have been watching
the Tor relay network. Not the traffic. Not the users. The shape.

23 behavioral features per relay, per day: bandwidth, uptime, restart frequency,
address stability, consensus weight, flag history, and related signals. No packets
inspected. No content. No individual identities.

The first observer — a Variational Autoencoder — maps the relay population into a
32-dimensional geometric space. The second — a Restricted Boltzmann Machine — learns
the energy landscape of relay co-activation patterns. Which relays tend to behave
similarly. Which diverge.

They are intentionally different architectures. When they independently agree on a
direction — that agreement is the signal.

Canonical Correlation Analysis measures that agreement across sliding time windows.
The output is two numbers per day:

<pre style="overflow-x:auto; margin-top:1rem; margin-bottom:1rem;">
Δρ = ρ₁ − ρ₂
     how much does one shared direction dominate over the second-best?
     Across 98 days this number never exceeded 0.005.

θ  = arccos(|v₁ · v₂|)
     how much did that dominant direction rotate between
     yesterday's window and today's?
     Background mean at the 14-day window: 14.5°.
</pre>

Three window sizes: W=3, W=7, W=14 days. Each resolves a different timescale.
W=3 drowns in noise. W=7 catches sharp point events. W=14 catches slow structural
regimes — month-long changes in how the relay population organizes itself.

This multi-scale structure was not designed. It emerged from laying three plots
side by side.

---

## The Honest Limitation — Stated First

Both observers were frozen across the entire 98-day period. Trained on early data,
never updated. As the network evolved, their representations drifted out of
distribution. The eigenvalue gap compressed toward zero throughout.

A principled statistical filter would have left almost no data to analyze. The system
was operating at the edge of its sensitivity. Signal and noise were mixed throughout.
What follows is a ranked list of candidates — not certified statistical outliers.
Monte Carlo validation is pending.

That limitation is placed here, at the front, because what follows is worth reading
precisely — not through rose-tinted glass.

It is also, in a strange way, what makes the findings worth noting at all.

---

## The Timeline

### December 19-20, 2025

The largest internal signal in the 98-day dataset.

Δρ jump = 0.008 at the 7-day window. By a significant margin — nothing else came
close. The geometry of the relay population shifted violently, by this framework's
standards.

The client-side picture was the opposite. OONI — the Open Observatory of Network
Interference, which tracks whether users can successfully connect to Tor across 181
countries — showed no corresponding spike. The global anomaly rate dropped slightly
on December 20th.

No code changes. No external events identified.

Something reorganized inside the relay network. Users experienced nothing. Not a
single external trace. A network built for anonymity and resilience shifted its
internal geometry — invisibly.

Cause: unknown. Still unexplained as of today.

---

### February 17, 2026

The FBI detected abnormal log activity on the Digital Collection System Network.
The system that manages court-approved wiretaps and FISA surveillance warrants
during investigations.

The exposed data included pen register returns — outgoing connection metadata from
surveillance targets: who they contacted, when, for how long — and trap-and-trace
returns, their mirror image. And the identities of the people under surveillance.

Sophisticated techniques. A commercial ISP vendor's infrastructure leveraged to
exploit FBI network controls. Congress notified weeks later. Not public on
February 17th. ([CNN](https://www.cnn.com/2026/03/05/politics/fbi-investigating-cyber-breach-critical-surveillance-network), [Politico](https://www.politico.com/news/2026/03/06/fbi-hack-white-house-nsa-cisa-00817072))

---

### February 20, 2026

Three days after the FBI detected the breach —

The E3 framework registered a structural trigger at the 14-day window scale.
θ = 67°. Ranked sixth in 98 days of data.

The next day — February 21st — θ collapsed to 1.4°. The minimum value recorded
in the entire W=14 dataset. One day. Trigger to lock. The axis froze.

Simultaneously: the OONI global anomaly rate began rising for the first time since
January. 11.5% to 13.4% the following day. A 14-day progressive climb had begun.

The character of the signal matters. The geometric shift was relay-side only. No
spike in the volume of users connecting globally. No change in accessibility. Only
the pattern of which relays co-activated together — and how that pattern aligned
between two observers who share no infrastructure and no methodology.

A relay-side event with a one-day lag to client-side impact.

Cause: unknown.

*An independent audit raised an important caveat: the February 20 signal may be a
CCA solver artifact — the algorithm switching between two nearly equal eigenvectors
in a degenerate subspace — rather than a genuine network event. The synthetic noise
test that would resolve this has not been run. Both interpretations remain live.
This post does not resolve that question.*

---

### March 1, 2026

Iranian IRGC drone strikes destroyed two AWS data centers in the UAE — Abu Dhabi
and Dubai. A third facility in Bahrain sustained nearby damage. Two of three
availability zones in AWS ME-CENTRAL-1 were knocked out. EC2, S3, Lambda, RDS,
DynamoDB: all affected.

Tor relays hosted on AWS infrastructure in the UAE degraded or went offline.

One caveat, stated plainly: most Tor relays are hosted in Europe and North America.
Middle East AWS hosting is a small fraction of global relay capacity. The physical
destruction likely contributed to what followed — but probably was not its dominant
cause.

Nine days after February 20th.

---

### March 6, 2026

The strongest signal in 98 days.

θ = 80.4°. Ranked first. Heuristic score: 0.255.

On the same day — independently, with no shared infrastructure, no shared
methodology, no coordination of any kind — OONI recorded an 18.2% global anomaly
rate across its collection. The highest reading in 270,285 records spanning
181 countries.

Two independent measurement systems. Same day.

<pre style="overflow-x:auto; margin-top:1rem; margin-bottom:1rem;">
February 20 + 14 days = March 6. Exactly.
</pre>

Three things converged on this date. The 14-day window purging the February 20
state — a mathematical consequence, not a new event. Six days of post-strike relay
disruption accumulated in the window. And the OONI ramp, climbing since
February 21st, reaching its peak.

These causes are real. They are not fully disentangled. The strongest single
observation is the simplest one: two independent systems, built differently,
measuring different things, peaked on the same day.

*One further caveat: Russia accounts for approximately 40% of all OONI anomalies
at a stable baseline of 91.3%. The global anomaly rate has not yet been recomputed
excluding Russia. The rise may be partially probe density, not access disruption.*

---

## What the Audits Said

Before writing this post, three independent methodological audits were run —
Gemini, ChatGPT, and a separate internal review. They were asked to find the
weakest points.

The March 6 OONI coincidence survived all three. Two independent systems, same day,
external to the entire pipeline — unexplainable by any internal artifact.

The December 19-20 Δρ jump survived all three. Largest in the dataset, no external
footprint, pure relay-side, genuinely unexplained.

What was reframed: the θ suppression pattern during degenerate periods may be CCA
numerical pseudo-stability over highly overlapping windows, not a physical property
of the network. The scoring function is heuristic with no established null
distribution. Relay population churn across 98 days has not been controlled for.

These findings were accepted. They are why the work continues rather than why it stops.

---

## What This Post Is Not Claiming

The framework did not predict the drone strike.

The framework did not detect the FBI breach.

February 20 has not been attributed to any cause.

The OONI rise has not been confirmed as access disruption — it may be probe density.

March 6 has not been confirmed as a statistically significant outlier — Monte Carlo
is pending.

No individuals are identified. No traffic was inspected. No content was read.

This is a geometric finding. Security implications, if any, are for others to assess.

---

## What This Post Is Claiming

Five dates. Sourced precisely.

<pre style="overflow-x:auto; margin-top:1rem; margin-bottom:1rem;">
December 19-20, 2025
Internal relay-side geometric event — largest in 98 days.
No external footprint. Cause unknown.

February 17, 2026
FBI breach of its surveillance warrant system.
Pen register and trap-and-trace data exposed.
Identities of surveillance subjects exposed.
Not publicly known until March 3-5.
([CNN](https://www.cnn.com/2026/03/05/politics/fbi-investigating-cyber-breach-critical-surveillance-network), [Politico](https://www.politico.com/news/2026/03/06/fbi-hack-white-house-nsa-cisa-00817072), AP, Reuters, WSJ)

February 20, 2026
E3 geometric trigger, relay-side only.
Axis locked to minimum the next day.
OONI began rising. Cause unknown.

March 1, 2026
Iranian drone strikes. AWS ME-CENTRAL-1.
Two availability zones destroyed.
(Tom's Hardware, CNBC)

March 6, 2026
E3 rank #1 in 98 days.
OONI 18.2% — highest in collection.
Both peaked simultaneously.
February 20 + 14 = March 6. Exactly.
</pre>

---

## The Question

The drone strike is explained. March 6 is layered but partially understood.
December 19-20 is a mystery — contained, quiet, and still waiting for an answer.

February 20 is different.

Three days after the FBI detected a breach of the system holding the identities of
its surveillance targets and their connection metadata —

The geometry of the Tor network shifted in a way that ranked sixth in 98 days of
continuous observation. Visible only at the relay level. No change in the volume
of users connecting. No change in accessibility. Only the pattern of which relays
co-activated together, and how that pattern aligned between two observers who did
not coordinate.

Nine days before a drone strike.

Fourteen days before the strongest signal in the dataset.

The framework has no opinion about why. It was watching shape, not cause.

**What changed on February 20th?**

---

## What Comes Next

The observers need to be retrained on the full 98-day range and the analysis rerun.
The synthetic noise test needs to establish whether February 20 survives retraining
or collapses into artifact. The OONI data needs to be recomputed excluding Russia.
NetBlocks country-level data needs to be correlated with December 19-20 and
February 20. Monte Carlo null distribution needs to be built.

None of this is done. The dataset ends yesterday. The question is open.

---

*This post is a sequel to [The Geometry Told Us]({{< relref "posts/geometry-told-us.md" >}}) —
which established that two independent observers agree on one dominant shared
direction in Tor relay behavior. ρ₁ = 0.826. The stiff axis. This post asks whether
that axis moves. And when it does — what was happening in the world.*

*The analysis pipeline will be published to GitHub. No causation is claimed.
No individuals are identified. No traffic was inspected.*
