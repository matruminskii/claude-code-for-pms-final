# Letting starved responders recover — a brief for Helen

**Scope:** this brief covers the *starvation* group only — Meteor Mite, The
Undertow, Vesper, Farlight (and the milder cases, Halfmoon and Corporal
Ashgrove). **It deliberately does not cover Nightwell, Stormwrack, Ironvale,
Cindermark, or The Drift.** Their tickets describe the same silence, but
their `pings_sent`/`pings_taken` data is flat-to-rising over the same weeks —
offers are reaching them and being accepted. That's a
data-integrity/attribution question (possibly `responder.id` not persisting
across device handover or re-registration), not a routing-score problem, and
it needs its own investigation with Ravi and Wen before we can design
anything for it. Folding it into this fix would mean shipping something that
visibly does nothing for half the people asking about it.

**Prototype:** [`prototype.html`](prototype.html) — open it in a browser. It
shows Meteor Mite's phone and Kip's console side by side, sharing one live
state.

## What actually happened in 4.2

Two changes shipped together on 12 Aug:

- **Routing reweighted toward proximity.** `recent-acceptance` weight cut
  from 0.40 → 0.25, `proximity` raised from 0.45 → 0.60. This was a genuine,
  long-requested fix — responders working wide geographies were losing
  callouts to someone 40 minutes closer just because that person had a
  slightly better acceptance record. Rook had been sitting on that ask for
  three quarters.
- **Callout timeout cut from 90s → 60s**, same for everyone. More offers
  now lapse into a timeout instead of getting answered — and a timeout is
  scored identically to a decline.

Neither change is the bug on its own. The bug is what they landed on top of:
a `recent-acceptance` score that **can only recover through new accepts** —
+0.08 per accept, −0.12 per decline or timeout, and nothing that restores it
on its own. Wen flagged this gap in a code comment in 2019; nobody came back
to it.

All four starved responders sat at the 1.0 ceiling for six straight weeks
before 4.2. In the 12 Aug release week specifically, each had roughly double
their normal weekly non-accept count — one rough week, arriving at the exact
moment the safety-net weight got cut in half and the timeout got shorter.
That was enough to start a loop: lower score → ranked further down → fewer
offers reach them → no further chances to earn the score back. Three of the
four had mathematically hit the 0.0 floor by 31 Aug. Meteor Mite went from
11 pings sent a week in early August to 1 in the week of 31 Aug, while the
other 15 responders averaged 10.9.

## Why we're not just reversing 4.2

Reverting the reweight puts us back to responders losing callouts to someone
much farther away because of a slightly better acceptance record — the exact
complaint that justified the change in the first place. That doesn't fix
anything; it swaps which group is unhappy. The actual defect isn't the
reweight — it's that the score this reweight now leans on more heavily has
no way to recover on its own. That's the piece we're proposing to fix.

## What we're proposing

Four changes. The first fixes the loop; the other three make it visible and
give the handler a way to act.

### 1. Weekly floor lift — the core fix

> **Once a week, for every responder marked available, any score below the
> 0.5 midpoint moves halfway toward 0.5 — whether or not they received an
> offer that week.**

From the floor that's 0% → 25% → 38% → 44%, so the loop breaks within a few
weeks even at zero offers. It's deliberately a *pull toward neutral*, not a
reset to the 1.0 ceiling: someone genuinely declining often still settles
below neutral, so this doesn't launder a habitual decliner into looking
reliable — it only reverses what one bad week did to someone who otherwise
held a strong record. That also gives Marcus a partial answer to his 14 Aug
question about whether the reweight was meant to hit repeat-decliners too.
Getting above 50% still takes accepted offers.

### 2. Every ping confirmed: notification and log

Part of why "phone never goes off" reads as one theme is that a responder
can't tell "I wasn't offered anything" apart from "I was offered something
and never saw it." Every offer that reaches the device now produces a push
notification and a log entry, labelled accepted, declined or timed out, with
the score change next to it. A timeout reads "the offer reached your phone
but expired after 60s" instead of being invisible.

The counts use one set of definitions throughout:

- **Pings sent** — every offer that reached the device.
- **Pings taken** — accepted offers only.
- **Declined** and **timed out** — both sent but not taken, counted
  separately so a missed offer is never mistaken for a "no".

### 3. The score and the week, made visible

The same view appears in two places: a new **Reliability** tab in the
Responder mobile app (next to Offers and Availability), and a new section in
the **responder detail panel** of the Handler console.

- **Current score** with a plain status: *At floor*, *Recovering* or *In good
  standing*. The console adds a trend line since 31 Aug.
- **Running totals** — sent, taken, declined, timed out.
- **Weekly summary** for the last completed week: pings sent and accepted,
  the change from the week before, and how that compares with the average of
  the other responders. The comparison shows counts first, with the gap in
  smaller text — for Meteor Mite in the week of 31 Aug: sent **1 vs 10.9**
  (−91%), accepted **0 vs 8.0** (−100%); the week before was 2 sent, 0
  accepted. These are real figures from `callout-history.csv`.

### 4. Handler priority for the next callout

The floor lift is gradual and stops at 50%; above that, only accepted offers
move the score. A starved responder still needs offers to get there. So the
handler gets a direct lever:

- **What it does:** puts the responder first in line for the next matching
  callout. Used once, then routing goes back to normal. The handler can
  cancel it until it's used.
- **When it's available:** only when the responder was sent **at least 50%
  fewer pings than the average responder** in the last completed week.
  Meteor Mite in the week of 31 Aug: 1 sent against a cutoff of 5.5 (half of
  10.9), so it's available. After a normal week it locks again, and the
  console says why.
- **What it doesn't do:** change the score. The priority offer is scored like
  any other (+0.08 accept, −0.12 decline or timeout).
- **Who sees it:** the handler sees "Priority queued" on the profile; the
  responder gets a banner and a push notification ("Kip gave you priority").
  Granting and cancelling are recorded in the existing routing-override
  audit log.

## What this looks like to the people it happens to

- **For Kip, managing Meteor Mite:** a profile that shows the score recovering
  week over week, every ping actually sent, and a weekly summary that puts
  Meteor Mite's 1 ping next to the 10.9 other responders got — instead of
  the unexplained gap with The Gale that Kip described. And, when the gap is
  that large, a button to do something about it now.
- **For a responder like Meteor Mite:** proof that a bad week didn't end
  things permanently — a score they can watch climb back, confirmation of
  every offer that did or didn't reach them, a notification when their
  handler moves them to the front of the line, and eventually the phone
  ringing again.

## Open questions

- **Priority cap.** While a responder qualifies, nothing stops the handler
  from granting priority again right after it's used. Once per week is a
  reasonable default — needs a decision.
- **Wen's review.** The floor lift and priority both touch ranking; Wen should
  confirm where each fits in `routing.py`/`history.py` before anyone
  estimates it.
- **Prototype limits.** The prototype simulates events; it doesn't run the
  real ranking, so offers don't come back on their own in the demo. The
  Gale's figure in the console comparison is illustrative, not from the data.
