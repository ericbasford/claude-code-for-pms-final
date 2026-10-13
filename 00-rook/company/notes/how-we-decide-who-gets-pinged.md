# How we decide who gets pinged

*Draft v1, written from the code in `00-rook/code/dispatch-routing/` as of release 4.2 (12 Aug 2026). Priya asked for this doc and none existed. Everything under "What the code does" is read directly from the code. Anything under "Not yet confirmed" needs Wen Li (who built it) before this goes beyond the Dispatch team.*

## In one paragraph

When a callout comes in, Dispatch takes everyone marked available in that region, scores each one, sorts best-first, and pings them **one at a time**. Each ping stays live for a fixed window (60 seconds since 4.2). A yes ends it. A no, or no answer before the window closes, moves it to the next person. Nobody is ever removed from the list. The score only decides the *order*, so someone ranked low is still asked, but only after everyone above them has said no or gone quiet.

## What the code does

### 1. Who is on the list
Everyone marked available in the callout's region at that moment (`availability.available_for`). Availability is set by the responder or their handler in the app. Routing never changes it.

### 2. The score
Three parts, each multiplied by a weight (`config.py`):

| Input | What it measures | Weight now | Weight before 4.2 |
|---|---|---|---|
| Proximity | Travel-time estimate (not straight-line since 4.1). 1.0 at zero minutes, falling in a straight line to 0.0 at 45 minutes or more | **0.60** | 0.45 |
| Recent acceptance | A running score, 0 to 1 (below) | **0.25** | 0.40 |
| Capability match | Share of the callout's required tags the responder has. 1.0 if the callout needs none | 0.15 | 0.15 |

Rough scale: one minute of travel is worth about 0.013 points. The full range of the acceptance score (0 to 1) is worth 0.25 points, or about **19 minutes of travel**. Before 4.2 it was 0.40 points, or about **40 minutes**. So 4.2 made a responder's history matter about half as much compared with how close they are.

### 3. The recent-acceptance score (`history.py`)
- Everyone starts at **0.5**.
- They take a ping: **+0.08**.
- They turn it down **or** the window runs out: **-0.12**. The code treats the two identically on purpose ("we asked and we didn't get a yes").
- It's clamped between 0 and 1.
- **It never decays.** There's a 2019 TODO asking whether it should ease back toward 0.5 over time; it was left as-is.
- How fast they answer doesn't matter. Only whether they answered inside the window.

Break-even is a **60% yes rate** (0.6 × 0.08 = 0.4 × 0.12). Above that the score drifts up, below it the score drifts down.

### 4. The offer (`offer.py`)
The offer is pushed to the responder's phone, then polled until they answer or the window (`OFFER_TIMEOUT_SECONDS`, 60) closes. If it closes, the offer is withdrawn from their phone ("it's gone to somebody else") and recorded as *missed*. Routing config ships in the release; handlers can't change it at runtime.

## Why this matters for 4.2 (hypothesis, not finding)

Put the pieces together and there is a possible loop:

1. Shorter window means more misses.
2. A miss costs the same as a refusal, so the score drops faster than it rises for anyone under 60%.
3. A lower score ranks you lower, so you're only pinged after others pass.
4. Fewer pings means fewer chances to earn +0.08, and the score never decays on its own.

That pattern fits the four responders in the data (Vesper, The Undertow, Meteor Mite, Farlight), whose pings fell from about 12 a week to about 1 and whose misses rose sharply. It doesn't prove the mechanism. The same data fits other causes (below).

The 2019 comment argues *against* decay because "if somebody has stopped taking work, we probably want that to stick until they take work again". But a low-ranked responder is rarely asked, so they rarely get the chance to take work again.

## Not yet confirmed (for Wen Li)

1. **Does the proximity change apply equally to responders who've been missing or declining?** Asked by the engineering manager on 14 Aug, never answered on the page. This code applies it equally. Is there anything outside this folder that doesn't?
2. **Are the misses real non-answers or push-delivery failures?** The code can't tell the difference. `push_to_device` and `poll_device` are stubs here, and the changelog notes a 4.1 push-reliability fix and an Aug ticket about older phones.
3. **Where do the scores live?** `history.py` keeps them in an in-memory dict. Do they persist across deploys and restarts, or reset to 0.5? That changes the picture a lot.
4. **Is the 60s window a deliberate choice?** The Q3 roadmap says "ping timeout tuning"; the release cut 90s to 60s. Confirm that's what was meant.
5. **Supply dependency.** `availability.current_record` is read by Supply for maintenance scheduling and its shape is a contract. If routing changes how a responder looks (for example, "rarely called out"), does Supply read that as a quiet window and book maintenance into it? Unverified; handler tickets include "Maintenance booked on a bad day".
6. **Does `available_for` ever filter by anything other than the availability flag?** The docstrings say no.

## What this doc doesn't say
- Nothing here identifies any responder. Per Security Policy 4.1, scores are keyed to the responder's cover name only, and nothing here should be extended to map to legal identity.
- No recommendation on changes. That waits on data from Ravi (split by weighting change vs. timeout change) and Wen Li's answers above.
