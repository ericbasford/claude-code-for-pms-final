# 4.3 brief: don't let a responder go quiet and stay quiet

*Draft for Helen Achebe (Director of Product). Owner: Dispatch PM. Prototype: `prototype-4.3.html` (open in a browser; click through). Numbers are from `rook-database` (29 Jun to 6 Sep). Mechanics below are starting values to tune with Wen Li and Ravi Menon, not findings.*

## The problem, in one breath
Since 4.2 (12 Aug), some responders stop getting pings and can't get back. The 60s ping wait turned slow answers into misses (missed pings ~2% → ~18%). A miss costs the same as a refusal (-0.12 vs +0.08 for a yes), the score never recovers on its own (2019 TODO in `history.py`), and a responder who isn't pinged can't earn points. Four responders (Vesper, The Undertow, Meteor Mite, Farlight) went from ~12 pings a week to 0-2. Nobody told them why, and handlers can't see it.

Hypothesis, not proven: we have no push-delivery or answer-latency data, so we can't yet say how many misses are slow answers vs. undelivered pings. This brief is built to be safe either way.

## Who it's for: The Undertow (responder), Desmond Okafor (handler)
The Undertow covers Harborside. Pinged for nearly every Harborside callout before 4.2, ~12 pings a week, ~75% taken. Since 12 Aug: 14 pings, 3 taken, 8 missed. Last yes on 17 Aug, then four straight misses (18 Aug, 21 Aug, 27 Aug, 5 Sep). Their phone goes silent for a week at a time, and when it does ring, the ping is gone before they can answer.

Desmond sees none of this on the console. Same shape as Kip's two cards (Meteor Mite dead quiet, The Gale on fire): "two cards that might as well be two different products", with nothing on screen to say why.

## What changes for them once this exists
**The Undertow (phone):**
1. A ping stays live for 90 seconds again, so a ping that buzzes mid-stairs is still theirs when they get to it.
2. A missed ping no longer counts like a "no". A miss costs less than a refusal; a refusal is unchanged.
3. Silence stops being a life sentence. After a few days with no pings, their score eases back toward neutral, so they get ranked into reach again without having to earn it first.
4. If a ping goes to someone else, the phone says so plainly ("Taken by another responder. No mark against you.") instead of just vanishing.

**Desmond (console):**
5. A "Quiet" tag on the coverage card when a responder hasn't been pinged for N days, with the plain reason: last ping, how many were missed, and "ranked low after missed pings", so the question to ask the responder is obvious. Read-only. He cannot change routing from the console.

## What it deliberately does not do
- **Does not revert 4.2's proximity weighting.** It was a long-asked fix for wide-area responders, and the data doesn't ask us to undo it.
- **Is not "just change 60 to 90".** That helps new misses but does nothing for a responder whose score is already at 0 and who gets one ping a week. It is one of five changes, not the fix.
- **No handler override of routing**, no runtime config. Routing still ships in the release.
- **No identity of any kind.** Scores stay keyed to cover name only (Security Policy 4.1). Nothing here tries to work out who anyone is.
- **No fix for push delivery**, if that turns out to be the cause of misses. This brief makes misses cheaper, not rarer.
- **No mutual aid / shared cover** (Q4 exploration), no dark mode, no per-responder alert sounds, no Handler Phone App. All real asks, all separate.
- **Does not change the Responder Availability Record's shape.** Supply reads it.

## How we'll know it worked
Baselines (pre-4.2 → now): missed 2% → 18%; the four's pings ~12/wk → 0-2/wk; "quiet" tickets 0 → 32; "gone before I could answer" tickets 0 → 13.
- Missed rate back under ~5% within two weeks of 4.3.
- Each of the four back to at least half their pre-4.2 weekly pings (~6/wk) within three weeks.
- "Quiet" and "gone before answering" tickets trend to near zero (Nadia Hoffmann tracks).
- Guardrails (Ravi, split by responder, not just the aggregate: the aggregate recovered partly because the four dropped out of it): other 12 responders' pings/week and acceptance don't fall; out-of-area refusals don't climb.

## Starting values (to tune)
| Lever | Today | Proposed start |
|---|---|---|
| Ping wait | 60s | 90s |
| Miss penalty | -0.12 | -0.04 |
| Refusal penalty | -0.12 | -0.12 (unchanged) |
| Yes credit | +0.08 | +0.08 (unchanged) |
| Easing | none | after 3 days with no ping, +0.07/day toward 0.5 (about 10 days from 0 to neutral) |
| Console "Quiet" tag | none | 5 days with no ping |

## Risks and open questions
1. **Wen Li (do first):** are scores in-memory only? If they reset on deploy the loop is weaker than I think. Are misses delivery failures or slow answers? Where does the travel-time estimate start from?
2. **Supply:** a "quiet" responder may read as a free maintenance window. If easing brings pings back, that partly self-corrects, but check with Supply before shipping.
3. **Ravi:** split 4.2's effect by weighting vs. timeout. Still not done. The 90s decision should wait for it, or ship as a measured change.
4. **Easing could re-ping someone who really has stepped away.** Accepted: a missed ping is cheap under this design and moves on after 90s.
5. **Helen:** is Q3's "Availability Confidence" (committed for 4.2, not shipped) related? It may be the thing that should tell Supply who's really unavailable.

## Decision needed
Go-ahead to take the prototype to Desmond and Kip (and Sofia Marino for the console and phone design), and to ask Wen Li the questions above before any code.
