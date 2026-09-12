---
name: verify-legal-authority
description: Use before any citation goes into a filing, memo, or brief - to confirm a case or statute exists, is still good law, and actually supports the proposition it is cited for. Triggers on "verify this citation", "check these cites", "is this case real", "citation audit", "check good law", "does this case say that", "audit the authorities", or whenever a draft pleading's authorities have not been checked against a database. Platform-neutral; routes to CourtListener/Descrybe MCP first, then fastcase-research, then westlaw-research.
---

Announce: `"I'm using the verify-legal-authority skill."`

**Before spending a search here, check the knowledgebase.** Treatise and practice-guide text already extracted lives in `~/.claude/knowledgebase/` — see the `knowledgebase` skill. Re-researching what is already captured wastes the effort and, on Westlaw, a scarce seat.

This is the verification workflow that `westlaw-research` and `fastcase-research` both call into.
`bluebook` verifies citation *form*; this skill verifies that the authority is *real, current, and
on point*. Run both.

<EXTREMELY-IMPORTANT>
A citation has three independent failure modes. Checking one does not check the others.

1. **It may not exist.**
2. **It may exist but no longer be good law.**
3. **It may exist, be good law, and not support the sentence it is attached to.**

Mode 3 is the most common and the least checked. A green citator flag says nothing about it.
</EXTREMELY-IMPORTANT>

## Which platform answers which question

Full side-by-side, with sources, in `references/platform-comparison.md`.

| Question | Go to | Why |
|---|---|---|
| Does this case exist? | CourtListener / Descrybe MCP | Free, no licence consumed |
| Is this citation fabricated? | **Fastcase** | A raw citation search covers briefs and pleadings too; zero hits is near-conclusive |
| Any negative treatment at all? | **Fastcase** Authority Check | Free and unmetered; caps at 100 citing cases |
| Cases citing this statute section? | **Fastcase** Statutory Annotations | Westlaw has no free equivalent here |
| Bulk-pull many authorities | **Fastcase** Export Queue | Unlimited downloads; Westlaw is capped |
| Alert me to new cases on this issue | **Fastcase** | **Alerts are blocked in Westlaw Patron Access** |
| Shareable link for an attorney | **Fastcase** Public Link | Free, non-expiring, no subscription needed |
| Does the negative treatment hit **my** point? | **Westlaw** | KeyCite Headnote(s) column — Fastcase cannot do this |
| How substantively does the citing case engage? | **Westlaw** | Depth of treatment bars |
| Was this implicitly overruled? | **Westlaw** | Overruling Risk |
| What did this statute say on a past date? | **Westlaw** | Graphical Statute → Versions |
| What did the opinion itself rely on? | **Westlaw** | Table of Authorities |
| Practice-guide treatment of the doctrine | **Lexis Digital Library** (SLL) | Texas Litigation Guide |

**Westlaw Patron Access has two concurrent seats and nothing persists between sessions.** Treat it
as a scarce batch resource: exhaust Fastcase first, build a worklist, then spend one session.

## Five gates

Run in order. Stop and report at the first hard failure.

### Gate 1 — Existence

Cheapest sources first; they consume no licence.

- `mcp__claude_ai_CourtListener__search` with `citation:"<cite>"`, then by `case_name`.
- `mcp__claude_ai_Descrybe_Legal_Engine__find_case_from_reference`.

If both resolve and agree on court and year, existence is established. Move on.

### Gate 2 — Falsification (only if Gate 1 was equivocal)

Run the citation as a **quoted string search in Fastcase across all sources** (see
`fastcase-research`). Fastcase returns the document *and every document citing it*, including
briefs and pleadings.

> **Zero results across all sources means the string appears nowhere at all.** That is the
> strongest available evidence that a citation is fabricated.

Then search the **case name** separately. Three outcomes:

| Result | Meaning |
|---|---|
| Name and cite both absent | Fabricated. |
| Name exists, cite belongs to a different case | **Misattributed** — real name, wrong reporter/court/year. |
| Name exists at a different cite | Garbled citation; correct it and re-run Gate 1. |

**Misattribution signature to watch for:** a real case name, the correct volume number, and an
`S.W.2d ↔ S.W.3d` reporter swap that lands decades away in the wrong court — often on top of an
unrelated real case. This is the characteristic failure of AI-drafted citations and it clusters:
finding one means auditing all of them.

**Cheap internal tells, no database needed:**
- Reporter/court/year must be consistent. A 1999 decision at 990 S.W.2d is not "(Tex. 1999)" — it
  is an intermediate appellate court.
- **"Tex. App." did not exist before 1981.** Texas intermediate courts were "Tex. Civ. App."
  A pre-1981 "Tex. App." cite is wrong on its face.

### Gate 3 — Good law

- **Fastcase:** Authority Check. Red flag = negative treatment; black/white = neutral only.
  **Reports cap at 100 citing cases — always say so.**
- **Westlaw:** KeyCite. Red = not good law for at least one point; yellow = some negative
  treatment; blue/striped = related appeal.

Open every flagged citing case and read what it actually says. Flags are frequently benign
("Superseded By Statute" attaching to the *citing* case's own context; "Declined To Extend" on an
unrelated point). Report which proposition the negative treatment reaches.

### Gate 4 — Substance

**The gate that matters most.** Read the holding and ask: does it support *our* sentence?

Fastest reliable method: read the **citator snippets**. Each entry is a court's own one-line
statement of the proposition the case was cited for. If none resembles our proposition, the case
is probably off-point.

Four failure types, all seen in practice:
- **Off-point** — real, current, about something else entirely.
- **Adverse** — the holding cuts against the proposition it is offered for.
- **Wrong court** — a federal habeas holding applied to a state civil matter, etc. Say so
  expressly if the argument is by analogy.
- **Unloaded predicate** — the lead case is fine but the rule it rests on comes from a bad cite.

### Gate 5 — Pinpoint

Open the document, find the language, confirm the page. Quote verbatim. Never approximate a
pincite; see `bluebook` IRON LAW #1.

## Auditing a whole filing

1. Extract every citation mechanically (regex over the PDF/DOCX). Include **letter-suffixed**
   forms — a `PX-\d+` or `\d+ S.W.\dd \d+` pattern silently misses `114A`-style variants.
2. Deduplicate; count distinct authorities.
3. Gates 1–3 on all of them.
4. Gate 4 on every authority that is the **sole** support for a proposition, and on anything that
   survived with a flag.
5. Report as a table: authority | exists | good law | on point | pinpoint | where cited.

**Report the denominator.** "5 of 27 defective" is actionable; "found some bad cites" is not.

## Reporting rules

- Distinguish **"verified to exist"** from **"verified to support the proposition."** Never let
  the first imply the second.
- State the citator's 100-citation cap every time you quote a negative count.
- If a database returns nothing, say which databases were searched and how. Absence in one source
  is not proof; absence in three, including a brief-inclusive corpus, is close to it.
- Never fill a gap with a remembered citation. Say it is unverified.

## When a filed document contains a bad citation

This is beyond document preparation. Do not decide it.

- Report precisely: what is wrong, where it appears, and what proposition depended on it.
- Note that courts treat prompt self-correction very differently from discovery by an opponent or
  the court, and that this is time-sensitive.
- Say plainly that whether and how to correct the record is a decision for a licensed attorney.

## Related

- `fastcase-research` — Fastcase access, Authority Check, statutory annotations, harvest protocol.
- `westlaw-research` — Westlaw patron access and KeyCite. Its one-at-a-time rule stands.
- `bluebook` — citation form. Complements this skill; does not replace it.
