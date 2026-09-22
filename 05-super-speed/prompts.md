# 05 · Super Speed — prompts

**Context:** You joined Rook two weeks ago as PM on Dispatch.
Release 4.2 shipped on 12 August, just before you arrived, and
landed badly. You've spent three sessions finding out what went
wrong: the two piles of feedback that didn't agree, the numbers that
hid four people inside an average, and the code that settled it —
the shorter timeout is what did it.

At the end of the session, ask Claude Code to save the prompts you
wrote yourself below — not the starter prompt. The closing slide has
the exact prompt to paste. By Module 6 this file is a prompt library
built from your own questions.

---

### 1.

Look into Helen's request from module 5. Describe me what she's actually asking for

### 2.

This is helpful, but I believe there are missing pieces in this brief. It's critical that it's working for all responders such as Meteor Mite, as well as Nightwell. Walk me through what might be missing in the brief.

### 3.

Prepare a proper brief,  focusing explicitly on the starvation group. Explain directly that we're skipping the Nightwell issue. Based on what we learned so far, explain what changed in 4.2, explain that we're not simply reversing the code. Introduce the key change, that will allow the starved group to regain the reliability score overtime.

### 4.

Adjust brief:
Introduce a specific mechanism for regaining the reliability score. Also, introduce notification module that, so that the people actually know that they were pinged. Also, introduce a prototype of the live scoring system that will show the responder their current reliability score and how it changes with each ping sent and taken. The numbers of pings sent and taken should also be visible.

### 5.

Save the brief exactly as it stands now as 05-super-speed/brief.md. Show me the file when it's done.

### 6.

Make the prototype look nice according to the 2026 popular design systems and templates

### 7.

When clicking decline it's actually counting the pings as taken - in fact these are not calculated as taken for the sake of scoring. Taken are accepted - declined are not taken similarly to those timeouted. Adjust the prototype

### 8.

open it in the in-claude browser

### 9.

Look at the prototype you made. Be objective. List out three things that you would improve.

### 10.

Introduce an weekly summary box, where you would show the number of pings sent and accepted the last week, how did it change compared to the previous week, as well as how did it compare with the average for other responders.

### 11.

Do not show percentage of the average, but rather the number of pings compared to the average number, and then in show the percent as a relation to the average in smaller font

### 12.

Introduce a mechanism that allows the handler to give the responder a priority for the next callout. This is only available, when the number of pings sent to this responder is at least 50% lower than the average for responders

### 13.

Now look into my prompts after the original brief was created and adjust the brief so that it reflects the current state of the prototype.

### 14.

Create me a claude artifact from the prototype
