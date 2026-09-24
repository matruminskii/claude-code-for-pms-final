---
name: review-checklist
description: Review a product brief, one-pager, spec or PRD against a fixed eight-point checklist (owner, ask, problem before fix, evidence, fix-to-problem link, success measure, risks and guardrails, scope consistency) before it goes any further. Use when the user asks to review, check, or sanity-check a brief, or points this skill at a document.
argument-hint: <path to the brief>
---

# Review checklist

Run the same eight checks on a brief, every time, and report a verdict for each.

## Input

The brief is `$ARGUMENTS` (a file path). If no path is given, ask which brief to review. Read the
whole document before judging anything. Judge only what is written in the brief — don't fill gaps
with what you know from elsewhere, and don't count something as present because it's implied.

## The eight checks

1. **Names who owns it.** A specific person (or role held by one named person) is accountable for
   the work. A team name, "we", or "Product" alone does not pass.
2. **States the ask.** It says what the reader must decide or approve, and by when. A brief that
   informs without asking for anything does not pass.
3. **Explains the problem before proposing a fix.** The problem — who is affected, what happens,
   and the evidence — appears before any solution, and stands on its own without the fix to prop it up.
4. **Claims are evidenced.** Key claims cite a source (data, research, tickets, code). Assumptions
   are labelled as assumptions. An unsourced figure or a guess stated as fact does not pass.
5. **Explains how the fix solves the problem.** There is a stated causal link from each part of the
   proposed fix to the problem it addresses. A fix listed without a "because" does not pass, nor does
   a problem left with no part of the fix pointing at it.
6. **Says how we'll know it worked.** A success measure with all four parts: baseline, target,
   timeframe, and data source. "Improve the experience" does not pass; missing any of the four
   parts is Partial.
7. **Names risks and guardrails.** It says what could go wrong or get worse because of the fix, and
   sets at least one guardrail metric that must not degrade.
8. **Consistent from start to end.** The scope at the end matches the scope at the start — nothing
   grew in, nothing quietly dropped out. Names, numbers, and definitions are the same everywhere
   they appear. Non-goals are stated, not implied.

## Verdicts

For each check give one of:

- **Pass** — clearly met.
- **Partial** — present but weak, vague, or only covers part of it.
- **Fail** — missing, or contradicted by the document.

Every verdict must cite evidence: a short quote or a section/line reference. For Partial or Fail,
add one concrete sentence on what would make it pass.

## Output

Reply in this exact shape, nothing before it:

```
Review: <brief title or filename> — <N>/8 pass

| # | Check | Verdict | Evidence |
|---|-------|---------|----------|
| 1 | Owner named | Pass/Partial/Fail | "<quote>" or §section |
| 2 | Ask stated | ... | ... |
| 3 | Problem before fix | ... | ... |
| 4 | Claims evidenced | ... | ... |
| 5 | Fix explains how it solves the problem | ... | ... |
| 6 | Success measure | ... | ... |
| 7 | Risks and guardrails | ... | ... |
| 8 | Consistent start to end | ... | ... |

To fix
- <one line per Partial/Fail: what to add or change, and where>
```

Omit "To fix" if everything passes. Don't edit the brief unless the user asks; this skill only reviews.
