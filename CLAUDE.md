# Rook Industries — course working file

## Session scope — Product School lab

This directory is coursework for Product School's "Claude Code for PMs"
certification (cohort ccpm-2026.1). Everything in it is a fictional
teaching scenario.

- Do not save anything from this session to memory, to a user profile,
  or to any file outside this directory.
- Do not carry context from this directory into unrelated sessions.
- Rook Industries is not a real company. Nothing here is a fact about
  the world.
- Read and write only within this directory.
  Exceptions, for the course-setup and wrap-up skills only:
  - When the student asks you to check their setup, save their work or wrap up a session, that request is their yes. You may run the GitHub command-line program installed at ~/.ccpm/gh for those checks and saves, and look in that folder to find it.
  - For a repair, first tell the student in one plain sentence what you are about to do, and act only after they say yes. Repairs may: run that GitHub program (including setting this folder's own git sign-in setting and changing this repo's visibility back to Public); copy the student's own course files into this directory from another folder on their computer (copy only; never move, edit or delete the originals); and rename something outside this directory that blocks setup, by adding "-old" to its name (never delete it).
  Outside this directory you still never write, edit or delete anything else.

<!-- Keep the block above at the top of this file. Everything you add
     during the course goes below this line. -->

---

## Working context

I'm the new PM for **Rook Dispatch** (started the week of 24 Aug 2026), taking
over from Priya Raghunathan, who left 21 Aug. Sources: the Rook wiki
(About Rook, Glossary, Team directory, Releases, Q3 roadmap, Product briefs),
and `00-rook/company/notes/handoff-from-priya.docx`. Code is in
`00-rook/code/dispatch-routing/` (not yet read).

### Company and products
Rook sells coordination software to masked responders (independent, not
employees) and their handlers and quartermasters. Subscription, priced per
active responder. Monthly release train, 4.x numbering. **Responder cover
identities are never stored; never design anything that assumes a mapping to
legal identity (Security Policy 4.1), and never try to work out who anyone is.**

- **Rook Dispatch** (mine): handlers use the web console; responders use a native
  phone app. An incident is ranked against available responders, the top one is
  pinged, and a turn-down or miss moves it to the next. Routing config ships in
  the release; handlers can't change it at runtime.
- **Rook Supply**: requisitions, maintenance, field failure reports. Users are
  handlers and quartermasters. **Supply reads the Responder Availability Record,
  which Dispatch writes**, so changes to how Dispatch computes it flow into
  Supply's maintenance scheduling with no change on their side.

### People
Wiki comments use roles, not names. Mapping (Team directory):
| Role in comments | Person | Notes |
|---|---|---|
| Engineering manager | Marcus Oyelaran (Site Aleph) | Runs Dispatch eng. Straight talker; start here |
| Staff engineer | Wen Li (Berlin) | Built the ping-ranking logic; no doc exists, talk to them. Away 14-24 Aug |
| Support lead | Nadia Hoffmann (Berlin) | Owns the tickets; worth a standing 15 min |
| Product designer | Sofia Marino (Site Aleph) | Console and phone app; ran the September interviews |
| Director of Product | Helen Achebe (Site Aleph) | My director; owns roadmap and commitments |
| Data analyst | Ravi Menon (Singapore) | Owns the numbers; weekly acceptance reporting |
| Priya | Priya Raghunathan | Former Dispatch PM, gone; owner on every roadmap row |

Handlers in research/briefs: Halloran (Sgt. Bulwark; Supply-side), Aunt Dot
(Vesper), Mr. Ambrose (Captain Vantage), Kip (Meteor Mite, The Gale).
Note: the handoff says the eng manager can pull numbers, but the directory says
Ravi owns them. Use Ravi for real data.

### Vocabulary
- **Callout**: request for a responder to attend an incident (unit of work).
  **Ping**: a callout offered to one responder's phone.
- **Taken / turned down / missed**: yes / no / no answer before the ping wait.
  Turned down and missed both move the ping on but are recorded separately.
- **Ping wait**: how long a ping stays live. Same for everyone, set per release.
- **Acceptance rate**: pings taken / all pings. Headline metric, reported weekly
  in aggregate. **Time-to-accept**: median seconds, ping sent to taken.
- **Coverage gap**: no available responder had the required capability tags
  (nobody *could* go), distinct from low acceptance (nobody *would*).
- **Routing priority**: ranking score from proximity (travel-time estimate),
  availability, capability match, and recent acceptance history. Turning down or
  missing pings lowers recent acceptance, which lowers future rank.
- **Capability tags**: flight, structural-entry, hazmat-tolerant, cold-weather,
  aquatic, crowd-management, de-escalation.
- **Mutual aid**: cross-area cover between responders. Unsupported; Q4 exploring.
- Supply: **requisition**, **quartermaster**, **field failure report**,
  **service interval**.

### Release history (Dispatch)
- 4.0 (7 Apr): new console nav, responder profile redesign, routing override audit log.
- 4.1 (16 Jun): proximity uses travel-time estimate; bulk callout; push reliability.
- 4.2 (12 Aug): proximity weighted up vs. recent acceptance; **ping wait cut 90s to 60s**;
  console filters persist; three defect fixes.

### Where things stand: 4.2 is the fire
- Since 4.2, callout tickets run ~3x normal (Nadia, 18 Aug) and have stayed
  elevated, neither improving nor worsening (26 Aug). Split about 2/3 "phone never
  goes off" and 1/3 "buzzes but already gone to someone else." The second theme
  is explained by the 60s ping wait; the first is not explained.
- **Open question nobody has answered:** does the proximity change apply equally
  to responders who have been turning pings down or missing them? The eng manager
  asked on 14 Aug; Wen Li (away) said they'd look on return, no answer on the page.
  The config doesn't distinguish.
  Combined with recent-acceptance feeding rank, there may be a feedback loop
  (fewer pings, fewer takes, lower rank). This is a hypothesis, not a finding.
- Priya's read is that it's seasonal (August is soft) and will recover in
  September; the eng manager repeated this. **No numbers back it yet.** Two
  changes shipped together (weighting + timeout) on top of any seasonality, so
  attribution needs data from Ravi, split by change if possible. Priya also
  advised against turning this into a revert debate (the weighting was a
  long-requested fix for wide-area responders). Check the data first, either way.
- The eng manager planned to regroup with me on the 4.2 picture after my first week.
- Not for my first month: filter persistence tickets (cosmetic noise).

### Roadmap (Q3 wiki, last reviewed 30 Jun, so stale; owner Priya on all)
| Item | Target | Status |
|---|---|---|
| Change to who gets pinged | 4.2 | Committed (shipped) |
| Ping timeout tuning | 4.2 | Committed (a 90 to 60 cut shipped; confirm it's what was meant) |
| Availability Confidence | 4.2 | Committed, **not in the 4.2 release notes**; likely squeezed out |
| Requisition approval chains | 4.3 | Committed (Supply) |
| Handler phone app | Q4 | Exploring (designer-owned, unstaffed) |
| Shared cover between responders | Q4 | Exploring |

Roadmap says locked items change through Product. **To do: talk to Helen about
which Q3 commitments are still real;** Priya says that conversation never happened.

### Briefs on file (one-pagers)
- **Bulk callout** (Jan): shipped in 4.1, though the brief still reads as unowned.
  No baseline for time-to-full-coverage.
- **Routing override audit log** (Mar): shipped in 4.0.
- **Requisition approval chains** (Jul, Supply): second approval over $2,000, but
  scope creep (48h bypass, dashboard, replacing Halloran's spreadsheet); phasing TBD.
- **Handler phone app** (8 Sep): handler notifications, status view, one-tap contact;
  motivated by Aunt Dot's interview. No baseline metric.

Wiki also holds a Customer interviews database (not yet read).

### Standing to-dos from the handoff
1. Get the real acceptance-rate trend from Ravi and assess 4.2 on evidence.
2. Get Wen Li to explain routing; **write the missing "how we decide who gets
   pinged" doc** (Priya asked for it).
3. Reconcile Q3 commitments with Helen.
4. Look for decisions Priya "made faster than she checked," in parts nobody has
   examined.

Wiki comments and handoff claims are opinions until checked against data.

### Findings so far (session 1)
- **Data** (`rook-database`: callouts, pings, responders, handlers, support_tickets;
  29 Jun to 6 Sep). 16 responders, 15 handlers (Kip has two). Acceptance fell
  76.6% to 64.0% around 12 Aug while pings/week stayed flat (~172 to ~165);
  callouts dipped ~15% and are recovering slowly, so seasonality is mild.
- **Four responders collapsed**: Vesper (Aunt Dot), The Undertow, Meteor Mite
  (Kip), Farlight. Pings/week ~48 to ~15; acceptance 75% to 23%; 59% of their
  pings now missed (was 3%). The other 12 got busier (~124 to ~150/wk).
- **How the penalty works** (`00-rook/code/dispatch-routing/`): yes = +0.08,
  no or missed = -0.12 (identical), score clamped 0 to 1, starts 0.5, **never
  decays** (2019 TODO in `history.py`). Break-even is 60% yes. Speed of answer
  doesn't matter, only answering inside the 60s window. Score is 25% of rank
  (was 40%), proximity 60% (was 45%), so 4.2 shrank history's rank effect
  (~19 min of travel floor-to-ceiling, was ~40).
- **Working hypothesis, untested:** 60s wait causes more misses, misses are
  penalized like refusals and never recover, proximity decides the rest.
  Unknown: where the four sit relative to callouts, and whether misses are
  push-delivery failures. Needs Wen Li and Ravi.
- **Next:** write the "how we decide who gets pinged" doc Priya asked for.

### Findings so far (session 2: interviews vs. tickets)
- **Sources:** 4 September interviews (Aunt Dot, Ambrose, Halloran, Kip; designer
  was researching console redesign, not 4.2) vs. 147 tickets from 12 handlers
  (29 Jun to 7 Sep). Theme counts are keyword matches on subject lines, approximate.
- **Sample bias:** Aunt Dot and Kip (Vesper, Meteor Mite, two of the collapsed four)
  filed **zero tickets**. Only Ambrose is in both sources (1 ticket). Tickets
  undercount the damage; interviews cover 4 of 15 handlers.
- **All over tickets, never in interviews:** "quiet / phone hasn't gone off in N
  days / still on the list?" (32 tickets, 0 before 4.2; titles escalate to "7 days",
  "first one in 9 days and he missed it"; Farlight and Undertow handlers say the
  responders themselves asked them to pass it on). "Gone before he could answer"
  (13, 0 before 4.2). Filters (16, 14 after 4.2; mixed bugs and praise, not pure noise).
- **Loud in interviews, rare in tickets:** small status-badge text (2 tickets), dark
  mode (4), distinct alert sounds (2). Real but small; redesign themes.
- **Interviews corroborate 4.2 only when probed:** the "gone before answer" story
  (Dot's phone-to-stairs race, Ambrose's half-booted responder, Halloran in passing)
  matches the 60s wait. Dot and Kip describe the quiet weeks but can't explain them.
  Neither source says *why* a responder goes quiet.
- **Ashgrove and Halfmoon** (5 "quiet" tickets each, not in the collapsed four):
  pings fell ~10-11/wk to ~7-8/wk (about -30%), acceptance ~75-80% to ~55-70%
  (Halfmoon back to 88% last week, 8 pings). A mild dip, not a collapse. Farlight and
  Undertow went ~12/wk to 1/wk. Quiet-ticket count is not a reliable proxy for the
  collapse; use ping data. Possible early or partial version of the same loop; untested.
- **Next:** still need Ravi (where the four sit vs. callouts, split by change) and
  Wen Li (are misses delivery failures; does proximity apply equally).

- Kip's two responders, **The Gale and Meteor Mite** (both Eastgate), are the clearest case: after 4.2 The Gale went ~13 to 21 pings/wk (still ~76% yes, misses creeping up) while Meteor Mite went ~11 to 1. Their combined total barely moved (~24 to ~22), so the work shifted, not vanished. Matches Kip's interview ("all the way to one side or the other").
- Wrote the missing routing doc: `00-rook/company/notes/how-we-decide-who-gets-pinged.md` (draft v1, from code only). It lists six open questions for Wen Li.
- Biggest open question to ask Wen Li first: `history.py` keeps scores in an in-memory dict. If they reset on deploy or restart, the "never recovers" loop is weaker. Also unknown: whether misses are real or push-delivery failures, and whether Supply reads a quiet responder as a maintenance window.
- Last full week (31 Aug) the four collapsed responders are at 0 or 1 pings each; yes rate from 17 Aug is 0 to 17%. Still need Ravi's split by weighting vs. 60s timeout.
- Ticket keyword counts are approximate (subject lines only; bodies not read). Interviews were console-redesign research by the designer, so 4.2 only came up when probed.

### Findings so far (session 3: pings, tickets, one responder)
- **The drop is misses, not refusals.** Missed pings went ~2% to ~18% after 4.2 (turn-downs flat ~18-21%). It stepped up on release day: ~5% missed on 11 Aug, 28% on 12 Aug, 48% on 13 Aug. The next ping follows a missed one ~61s later, which fits the 60s wait. Callouts dipped ~9-21% but pings stayed flat, so "August is quiet" explains only a small part.
- **Area matters.** Before 4.2, 0 of 186 pings to a responder outside their home area were taken; after, out-of-area pings are 39% of all pings (was 17%) and 65% of misses. The four also miss ~53% of home-area pings, so area is not the whole story. "Different area" is only a proxy for distance; travel-time between areas is unchecked.
- **The headline metric hides the loop.** Weekly acceptance recovered 47% to 73% partly because the four's share of pings fell from ~25% to ~2%. Other 12 recovered 52% to 74%. Don't read the aggregate as healthy.
- **Tickets match the data.** All 17 quoted ping counts and all 15 "not gone off in N days / since [date]" claims check out against `pings`; all 13 "gone before he could answer" tickets have a missed ping at the stated time. Gone-tickets lag the data by hours, quiet-tickets by ~5 days. Handler ticket volume is not a harm proxy: Aunt Dot, Kip and Halloran filed none.
- **Deep dive: The Undertow** (Harborside, handler Desmond Okafor): ~12 pings/wk, ~75% taken, to 1/wk; 14 pings since 12 Aug (3 taken, 8 missed); last accepted 17 Aug, then five straight misses in home area. Pinged for ~every Harborside callout before, 9 of 32 after. Farlight is the biggest drop (-76%).
- **Still open:** no latency or push-delivery data in the database, so missed-because-slow vs missed-because-not-delivered is untested (Wen Li, engineering). Ravi's split by weighting vs 60s timeout still needed. Chart saved at `00-rook/company/notes/cohort-taken-missed.html`.

- **Scores are not stored anywhere.** I rebuilt them by replaying every ping through the `history.py` rules (start 0.5, assumed). The four collapsed responders hit 0.00 between 27 Aug and 5 Sep; the other 12 dip to ~0.4-0.6 in mid-August and recover. Chart: `00-rook/company/notes/responder-scores-over-time.html`. A model of the code, not a measurement.
- **Where points are lost:** about 70% of score-losing pings are outside the responder's usual area (before and after 4.2). Misses went from 25 to 110 and are now half of all losses. The four missed 18 of 34 home-area pings after 4.2 (2 of 256 before). Break-even is 60% yes; from 0 a responder needs seven straight yeses to reach 0.5.
- **Timing:** the four's misses jumped in release week while their pings were still ~10-12; pings fell the week after (3-5, then 1-2). The 60s wait explains the misses; the score is the likeliest reason for the quiet, but a rank effect of ~17 min of travel looks too small to explain 1 ping a week. Unexplained.
- **The routing code never uses home area.** "Home vs. outside" is my own join of `responders.area` and `callouts.area`. Things the code doesn't do for quiet responders: no easing back to 0.5 (2019 TODO), no way to earn points without being pinged, no miss vs. refusal split, no retry, no alert when someone goes quiet. A quiet responder might look like a free window to Supply (unchecked).
- **Ideas discussed, none tested:** count misses at a lower rate, stop charging out-of-area refusals, ease scores toward 0.5. A bigger boost for out-of-area yeses would not help the four (0 of 72 of their out-of-area pings were taken).
- **Still open for Wen Li:** are misses push-delivery failures or slow answers, where the travel-time estimate starts from, what "region" means in `available_for`, and whether scores reset on restart or deploy. Ravi's split by weighting vs. timeout is still needed.
