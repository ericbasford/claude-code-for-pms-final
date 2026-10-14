# 03 · Rewind — prompts

**Context:** You joined Rook two weeks ago as PM on Dispatch.
Release 4.2 shipped on 12 August, just before you arrived, and
landed badly.

Last session you read four conversations and every support ticket
since 4.2 — and found the two piles did not agree.

At the end of the session, type wrap up, and Claude Code saves the
prompts you wrote yourself below — not the starter prompt — updates
CLAUDE.md, and saves your work to GitHub. By Module 6 this file is a
prompt library built from your own questions.

---

### 1.

Can you do the same analysis, but break the data down into two cohorts. One cohort is the set of four responders who account for much of the collapse. The other cohort is everyone else. I'd like to see the before & after 4.2 data for these groups.

### 2.

This is begging for a graph. Can you graph the cohorts on a time series showing lines for both taken % and missed %?

### 3.

Okay, I understand the people related to the issue. My hypothesis remains that the folks who are missing the pings are not bad at their jobs, but that there might be something wrong with the technology or their expectations. Can you tell me more about the specific pings that were missed. What qualities do they share compared to pings that were picked up?

### 4.

What are all the tables and data sources I have to work with? I want to do some cross-comparisons to see if the subjective/qualitative feedback matches the objective facts.

### 5.

Yes, please read the ticket bodies and the four interviews next and tabulate each claim against the matching data.

### 6.

Compare the support_tickets table on rook-database against the pings table. Do they agree with each other? When did people start writing in, and when do the numbers actually move?

### 7.

The previous PM said this was just August being quiet. Is there anything in this file that would tell me whether that was right?

### 8.

Who were the "quiet" responders? I want to pick one and then will ask you to deep dive on them. Who had the biggest drop from before 4.2 to after?

### 9.

Let's go with The Undertow. Show me every week for them, how many times we pinged them, how many they took. Then tell me what happened to that person, week by week, in plain English.

### 10.

If I'd only asked for the number, not the rows, what would I have missed?
