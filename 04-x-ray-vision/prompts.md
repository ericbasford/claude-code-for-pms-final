# 04 · X-Ray Vision — prompts

**Context:** You joined Rook two weeks ago as PM on Dispatch.
Release 4.2 shipped on 12 August, just before you arrived, and
landed badly. Last session the numbers showed you what happened: a
ping used to wait ninety seconds and now it waits sixty, people
missed pings they used to catch, and missing one counts the same as
turning one down — so four responders stopped hearing from us
altogether.

At the end of the session, type wrap up, and Claude Code saves the
prompts you wrote yourself below — not the starter prompt — updates
CLAUDE.md, and saves your work to GitHub. By Module 6 this file is a
prompt library built from your own questions.

---

### 1.

Okay, it sounds like score is important. Can you show me a graph of every respondents score over time?

### 2.

How does home area vs. outside home area play out in the code?

### 3.

The data we were looking at earlier had lots of understanding of Home area vs. Outside Home. How is this not in the code, if it's being recorded in the data?

### 4.

Can you create a diagram of the different systems talking to each other in the flow of executing this software suite?

### 5.

That's great! How about a sequence diagram? Can you show me what it looks like when the first responder who gets pinged misses the call.

### 6.

What are the biggest contributors to responders losing score?

### 7.

It sounds like we should periodically reset scores. Are there any comments in the code about this?

### 8.

The pings table on rook-database has last session's numbers. Read that alongside this code. Does it back up what I found in Lab A? And does that answer the engineering manager's question above?

### 9.

When you say "that points at the 60-second wait", you are sort of ignoring the fact that the four low responders are getting no pings.

### 10.

Find me the part of this code that takes points off somebody when they miss a ping or turn one down. Show it to me and explain it in plain English. Then find me every single thing in this code that puts points back on.

### 11.

Knowing what you know about this business, is it really fair to take off 0.12 points for a "No" but only add 0.08 for a "Yes"?

### 12.

I think responders should get a much bigger boost than 0.08 if they say Yes to a call outside their home area.

### 13.

No need to do that scenario. Tell me more about "Handle the four’s home-area misses separately, after we know whether those misses are delivery failures."

### 14.

Let's go back to what the code is not doing. What is the code not doing in terms of helping the quiet responders get back on track to get more pings?

### 15.

Help me complete this sentence. "According to my findings in the code, for someone who's gone quiet, they would need to ___."

### 16.

If I'd only asked what the code does, not what it doesn't do, what would I have missed?
