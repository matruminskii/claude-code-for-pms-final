# 04 · X-Ray Vision — prompts

**Context:** You joined Rook two weeks ago as PM on Dispatch.
Release 4.2 shipped on 12 August, just before you arrived, and
landed badly. Last session the numbers showed you what happened: a
ping used to wait ninety seconds and now it waits sixty, people
missed pings they used to catch, and missing one counts the same as
turning one down — so four responders stopped hearing from us
altogether.

At the end of the session, ask Claude Code to save the prompts you
wrote yourself below — not the starter prompt. The closing slide has
the exact prompt to paste. By Module 6 this file is a prompt library
built from your own questions.

---

### 1.

Open the folder 00-rook/code/dispatch-routing/. This is the part of our software that decides who gets asked to take a job. I have never read code before and I am not going to start now. Walk me through what happens from the moment something goes wrong somewhere to the moment a responder's phone buzzes, in plain English, no jargon. Then tell me which file each step lives in.

### 2.

How does the calculation of reliability score look like? Does it get reset over time?

### 3.

Is it possible that some people are stuck at 0.0 and so they never or almost never get pinged

### 4.

But in theory, one could have 0 reliability score but still be pinged if they are close

### 5.

Could you check what was the proximity of those responders that stopped getting pings after 4.2

### 6.

Which files have changed in 4.2 that could have caused the current issues

### 7.

Recommend what would you do now after witnessing issue with people not receiving any scores

### 8.

I would like to know, who are the responders who get asked first. Are these heroes the ones that have been in the system before, or are these new ones

### 9.

Can you give me the ranking of before and after in terms of weekly number of pings sent and taken?

### 10.

Find me the part of this code that takes points off somebody when they miss a ping or turn one down. Show it to me and explain it in plain english. Then find me every single thing in this code that puts points back on.

### 11.

Somebody has been quiet for a month. Walk me through, step by step exactly what would have to happen for them to start getting work again.

### 12.

How is the proximity calculated? Is it dynamci or static?

### 13.

How does the score degrades with each more minute travel time? Can you show me on a chart?

### 14.

Is the proximity calculated to the actual location of the superhero or to their home address?

### 15.

Is callout.location where the hero is or where the incident is?

### 16.

How is the travel time evaluated? By common travel method or ?

### 17.

Can you think of any other, creative approaches at increasing one's score?
