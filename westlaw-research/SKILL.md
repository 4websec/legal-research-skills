---
name: westlaw-research
description: Use when the user wants to verify a case's good-law status, run KeyCite, check depth of treatment or Overruling Risk, look up a prior version of a statute, use the West Key Number System, or otherwise research on Westlaw Patron Access via the Harris County Law Library. Triggers on "keycite", "kc", "check good law", "is this case still good", "verify on westlaw", "shepardize", "access westlaw", "law library westlaw", "key number", "headnote", "depth of treatment", "table of authorities", or any request to confirm an authority before it goes in a filing. Westlaw here is a SCARCE two-seat resource - use fastcase-research first for anything Fastcase can do. Pairs with verify-legal-authority.
---

Announce: `"I'm using the westlaw-research skill."`

**Before spending a search here, check the knowledgebase.** Treatise and practice-guide text already extracted lives in `~/.claude/knowledgebase/` — see the `knowledgebase` skill. Re-researching what is already captured wastes the effort and, on Westlaw, a scarce seat.

# Westlaw Patron Access — Harris County Law Library

**This is not full Westlaw.** It is Westlaw Patron Access, a plan-scoped library product, reached
through a subscription that supports **two concurrent users**. Treat every session as scarce.

## <EXTREMELY-IMPORTANT> Guardrails

- **Two seats only.** The library states its subscription "allows for up to two Pro Se/Non-Attorneys
  users to access Westlaw resources simultaneously." A session you hold idle blocks someone else.
  **Log out when finished.**
- **Credentials.** Username **only** from env `WESTLAW_USER`. Password **only** from env
  `WESTLAW_PW` — never hardcoded, never echoed. If unset, ask the operator to set it.
- **The operator authenticates, then hands over.** Claude never types a password or a 2FA code.
- **One lookup at a time, human-paced.** Do **not** bulk-scrape, crawl, mass-download, or run
  automated batch queries here. (This restriction is Westlaw-specific and is **not** relaxed by
  `fastcase-research`'s harvest protocol — different platform, different licence.)
- **Do not redistribute** Westlaw content beyond the operator's work product for this matter.
- **Access is for self-represented litigants and non-attorneys.** The library states "Any other
  use is not allowed."

### Set the credentials (one time)
```powershell
[Environment]::SetEnvironmentVariable('WESTLAW_USER', '<library-username>', 'User')
[Environment]::SetEnvironmentVariable('WESTLAW_PW', '<paste-password>', 'User')
```

## Spend a seat only on what only Westlaw can do

Do everything else in `fastcase-research` first. Log in to Westlaw for:

1. **Headnote-level substance** — which citing cases discuss *our specific point of law*.
2. **Depth of treatment** — how substantively a citing case engages the cited one.
3. **Overruling Risk** — implicit overruling Fastcase cannot detect.
4. **Prior versions of a statute or rule** (Graphical Statute / Versions).
5. **Table of Authorities** — what the opinion itself relied on.
6. **ALR / Am. Jur. / O'Connor's** and other analytical materials.

If the question is "does this case exist," "is there any negative treatment," or "what cases cite
this statute" — that is Fastcase. Do not burn a seat on it.

## What Patron Access does and does not have

Thomson Reuters describes Patron Access as: "Session-based tools, such as **history and
preferences, are wiped clean after each user**" and "**Personalized tools, such as alerts and
folders, are blocked** from Westlaw Patron Access to maintain privacy." Content is plan-scoped —
"**Patrons can only access documents in your library's plan**" — beginning with "cases, statutes,
KeyCite, and analytical materials designed for your state." The engine is **WestSearch Plus**
(the Westlaw Edge engine), not Precision.

**Consequences:**
- **Nothing persists.** No folders, no alerts, no history between sessions. **Download everything
  you need during the session.**
- **Presume these are ABSENT** unless proven otherwise in-session, because they are Precision-tier
  or add-on features: Quick Check and Quick Check Judicial, Litigation Analytics, Precision
  Research, Outline Builder, KeyCite Cited With, the red-striped "Overruled in Part" flag.
- **Presume PRESENT** (base plan): KeyCite, the Key Number System, cases, statutes, and the
  library's analytical modules.
- Some patron implementations cap downloads (reported 20/session, 150/day — library-reported, not
  a Thomson Reuters figure). **Measure the actual cap on the first session and record it.**

## Session protocol

### Before logging in — build the worklist
A Westlaw session is a **pre-planned batch, not exploration.** Write down every authority and the
exact question for each *before* touching the site. Nothing persists server-side, so an
unstructured session is wasted.

### Step 1 — Open the access page
Browser: `mcp__playwright-legal__*` (preferred — real selectors, reliable waits) or
`claude-in-chrome`. Browser must be **headed** so the operator can see the login.

Navigate to `https://www.harriscountylawlibrary.org/remote-database-access`, snapshot, and click
the **Launch Westlaw** link. Read the snapshot; do not rely on a fixed selector.

> A direct patron URL of the form `westlawpatronaccess.thomsonreuters.com/remote/RPA-…` exists.
> **Do not fetch it speculatively — opening it can consume a seat.**

### Step 2 — Seat check
**If a seat is unavailable you get an error / "try again later" page.**

On any launch failure or error page: **stop, report it to the operator, and do not retry.**
Repeated attempts waste time and may worsen queueing. Treat an unexplained stall past the
navigation timeout the same way — report probable seat exhaustion and hand back. Never guess that
it is a technical fault.

### Step 3 — Operator authenticates, then hands over
The operator signs in and completes 2FA. **Claude waits.** Ask plainly:
*"Sign in and complete 2FA, then tell me when you're through."* Only proceed on their word.

### Step 4 — Work the list, downloading as you go
Because nothing persists, download or capture each result before moving on.

### Step 5 — Log out
Release the seat explicitly. Report what was obtained and where it was saved.

## KeyCite

Access: click the flag on a document; use the **Negative Treatment**, **History**, or **Citing
References** tabs; or type `kc <citation>` / `keycite <citation>` in the search bar.

### Case flags — Thomson Reuters' own definitions
- **Red** — "warns the case or administrative decision is no longer good law for at least one of
  the points it contains."
- **Yellow** — "warns the case or administrative decision has some negative history, but has not
  been reversed or overruled."
- **Blue-striped** — "warns the case has been appealed to the US Court of Appeals or the US
  Supreme Court." Neutral. Removed once decided.
- **Overruling Risk** — "indicates a case or administrative decision may no longer be good for at
  least one point of law based on its reliance on an overruled or otherwise invalid prior
  decision." Appears in the Negative Treatment tab.

### Statute and court-rule flags — different meanings
- **Red** — "amended, repealed, superseded, or held unconstitutional or preempted in whole or in
  part."
- **Yellow** — renumbered or transferred; uncodified session law or proposed legislation exists;
  limited on constitutional or preemption grounds; or a prior version received negative treatment.

### Negative Treatment tab
Lists negative direct history, negative citing references, and overruling risks. Two columns do
the real work:
- **Depth** — bars showing how substantively the citing case discusses the cited case.
- **Headnote(s)** — "indicate which headnotes in the cited case contain the points of law
  discussed by the citing cases."

**Depth of treatment, TR's definitions:**
| Level | Meaning |
|---|---|
| Examined | "an extended discussion of the cited case, usually more than a printed page of text" |
| Discussed | "a substantial discussion… usually more than a paragraph but less than a printed page" |
| Cited | "some discussion of the cited case, usually less than a paragraph" |
| Mentioned | "a brief reference… usually in a string citation" |

**Use the Headnote(s) column for substance.** A red flag on a headnote we do not rely on may be
irrelevant to us; negative treatment on the headnote carrying our proposition is fatal. This is
the check Fastcase structurally cannot perform.

### History tab
Direct history plus related references, with a **graphical view**. For statutes and rules:
- **Graphical Statute** — track changes over time.
- **Versions** — prior versions of the statute or rule. ← *Use this to establish what a statute
  said on a past date.*
- **Validity**, **Editor's and Revisor's Notes**, **Bill Drafts**, **Legislative History Materials**.

### Citing References tab
"Cases, administrative materials, secondary sources, briefs, and other court documents **that cite
your case**." Narrow with **Search within results**, or by document type then jurisdiction.

### Table of Authorities
A **case-only** tab. Shows the authorities the opinion *itself* cited, organized by depth of
discussion — the mirror image of Citing References. Useful for testing whether an opinion actually
rests on what a brief claims it rests on.

*Caveat: no Thomson Reuters page states that TOA displays the current KeyCite status of the cited
authorities. Do not claim it does; check each cited authority separately.*

## West Key Number System

"Each legal issue in a published opinion is identified, summarized in a **headnote**, and assigned
a **topic and key number**."

- **Browse:** Home → Content Types → **Key Numbers** → drill into a topic.
- **Search directly:** `115k101` = topic 115 (Damages), key number 101 (Expenses). Set jurisdiction
  before searching.
- **From a case:** the West Headnotes section shows topics and key numbers for that case; the
  **"Cases that cite this headnote"** link retrieves other cases citing that exact point.
  Headnotes shown are from the same jurisdiction as the case being viewed; use **Change** next to
  the jurisdiction to switch.
- Narrow a headnote list with **Search within results**, which accepts Boolean (`fair honest /s election`).
- Digest topic as a field restriction: `to(413)`.

**This is the strongest substance tool available.** To test whether a case supports our
proposition, find the headnote carrying that proposition and pull the cases citing it.

## Search syntax — read this before searching

Westlaw's connectors are **not** Fastcase's. Two traps:

> **A space means OR. `&` means AND.** And critically: "a search is processed as a **plain language
> search** if it includes only AND or OR connectors." To force a Boolean search you must include a
> grammatical, numerical, or BUT NOT connector; a root expander; a quoted phrase; a universal
> character; or a field restriction.

| Operator | Meaning |
|---|---|
| `&` | AND — terms anywhere in the document |
| *space* | **OR** |
| `/s` `+s` | same sentence / first precedes second in same sentence |
| `/p` `+p` | same paragraph / first precedes second in same paragraph |
| `/n` `+n` | within n terms / precedes by n terms (n = 1–255) |
| `%` | BUT NOT — **must go at the very end of the query** |
| `!` | root expander (`object!` → objected, objection…) |
| `*` | universal character (`withdr*w`) |
| `#` | search exactly as typed; turns off plurals and equivalents |
| `" "` | phrase |

**Processing order — memorise this:**
`" "`, space (OR), `+n`, `/n`, `+s`, `/s`, `+p`, `/p`, `&`, `%`

TR's own warning: `frisk! search! /3 seiz!` does *not* do what it looks like, because the OR
between `frisk!` and `search!` resolves first. Use parentheses: `frisk! (search! /3 seiz!)`.

**Field restrictions** take the form `abbreviation(terms)`, and comma-join to search several:
`sy,di(retaliat! /p whistle-blow!)`. TR-confirmed abbreviations: `sy` (synopsis), `di` (digest),
`to` (digest topic), `pr,ca` (statute headings/description), `wp` (words and phrases).

*Modern TR documentation deliberately stops publishing a full abbreviation table and points to the
**Advanced Search** page instead. Do not guess abbreviations — use Advanced Search.*

## Reporting

For each authority report: citation as verified; KeyCite flag **and what it attaches to**;
negative treatment **relevant to our proposition specifically**, with depth of treatment; verified
pinpoint; and where the local copy was saved. Distinguish "no negative treatment" from "no
negative treatment on the point we rely on."

## Fallback

If the proxy or patron flow misbehaves in the automated browser, have the operator drive their own
Chrome and either paste the result or let `claude-in-chrome` read the page they landed on.
Manual-paced either way.

## Related

- `fastcase-research` — the default, unmetered platform. Try it first.
- `verify-legal-authority` — the five-gate verification workflow this feeds.
- `bluebook` — citation form.
