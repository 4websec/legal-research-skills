---
name: knowledgebase
description: The local knowledgebase of books, treatises, and practice guides already read, with a machine-readable map. Use BEFORE any external research — case law, statutes, treatises, web — to check what is already captured, and AFTER any substantive read to write the knowledge back so it persists across projects. Triggers on "what do we know about", "have we read", "already researched", "check the knowledgebase", "save this book", "add to knowledgebase", treatise, practice guide, Dorsaneo, Matthew Bender, secondary source, doctrine lookup, and on starting any legal or technical research task.
---

Announce: `"I'm using the knowledgebase skill."`

The knowledgebase lives at **`%USERPROFILE%\.claude\knowledgebase\`** — global, beside `skills\`
and `memory\`, so it is available in every project regardless of working directory.

Its purpose is compounding: **each book read once, mapped, and reused forever.** A treatise
section extracted into a case folder dies with that case. The same section in the knowledgebase
answers the next matter for free.

## <EXTREMELY-IMPORTANT> Retrieval before research

**Before any external lookup — Fastcase, Westlaw, a borrowed volume, CourtListener, web search —
search the knowledgebase first.**

```bash
KB="$HOME/.claude/knowledgebase"
cat "$KB/INDEX.md"                          # what books exist, coverage, status
grep -rn "bill of review" "$KB/books/"      # phrase and citation search
cat "$KB/topics/texas-bill-of-review.md"    # doctrine entry point, cross-book
```

Re-researching something already captured is the exact failure this exists to prevent. Spending a
scarce Westlaw seat on it is worse.

**Say what you found before going outside**: what the knowledgebase answered, and what it did not.
"The KB has § 100.14 on the elements but nothing on the meritorious-defense showing, so I'm going
to Fastcase for that" is the shape. Never silently skip the check, and never imply the KB covered
something it did not.

## The structure

```
knowledgebase\
  INDEX.md                     human-scannable: every book, coverage, status, last touched
  manifest.json                THE MAP — one record per section, machine-readable
  books\<book-slug>\
    BOOK.md                    full citation, publisher, volumes, edition, access route, licence
    TOC.md                     complete table of contents, each entry read | unread
    v07\100-112.md             one file per section
  topics\<topic>.md            cross-book topic page: every section on this topic, any book
```

### manifest.json is the mapping

One record per section. It is what makes coverage measurable, sweeps resumable, and a future
embedding layer possible without restructuring.

```json
{
  "book": "dorsaneo-texas-litigation-guide",
  "citation": "7 Dorsaneo, Texas Litigation Guide § 100.112 (2025)",
  "volume": 7, "chapter": 100, "section": "100.112",
  "title": "Plaintiff's Original Petition for Bill of Review",
  "path": "books/dorsaneo-texas-litigation-guide/v07/100-112.md",
  "status": "read", "read_on": "2026-09-06", "release": "2026-02-02",
  "topics": ["texas-bill-of-review", "default-judgment", "pleading-forms"],
  "absorbed_into": ["skills/texas-bill-of-review"]
}
```

`status` is `read` | `unread` | `queued` | `unavailable`. Structural queries run off it:

```bash
python -c "import json;d=json.load(open('manifest.json'));print([s['section'] for s in d['sections'] if s['status']=='unread'])"
```

## <EXTREMELY-IMPORTANT> The write-back rule — three tiers, all three required

Every substantive read splits three ways. This is a required step, not a suggestion.

| Tier | Destination | Contents | Lifetime |
|---|---|---|---|
| **1. Source of truth** | `knowledgebase\books\…` + a `manifest.json` record | verbatim text, full citation, release date | permanent, global |
| **2. Distilled doctrine** | a global skill under `~\.claude\skills\` | rules, elements, quotable holdings, traps | loads in every project |
| **3. State + matter work** | `~\.claude\projects\C--\memory\` and the matter's `authorities\` | loans, due dates, coverage, what it means for *this* case | session / case scoped |

**Tier 2 is the step that gets skipped, and it is the entire point.** A book that produced only
files has been *stored*, not *learned*. If no topic skill exists, create one; if one exists,
extend it.

Worked example, end to end:

```
books/dorsaneo-texas-litigation-guide/v07/100-112.md   tier 1 — verbatim form + comment
skills/texas-bill-of-review/SKILL.md                   tier 2 — the doctrine, loads everywhere
<matter-root>\authorities\
  DORSANEO_100_112_FORM.md                             tier 3 — what it means for this matter
memory/lexis-digital-library-reading.md                tier 3 — loan state, coverage
```

## Adding a new book

1. **`BOOK.md` first** — full citation, publisher, volume map, edition year, how it is accessed,
   licence terms.
2. **Capture the full TOC before extracting anything.** The TOC is the map: it makes coverage
   measurable and turns "read the book" into a finite, resumable queue.
3. **Seed `manifest.json`** with every section as `unread`.
4. Extract, checkpointing after each section (see the acquisition skill for the source's
   mechanics — e.g. `lexis-digital-library`).
5. Update `INDEX.md` coverage and write tier 2 and tier 3.

## Section file format

Open every section file with a citation header, then the text:

```markdown
# 7 Dorsaneo, Texas Litigation Guide § 100.112 — Plaintiff's Original Petition for Bill of Review

Book: Dorsaneo's Texas Litigation Guide (Matthew Bender) · Volume 7 · 2025 edition
Release shown in reader: 02/02/2026 · Extracted: 2026-09-06
Source: LexisNexis Digital Library (Harris County Law Library), borrowed copy
Licence: personal litigation-research use. Do not redistribute.
```

Then the text. **Quoted passages stay verbatim and are marked as quotations; paraphrase is
labelled paraphrase.** Close with a short "what this changes" block where there is one.

## Integrity rules

- **Nothing unverified reaches tier 2.** If a rule is half-remembered, leave it out entirely
  rather than hedging it into a skill. Global skills are read as settled.
- **Always record the release/edition date.** A later release can change the text, and a rule
  quoted from a superseded release is a filing risk.
- **Never delete a section file to tidy up** — change its `status` instead. Deleting destroys the
  record that it was ever read.
- **Coverage claims must be honest.** Report `read / total` from the manifest. Never describe a
  partial read as having read the book.

## Licence and handling

Library-licensed treatise text is for personal litigation research under the library's terms.
**It stays local.** Do not publish it, redistribute it, or paste it into external services.

## Related

- `lexis-digital-library` — acquisition: borrowing and extracting from the LexisNexis Digital
  Library reader, including the whole-book sweep protocol.
- `texas-bill-of-review` — a tier 2 skill built from this knowledgebase.
- `verify-legal-authority` · `fastcase-research` · `westlaw-research` — external research, to be
  used *after* the knowledgebase is checked.

*Document preparation only; not legal advice. Attorney review required before filing.*
