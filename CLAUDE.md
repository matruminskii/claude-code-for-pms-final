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

<!-- Keep the block above at the top of this file. Everything you add
     during the course goes below this line. -->

---

## Working context

You are PM for **Rook Dispatch**. Source docs: `00-rook/company/` for the official
material. Also worth knowing about, none of it referenced from company/ and easy to
miss: `00-rook/code/dispatch-routing/` (the actual routing implementation — routing.py,
config.py, offer.py, history.py, availability.py), `00-rook/data/callout-history.csv`
(weekly per-responder ping counts, the closest thing to real numbers on the 4.2
situation), and `00-rook/feedback/` (handler interviews under `interviews/`, all 25
support tickets under `tickets/`). Cross-checking these against each other and against
the official docs is where the findings in "Data contradicts the narrative" below came
from — worth doing again as more weeks of data come in.

### The products
Rook builds coordination/provisioning software for the protective-response sector —
masked responders and the handlers/quartermasters who support them. Rook is not the
responders' employer; software is sold directly to responders and to handlers. Two
products, both currently on release **4.2**, shipped monthly on the same train.

**Rook Dispatch (yours)** — responder coordination.
- *Surfaces*: web console (Handler), native mobile (Responder). Both stable; **routing
  is where the interesting work and the risk both live** (Priya's words).
- *Core workflow*: incident entered (by handler, or pushed from intake) → Dispatch
  ranks available responders (**routing priority**) → **callout offer** sent to the
  top-ranked responder's mobile → accept / decline / timeout → next in line, or
  acceptance closes the loop.
- *Routing priority inputs*: proximity (travel-time estimate, not straight-line — 4.1),
  current availability, capability match, recent acceptance history (a decline or
  timeout lowers this until it recovers). Config ships with the release, not
  handler-adjustable at runtime.
- *Capability tags*: flight, structural-entry, hazmat-tolerant, cold-weather, aquatic,
  crowd-management, de-escalation.
- *Handler console features*: incident entry, coverage view, routing override,
  availability/capability-tag management, filters that persist between sessions
  (4.2), responder detail panel, coverage report export, audit log for routing
  overrides (4.0), responder profile (4.0).
- *Responder mobile features*: receive/accept/decline callout offers, set
  availability, push notifications (reliability improved 4.1).
- *Other mechanics*: callout timeout — 60s as of 4.2, was 90s, same for every
  responder; bulk callout for incidents needing more than one responder (4.1).
- *Metrics*: acceptance rate (headline, weekly aggregate), time-to-accept (median
  seconds), coverage gap (capability mismatch — a capacity problem, distinct from low
  acceptance).
- *Owns*: the **Responder Availability Record**, read by Supply.

**Rook Supply** — gear provisioning.
- *Users*: Handler (raises requisitions, files field failure reports, manages a
  responder's kit), **Quartermaster** (approves, fulfills, holds the catalog/stock).
- *Core workflow*: requisition raised against the equipment catalog → routes to a
  quartermaster for approval → fulfillment tracked to delivery/issue → maintenance
  schedule generated per item from its service interval → field failure reports feed
  back into item history and can trigger early maintenance.
- *Dependency on Dispatch*: maintenance scheduling reads the Responder Availability
  Record to avoid booking maintenance into likely-callout windows — read-only; changes
  on Dispatch's side flow through automatically, no work needed on Supply's side.

Confidentiality: never store or try to reconstruct a responder's legal identity — only
capability tags, availability windows, callout history. This is contractual, not a
preference. Read Security Policy 4.1 before touching responder records. Two people
have been fired for trying to identify a responder.

### Vocabulary (full glossary: `company/glossary.docx`)
**Responder** = accepts callouts, independently operating, never a legal identity in
our systems. **Handler** = responsible for a responder or small group — availability,
gear, readiness; usually the one clicking around in the product. **Quartermaster** =
owns equipment stock and approvals, a Supply user. **Cover identity** = responder's
public persona; we hold no mapping to a legal identity. **Callout** = the unit of work
(a request for a responder to attend an incident). **Callout offer** = one callout
presented to one responder. **Callout timeout** = how long an offer stays live before
moving to the next responder, same for everyone (cut **90s → 60s in 4.2**). **Decline**
≠ timeout in the data, though both push the callout onward — declining or timing out
also lowers the recent-acceptance component of routing priority until it recovers.
**Capability tag** = labeled competency on a responder record, matched against what an
incident requires (full tag set under Dispatch, above). **Mutual aid** = cross-region
coverage between responders — not built, Q4 exploration. Supply-side terms you'll hear
on shared calls: **requisition**, **field failure report**, **service interval**.

### Where things stand (as of your start, early Sept 2026)
**4.2 shipped 12 Aug**: reweighted routing toward proximity vs. recent acceptance
history (a long-requested fix from responders working wide geographies, so a nearby
responder isn't skipped for one 40 minutes out with a better acceptance record — Rook
had been sitting on the ask for three quarters), cut the offer timeout to 60s, added
console filter persistence, plus 3 defect fixes (duplicate push on re-offer, capability
tag ordering, coverage-report timezone).

**Live issue**: acceptance is down and tickets are ~3x normal since ~13 Aug, in two
themes — (1) "phone never goes off" (unexplained) and (2) "buzzed and was already gone
by the time I answered" (expected — shorter timeout). Split is roughly two-thirds
theme (1) to one-third theme (2), per Nadia's tracking as of 26 Aug. Priya's (outgoing
PM) read: likely mostly seasonal (August is soft every year) confounded with the
timeout change, expect it to ease in September — but she flagged that explicitly as
something to check, not trust, since it was never validated against real numbers.
**Do not let this become a "revert 4.2" conversation** — the routing change was a
genuine, long-standing ask; reverting just trades one unhappy group of responders for
another. Nadia has the ticket-theme breakdown ready to hand off. Marcus asked the team
to give you a week to ramp before regrouping properly on the 4.2 picture — that
regroup is due now.

Also unresolved, from the team Slack: Marcus asked (14 Aug) whether the reweighted
routing was meant to apply to responders who've been declining a lot too, or only to
everyone else — the config doesn't distinguish, and it's unclear whether that was a
deliberate call or a side effect. Wen said she'd look on return from PTO (back since
24 Aug); doesn't look resolved yet. Worth raising directly with her — it may bear on
the "phone never goes off" theme.

**Known gaps to close**: no written spec of how routing/ranking actually decides who
gets pinged (Wen built it, it lives in her head) — Priya asked her successor (you) to
write one. Console filter persistence will generate cosmetic/noise tickets — expected,
not worth chasing. Some Q3 items got squeezed out of 4.2 when the timeline compressed;
which are still committed vs. quietly dropped is an open conversation with Helen that
hasn't happened yet.

**Q3 roadmap** (`company/roadmap-q3.pdf`, owner Helen Achebe, reviewed monthly):
committed to 4.2 — change to who gets pinged, Availability Confidence (a confidence
score alongside stated availability, driven by support escalations), ping timeout
tuning. Committed to Supply 4.3 — requisition approval chains. Exploring for Q4 —
handler phone app (Supply), shared cover between responders (Dispatch). Commitments
against a numbered release are locked; changes go through Product, not directly.

### Data contradicts the narrative (your own analysis, not yet shared with the team)
Cross-referencing `../data/callout-history.csv`, the routing code in `../code/dispatch-routing/`,
and `../feedback/` (4 handler interviews + all 25 tickets) — nobody else appears to have done
this yet. Marcus called his 18 Aug numbers "rough, not the real numbers," and Priya's seasonal
read was explicit that it was never checked against actuals.

- **Not a slump — a redistribution.** Total offers/week across the 16 sampled responders is
  flat (172 → 165, late Jun to late Aug); overall accept rate is down only ~4pts (76.7% →
  72.7%). Meanwhile Nightwell, Stormwrack, and Sgt. Falkirk are getting *more* offers than
  ever (highest volumes in the dataset), while Farlight, Meteor Mite, The Undertow, and Vesper
  collapse to 0-1 offers/week by 8/31, down from a steady ~11-15/week in June/July.
- **Two different problems are hiding under "phone never goes off"**: (a) confirmed starvation
  for the 4 collapsed responders, plausibly from the 4.2 weight change (proximity 0.45→0.60,
  recent-acceptance 0.40→0.25, hard 45-min cutoff) combined with ranking that never excludes
  anyone, just reorders them — corroborated directly by Kip's interview (Meteor Mite vs. The
  Gale, same handler/week/city, opposite trajectories in the data) and by Ambrose/Aunt Dot each
  independently describing "a slower response used to still win, now it doesn't." (b) An
  unexplained mismatch for Nightwell/Stormwrack/Falkirk (and milder cases: Ironvale, Cindermark,
  The Drift, Ashgrove, Halfmoon) — their tickets describe the same silence, but `pings_sent` is
  flat or rising for them. Either the counting is wrong, or offers are being marked sent without
  the push reaching the device — worth ruling out before trusting acceptance-rate as a measure
  of what's actually happening.
- **Capability tags aren't a hard filter in routing.** `routing.py`: the availability query that
  builds the candidate list doesn't check capability at all; `capability_score()` is
  partial-credit and only 15% of the ranking score, unchanged since 4.0. A responder with none
  of the required tags can outrank one who has them, if closer and with a decent acceptance
  score — worth confirming with Wen this is intentional, given tags like hazmat-tolerant.
- **Marcus's 14 Aug Slack question has a partial answer already in the code**: `history.py` —
  the decline/timeout penalty never decays on its own (an open TODO from Wen, dated 2019). But
  its weight actually shrank in 4.2, so repeat-decliners are penalized *less* on that axis than
  before, not more. Doesn't explain the starvation pattern above, which looks proximity-driven.
- **The "two themes" aren't as separate as tracked.** Several tickets (Nightwell, Undertow,
  Cindermark, Ironvale) show the same sequence — long silence, then one offer lost almost
  instantly — not two disjoint complainer groups.

Worth raising with Marcus and Wen before the 4.2 regroup — it argues against waiting for
September to look closer.

- **Supply-side gaps, only visible in Sofia's interviews, written up nowhere**:
  Halloran (handler, also runs the gear cage) describes the requisition approval queue
  as flat and unprioritized in practice — an 11-day wait on a cracked vest plate
  despite setting the priority field, which doesn't change anything about how fast a
  request moves. Field failure reports also "go into a void" — filed, but no feedback
  to the filer on whether anything happened. Neither is in the Supply one-pager or the
  Q3 roadmap's justification for requisition approval chains — not your product, but
  worth a heads-up to whoever owns it.
- **Two concrete, repeated design asks with no home in any doc**: dark mode for the
  console (Kip, raised unprompted in every conversation with Rook) and distinct alert
  sounds per responder for handlers managing more than one. Both only exist in Sofia's
  interview transcripts.

### People (`company/who-does-what.xlsx`, `company/notes/handoff-from-priya.docx`)
- **Helen Achebe** — Director of Product, Dispatch & Supply, owns roadmap/commitments.
  Chicago. "She's good. She'll give you room." (Priya)
- **Marcus Oyelaran** — Engineering Manager, Dispatch. Straight talker, will tell you
  when something's a bad idea — start here for anything you're unsure about; can
  usually pull numbers for you. Chicago.
- **Wen Li** — Staff Engineer, built the routing/ranking logic. The only real source of
  truth on how a responder gets ranked — talk to her, no document exists yet. Berlin.
- **Sofia Marino** — Product Designer, console + phone app. Chicago.
- **Nadia Hoffmann** — Support Lead, Dispatch & Supply, sees complaint volume before
  anyone else; worth a standing 15 minutes. Berlin.
- **Ravi Menon** — Data Analyst, Dispatch & Supply, weekly reporting on how often
  responders are answering; requests go through `#data`. Singapore.
- **Priya Raghunathan** — your predecessor as Dispatch PM (14 months), departed 21 Aug
  2026. Left a handoff doc; reachable through Marcus only if something's on fire.
