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

A pen register does not record the content of a communication. No audio. No
transcripts. No messages. It records outgoing metadata — every IP address contacted,
the exact timestamp, how long the connection lasted. A trap-and-trace is the mirror
image: incoming metadata. Who reached out to you, when, for how long.

That distinction matters. Metadata reveals the pattern of life. For whoever breached
this system, the prize was not a wiretap recording. It was the map of who the FBI
was watching — and everyone those people had been talking to.

The attacker entered through a commercial ISP vendor's infrastructure. A supply
chain compromise. The front door was locked. They used a trusted third party as
a backdoor.

This entry vector has a name and a history.

In 2024, a Chinese state-linked threat group known as Salt Typhoon compromised the
lawful intercept systems of at least nine major US telecommunications providers —
AT&T, Verizon, Lumen, and others. They entered the same way: through ISP
infrastructure, through the supply chain, through trusted vendor access. Once
inside, they accessed the metadata of over a million users. More importantly, they
accessed federal target selection lists — the active roster of who law enforcement
was watching, and the full operational network of everyone those targets had been
in contact with.

Salt Typhoon did not just steal data. They learned the structure of FBI surveillance.
Who was being watched. Who was connected to whom. The entire graph.

The FBI has since stated that Salt Typhoon holds exfiltrated data in perpetuity —
for future theft, future exploitation, future leverage. Whether the February 17
breach is connected to Salt Typhoon has not been confirmed. The entry vector is
identical. The target is identical. The value of the prize is identical.

When that map moves — to peer intelligence services, to the subjects of surveillance
themselves, to operational networks that have now learned they are burned — the
people on it who use Tor do not stop using Tor. They change how they use it. New
entry guards. New circuits. Different timing patterns. Operational security responses
at scale are not visible in traffic volume. They are visible in the pattern of which
relays get selected together — and how that pattern shifts between two independent
observers watching the same network from different mathematical vantage points.

Three days later, <u>on February 20th, the geometry of the Tor network changed.</u>

This post is about what that means — and why the honest answer is: I don't know yet.

*W=14 days — Feb 20 trigger (θ=67°), Mar 6 rank #1 (θ=80.4°):*
![E3 Signal W=14](/plots/e3_w14_signal.png)

The [previous post]({{< relref "posts/geometry-told-us.md" >}}) documented that the
pipeline had been operating in a degenerate state since February 9th — 13 consecutive
days where the CCA alignment collapsed and the two observers lost their shared
direction entirely. February 20th sits inside that window. What follows happened
while the system was near-blind.

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
What follows is a ranked list of candidates. A synthetic noise test has since been
run — the March 6 score of 0.255 sits above the noise maximum at W=14. The scoring
metric never fires on pure random input at that window scale.

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

This is the same entry vector Salt Typhoon used in 2024 to compromise nine US
telecom providers and access federal lawful intercept systems. In that operation,
Salt Typhoon accessed active federal target selection lists — the operational map
of who was under surveillance and their full contact network. Attribution of the
February 17 breach has not been confirmed. The pattern is documented.

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

A relay-side event. No corresponding client-side spike.

This is consistent with operational security responses at scale. Users who learn
they are under surveillance — or whose contacts learn the same — do not stop using
Tor. They change how they use it. New entry guards, new circuits, changed timing.
That behavioral shift does not change the number of users connecting. It changes
which relays they connect through, and how those relay selections correlate across
the network. That is exactly what the geometry measures.

Cause: unknown.

*An independent audit raised an important caveat: the February 20 signal may be a
CCA solver artifact — the algorithm switching between two nearly equal eigenvectors
in a degenerate subspace — rather than a genuine network event. A synthetic noise
test was run to address this. The θ suppression pattern — 9 of 11 W=14 windows
below 20° in the Feb 20 → Mar 6 period — does not appear in noise. The maximum
consecutive run below 20° in synthetic data was 2 windows. The suppression is not
a noise artifact. Whether it reflects a physical property of the network or a
numerical property of the frozen observers remains an open question.*

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

θ = 80.4°. Ranked first. Heuristic score: 0.255 — above the noise maximum at W=14.
The scoring metric does not fire on pure random input at that window scale.

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

*One important caveat on the OONI figure: recomputed excluding Russia, the March 6
anomaly rate falls to 7.48% — Russia accounts for approximately 59% of the global
peak. The February 20 to March 6 ramp survives exclusion, rising 1.78 points across
non-Russian probes over the same period, but the magnitude of the 18.21% global
figure is heavily Russia-driven. The underlying non-Russian signal is real and
directionally consistent. The global headline number should be read with that context.*

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

The Salt Typhoon connection to the February 17 breach has not been confirmed.

The OONI rise has not been confirmed as access disruption — it may be probe density.

March 6 has not been confirmed as a statistically significant outlier by external
review — but the score of 0.255 sits above the noise maximum at W=14 in synthetic
testing. The scoring metric never fires on pure random input at that window scale.

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
Same entry vector as Salt Typhoon 2024.
Attribution unconfirmed.
(AP, CNN, Politico, Reuters, WSJ)

February 20, 2026
E3 geometric trigger, relay-side only.
Axis locked to minimum the next day.
OONI began rising. Cause unknown.

March 1, 2026
Iranian drone strikes. AWS ME-CENTRAL-1.
Two availability zones destroyed.
(Tom's Hardware, CNBC)

March 6, 2026
E3 rank #1 in 98 days. Score above noise maximum.
OONI 18.2% globally — highest in collection.
Ex-Russia: 7.48%, ramp +1.78 points confirmed.
Both peaked simultaneously.
February 20 + 14 = March 6. Exactly.
</pre>

---

## The Question

The drone strike is explained. March 6 is layered but partially understood.
December 19-20 is a mystery — contained, quiet, and still waiting for an answer.

February 20 is different.

Three days after the FBI detected a breach using the same entry vector a Chinese
state actor used in 2024 to map the operational network of every active FBI
surveillance target —

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
The synthetic noise test has been run — March 6 scores above the noise maximum at
W=14; the θ suppression pattern does not appear in synthetic data. The OONI data
has been recomputed excluding Russia — the non-Russian anomaly rate rose +1.78 points
Feb 20 → Mar 6, confirming the ramp is real; Russia accounts for ~59% of the March 6
global peak. NetBlocks country-level data needs to be correlated with December 19-20
and February 20. Monte Carlo null distribution needs to be built.

None of this is done. The dataset ends yesterday. The question is open.

---

*This post is a sequel to [The Geometry Told Us]({{< relref "posts/geometry-told-us.md" >}}) —
which established that two independent observers agree on one dominant shared
direction in Tor relay behavior. ρ₁ = 0.826. The stiff axis. This post asks whether
that axis moves. And when it does — what was happening in the world.*

*The analysis pipeline will be published to GitHub. No causation is claimed.
No individuals are identified. No traffic was inspected.*
