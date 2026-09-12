---
name: lexis-digital-library
description: How to borrow, read, and extract full text from the LexisNexis Digital Library (OverDrive) reader — Matthew Bender treatises including Dorsaneo's Texas Litigation Guide. Use when reading or extracting any borrowed library ebook or treatise, planning a whole-book sweep, or when the reader will not yield text. Triggers on "lexisdl", "LexisNexis Digital Library", "OverDrive", "Dorsaneo", "Texas Litigation Guide", "Matthew Bender", "treatise", "practice guide", "borrow a book", "library ebook", "read.lexisdl.com", and on the symptom — "get_page_text returns nothing", "reader won't extract", "canvas reader", "screenshots timing out".
---

Announce: `"I'm using the lexis-digital-library skill."`

This is the **acquisition** side. Where the extracted text goes and how it becomes durable
knowledge is the `knowledgebase` skill — read that one too before a substantial read.

## Access

| Instance | Card |
|---|---|
| `lexisdl.com/library/harriscountylaw` | Harris County Robert W. Hainsworth Law Library |
| Texas State Law Library instance (`guides.sll.texas.gov/electronic-databases`) | free Texas-resident card |

**The operator logs in, then hands over.** Claude never types credentials or 2FA codes.

## Lending etiquette

OverDrive lending model: limited copies, **7-day default loan**, 10 limited-copy titles at once.

**Borrowing takes the only copy out of circulation.** Confirm with the operator before borrowing,
and return early when the read is done. **Record the due date in memory** — after it the text is
unreachable, and anything not extracted is gone.

## <EXTREMELY-IMPORTANT> The extraction technique — two steps, both required

Extraction fails unless **both** of these are true. Step 2 is the one that is easy to miss, because
in visual mode the text is plainly visible on screen while being **absent from the DOM** — so
`get_page_text` returns only toolbar chrome and it looks like a hang or a "wedge."

**Step 1 — top-level URL.** The library page embeds the reader in a **cross-origin iframe**. Take
the reader's own URL, shape `https://<host>.read.lexisdl.com/?d=<base64 token>&p=lib-<id>`, from
the address bar or the iframe `src`, and **navigate to it as a top-level page.**

**Step 2 — screen reader mode.** The reader has two modes. **Visual reading mode renders to a
canvas-like layer with no extractable text.** Switch to the assistive mode:

- The header carries an **"Enter screen reader mode"** button (`find` locates it; shortcut
  **Shift+'**). It is a skip-link, so it may not be visible in a screenshot.
- Confirmation: the page turns high-contrast with green/pink links and **"Previous part" /
  "Next part"** controls, and `get_page_text` reports `Source element: <article>`.
- Diagnostic: if `get_page_text` output contains **"You are in visual reading mode"**, step 2 has
  not taken. That string is the definitive tell.

**The toggle is unreliable.** It often does not fire while a panel (TOC or Find) is open, and the
accessibility label stays "Enter screen reader mode" even after it succeeds, so trust the
screenshot or the `<article>` source line, not the label. Clicking twice toggles it back off.

**Two things vendor docs add about the failure** (help.lexisdl.com/3040 and /3048, read 2026-09-07;
documented but not yet reproduced in-session — treat as leads, not verified behavior):

- **Shift+V is a documented alternate binding** for the same toggle. When Shift+' is swallowed, try
  Shift+V before falling back to transcription.
- Shift+' is specified to work only **"after entering focus or forms mode"** — i.e. the keystroke
  reaches the reader only when focus is inside its interactive layer, not on page chrome. That is
  the likely root cause of the flakiness above. **Click into the body text first, then send the
  shortcut.**
- **Shift+/ opens the reader's own keyboard-commands menu**, which lists the live bindings for the
  current build. Cheaper than guessing when a shortcut misfires; note the modifier is
  user-configurable, so a collision is possible.

**Fallback when the toggle will not engage** (it failed outright on § 100.51): page through with the
arrow keys and read the screenshots. Slower and it is transcription rather than a machine dump —
**mark anything captured this way as transcribed** so a future session knows to re-verify before
quoting it in a filing.

The `?d=` token is a session artifact — read it live from the open reader rather than reusing a
stale one from notes or an earlier session.

Verified on **LexisDL**. Other OverDrive readers plausibly behave the same way, but that is
**untested** — say so rather than asserting it.

## Paging

**Chapter-level jumps beat part-level clicking when you know where you are going:** Shift+.
(next chapter) and Shift+, (previous chapter), with Shift+PageDown / Shift+PageUp as alternates.
Page-level: PageDown or Space forward, PageUp or Shift+Space back. Vendor-documented, not yet
exercised in a sweep.

The **"Next part"** button advances one section at a time, and **its element ref survives across
clicks**, so the loop is one ref reused:

```
click "Next part" → get_page_text → append → repeat
```

Left and right arrow keys also turn pages. Long sections may span several "parts" — keep paging
until the next section heading appears, rather than assuming one click equals one section.

The header shows a **reading-position percentage**, which is the fastest way to say where you are
in a volume ("at 8%").

## Find within book — search the whole volume

The magnifier in the reader toolbar (**Ctrl+F**) searches the entire volume and returns results
**grouped by section**, each with a jump link. This answers "where does this book discuss X"
in one query instead of paging blindly, and it is the right first move when the TOC heading does
not obviously match the concept.

Opening it is fiddly: a plain click only *focuses* the toolbar button. **Click it, then press
Return** to open the panel, then click the input and type.

**On the "wedge" that isn't.** A page stuck on "Loading" with `get_page_text` returning only
toolbar chrome is almost always **visual reading mode**, not a hung reader — check for the "You are
in visual reading mode" string before concluding anything is broken. The persistent
"Loading. Click to pause." bar is the book pre-downloading in the background and does **not** block
extraction. Prefer navigating by **table of contents** (Shift+C) over search-result links anyway:
the TOC has a **filter box** that searches all section headings (type `100.14`), which is the
fastest way to reach a known section.

## Other reader capabilities

| Control | Shortcut | Use |
|---|---|---|
| Table of contents | Shift+C | reliable navigation; prefer over search-result jumps |
| Find within book | Ctrl+F | whole-volume search, grouped by section |
| Print chapter | Ctrl+P | chapter-level export |
| Annotations / highlights | Shift+A / Shift+H | persist per account |
| Display settings | Shift+S | |
| Close book | Shift+Q | observed in-session; not in vendor shortcut list |
| Keyboard-commands menu | Shift+/ | authoritative list of live bindings for this build |
| Next / previous chapter | Shift+. / Shift+, | faster than the "Next part" loop for known targets |
| Screen reader mode (alt) | Shift+V | second lever when Shift+' will not fire |
| Title info / recent activity | Shift+I / Shift+R | |
| Dictionary | Shift+D | |

The loan token in the reader URL carries `allow-clipboard` and `allow-print` permissions — copying
and printing are licensed, which is what makes extraction legitimate here.

## Whole-book sweep protocol

How to actually read an entire book without losing the run.

1. **TOC first, always.** Capture the complete table of contents before extracting any body text.
   Write it to `knowledgebase\books\<slug>\TOC.md` and seed `manifest.json` with **every** section
   as `unread`. The TOC is the map — without it, "read the book" has no finish line and no
   resume point.
2. **Work a chapter at a time**, in TOC order, pulling the next target from the manifest queue.
3. **Checkpoint after every section.** Write the section file, flip its `status` to `read`, then
   move on. **Never batch writes to the end of a run** — a dropped session or an expired loan then
   costs one section instead of the whole sweep.
4. **Resume from the manifest.** On interruption, the queue is whatever is still `unread`. No
   state lives in the conversation.
5. **Report coverage honestly** as `read / total`, naming what was skipped and why. Never describe
   a partial sweep as having read the book.
6. **Scope by need, not completeness.** A 26-volume treatise read end to end is a large
   multi-session job. Take the chapters the work actually touches and let coverage grow over time.

## Dorsaneo's Texas Litigation Guide — volume map

26 volumes, Matthew Bender, updated quarterly. Cited as `7 Dorsaneo, Texas Litigation Guide
§ 100.14`.

```
1-7   Pretrial Practice        14-16  Commercial         22-23  Family Code Litigation
8-9   Trial Practice           17-18  Real Estate        24     Probate
10    Appellate Practice       19-21  Personal Injury    25     Administrative
11-13 Business Entities                                  26     Index
```

Each chapter runs **legal background → drafting guide → forms → research guide**, so the form for
a chapter's remedy sits in the same chapter as its doctrine.

Chapters already touched are recorded in the knowledgebase — check `INDEX.md` before borrowing.

## Related

- `knowledgebase` — where extracted text goes, the map, and the three-tier write-back rule.
- `texas-bill-of-review` — doctrine distilled from Dorsaneo ch. 100.
- `verify-legal-authority` · `fastcase-research` · `westlaw-research` — other research routes.

*Document preparation only; not legal advice. Attorney review required before filing.*
