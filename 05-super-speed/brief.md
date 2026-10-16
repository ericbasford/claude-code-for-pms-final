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
5. When pings go quiet, the app tells them why in plain words and lets them tap "Am I connected?" It checks that the phone is online, reaching Rook and marked available, and **that notifications are on**, with a one-tap path to turn them on. (Notifications being off would look exactly like "my phone never goes off". Untested as a cause.)
6. Each ping shows the capability it needs as an icon plus a label (for example a door for Structural-entry), so they can decide quickly. Prototype shows one tag; the other six need icons.

**Desmond (console):**
7. A "Quiet" tag on the coverage card when a responder hasn't been pinged for N days, with the plain reason: last ping, how many were missed, and "ranked low after missed pings", so the question to ask the responder is obvious. Read-only. He cannot change routing from the console.

## What it deliberately does not do
- **Does not revert 4.2's proximity weighting.** It was a long-asked fix for wide-area responders, and the data doesn't ask us to undo it.
- **Is not "just change 60 to 90".** That helps new misses but does nothing for a responder whose score is already at 0 and who gets one ping a week. It is one of several changes, not the fix.
- **No handler override of routing**, no runtime config. Routing still ships in the release.
- **No identity of any kind.** Scores stay keyed to cover name only (Security Policy 4.1). Nothing here tries to work out who anyone is.
- **No fix for push delivery**, if that turns out to be the cause of misses. This brief makes misses cheaper, not rarer.
- **No mutual aid / shared cover** (Q4 exploration), no dark mode, no per-responder alert sounds, no Handler Phone App. All real asks, all separate.
- **Does not change the Responder Availability Record's shape.** Supply reads it.

## How we'll tell people (not a silent change)
The wait going from 60s back to 90s, and misses costing less, are changes people will feel, so they get announced. Tone: plain and honest. We shortened the wait in 4.2, it was too short, we're fixing it. No score jargon, and no promise that anyone's rank returns on a date. Wording needs Helen's sign-off.

| Who | What they get | When |
|---|---|---|
| Handlers (e.g. Desmond, Kip) | Email and console banner: what changed, why, and what the new "Quiet" tag means. Read-only; they still can't change routing | One week before release, banner again on release day |
| Responders (e.g. The Undertow) | One-time "What's new" card on first open: 90s wait, misses count for less, "Am I connected?" check | First app open after 4.3 |
| Quiet responders | Same card, plus the connection and notifications check, pushed to anyone with no pings for 5+ days (they may not open the app) | Release day |
| Support (Nadia Hoffmann) | Reply macro for "phone never goes off" and "gone before I could answer", plus a heads-up so she can tag tickets by theme | Before release |
| Internal (Helen, Marcus Oyelaran, Ravi Menon, Wen Li) | Release note stating the 4.2 reasoning, the 4.3 values, and what we'll measure | Before release |
| Supply | Heads-up that quiet responders may start getting pings again | Before release, once checked |

Follow-up: two weeks after release, a short note to handlers on whether quiet responders came back (Ravi's numbers). 4.3's release notes must list the ping wait change this time; 4.2's did, but the "Availability Confidence" commitment was silently dropped.

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

## Later: avenues to explore (not in 4.3)
**Competitive pings**: offer a callout to the top 2-3 ranked responders at once, with each seeing they're competing. Parked on purpose: shipping it in 4.3 would hide whether the 4.3 fixes worked.
- *Why it's tempting:* about a third of callouts since 4.2 (143 of 440) needed a second or later ping, up from 22% before, and unfilled callouts doubled (11% vs 5.5%). Waiting through each window is part of that.
- *Why it's risky:* it shuts out low-ranked responders like The Undertow and piles more work on the busy ones (The Gale). Losing a race would feel like "gone before I could answer", already a top ticket theme. It rewards whoever has a thumb on the phone, not the best fit, and it breaks acceptance rate as a metric. Responders haven't been asked.
- *If we explore it:* Q4, next to shared cover. Smallest test: offer 2 responders at once, only on callouts that need a second ping, first yes wins, and losing a race carries no score penalty. Interview responders before designing any "who you're competing against" screen.
- *Variant to try first:* claim with an ETA. The system picks the best fit within a short window, which fits the proximity weighting better than a tap race.

**Other ideas, all untested:**
- **One-tap "why" after a miss.** "Missed that one? Tap: driving / no signal / too far / not available." Cheap, and it gives us the thing we're missing: why pings are missed (slow answers vs. delivery failures vs. responders saying no by silence). Best first experiment, because it informs everything else.
- **Wait extends while you're looking.** The clock pauses or stretches once the responder opens the ping. Answers the "phone-to-stairs" race without a longer wait for everyone, and doesn't slow callouts when nobody's reading.
- **"You're next" heads-up.** A soft nudge to the next responder in line ("a callout may come your way in a minute") so the phone is already in hand. Helps the "gone before I could answer" theme. Risk: it's noise if the first responder usually says yes.
- **Comeback ping.** For a responder who's been quiet, offer low-urgency callouts first, with no penalty if missed, so they can earn points without being pinged for urgent ones. Fixes "no way to earn points without being pinged" more directly than easing. Risk: needs a way to tell urgent from low-urgency, and nothing in the data does that today.
- **A "rest" setting for the busy.** The other side of quiet: The Gale is "exhausted" while Meteor Mite sits idle. Let a responder or handler flag "ease off", or cap pings per day, so work spreads. Risk: fewer pings for people who want them.
- **Quiet-time hours.** Responders set when they want pings so night misses stop counting against them. Untested whether misses cluster by time of day; Ravi could check.
- **Shared cover for quiet responders** (existing Q4 exploration item). Mutual aid could route a quiet responder's area to a neighbour until they're back, but it also moves work away from them.
- **Optional post-incident "share your story" (from a "selfie with the person helped" idea).** Raised for social media and press. *Not* on the "It's yours." screen: that moment is before the incident and should stay clear. If pursued, it's a separate screen after resolution. Needs, before any design: victim consent (asked directly, can decline or withdraw, no minors or people in distress), responder choice (hands only, silhouette or no photo, per-campaign opt-in), no effect on routing, location and device data stripped, images keyed to cover name only (Security Policy 4.1), and sign-off from Security and Legal (publicity rights; Berlin means GDPR). A quote with no photo is the safer start. Marketing should own the campaign. Keep it out of 4.3 so it doesn't muddy the quiet-responder measurement.

## Risks and open questions
1. **Wen Li (do first):** are scores in-memory only? If they reset on deploy the loop is weaker than I think. Are misses delivery failures or slow answers? Where does the travel-time estimate start from?
2. **Supply:** a "quiet" responder may read as a free maintenance window. If easing brings pings back, that partly self-corrects, but check with Supply before shipping.
3. **Ravi:** split 4.2's effect by weighting vs. timeout. Still not done. The 90s decision should wait for it, or ship as a measured change.
4. **Easing could re-ping someone who really has stepped away.** Accepted: a missed ping is cheap under this design and moves on after 90s.
5. **Helen:** is Q3's "Availability Confidence" (committed for 4.2, not shipped) related? It may be the thing that should tell Supply who's really unavailable.

## Decision needed
Go-ahead to take the prototype to Desmond and Kip (and Sofia Marino for the console and phone design), and to ask Wen Li the questions above before any code.
