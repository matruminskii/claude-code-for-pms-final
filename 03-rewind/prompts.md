# 03 · Rewind — prompts

**Context:** You joined Rook two weeks ago as PM on Dispatch.
Release 4.2 shipped on 12 August, just before you arrived, and
landed badly.

Last session you read four conversations and every support ticket
since 4.2 — and found the two piles did not agree.

At the end of the session, ask Claude Code to save the prompts you
wrote yourself below — not the starter prompt. The closing slide has
the exact prompt to paste. By Module 6 this file is a prompt library
built from your own questions.

---

### 1.

Open 00-rook/data/callout-history.csv. Every row is one responder in one week: how many times we pinged them, and how many of those they took. Release 4.2 shipped on 12 August. Tell me what changed after that date. Show me the weekly numbers before and after, and show me the rows you used to get them.

### 2.

How come some people claim phone never goes off yet they take more pings? Are you sure that the responder is properly labelled to a given ping sent and taken?

### 3.

Let's get back to the beginning. Tell me what is in the callout history file. What information can I get from it

### 4.

Is there any correlation between the handler and the reported lack of tickets among the responders?

### 5.

What's the definition of ping_taken?

### 6.

Based on what you discovered, what would be the one key number you'd put in front of the director of product to describe what happened in 4.2

### 7.

Do we have any data that would say if there are any callouts that don't get answered at all?

### 8.

Is there any possibility that ping_taken was wrongly attributed? E.g. it was calculated based on acceptance after the 60second window or something of that sort

### 9.

Now look into both the callout csv file as well as tickets. Compare these sources in-depth. For example, look when the numbers moved and when people started writing in.

### 10.

Think deeply on this. Why does data for Nightwell contradict what's in the ticket. Propose 5 unique potential reasons for that.

### 11.

Is the assignment of responder ID mentioned anywhere in the repository? Is the responder ID persistent or does it rotate?

### 12.

Can you check the who-does-what.xlsx again with a proper tool for reading .xls files?

### 13.

Is there any information about how devices are handled. Is it possible that the device was handed over to another responder

### 14.

Let's refocus on one person, Nightwell. Tell me what happened to that person. Give me their numbers week by week. Think what would have to happen for them to be getting more tickets.

### 15.

Look inside the repository, analyse all files. Perhaps there's some information on how these numbers in callout_history.csv are actually calculated

### 16.

You already see my thought process. Is there anything in the repo that may suggest that I might be missing out on?

### 17.

Now get back to responders. Look at all the responders that almost zeroed out. How does their patterns week by week look like? Is the decline penalty severe enough that they might stop receiving new pings? Has it happened in such a way that there was one week where they declined plenty of tickets so the following week they got less tickets overall? Explain this in plain language.

### 18.

Before we wrap up, three things. First: look back through this session and find the prompts I wrote myself, not the starter I pasted. Save them into 03-rewind/prompts.md, one per numbered slot, exactly as I typed them. Don't tidy them up. Second: add a few lines to the Working context in CLAUDE.md, anything we figured out today that isn't in there yet and that I'd want you to already know next session. Third: commit everything that's changed with a short message describing what this session did, then push. Tell me when it's done and give me the link to my repository on GitHub.
