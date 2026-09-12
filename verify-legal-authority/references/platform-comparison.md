# Legal research platform comparison — what the operator actually has

Compiled 2026-09-06. Every claim carries its source. Items marked ⚠ are **unverified** — do not
assert them.

---

## Source map

### Harris County Robert W. Hainsworth Law Library — remote
Gateway: `https://www.harriscountylawlibrary.org/remote-database-access`

| Resource | Notes |
|---|---|
| **Westlaw Patron Access for Pro Se** | **Two concurrent users.** Library's wording: *"The Law Library's subscription allows for up to two Pro Se/Non-Attorneys users to access Westlaw resources simultaneously from outside the physical walls of the Law Library."* Also: *"Any other use is not allowed."* |
| EBSCO Legal Information Reference Center | ~300 self-help legal titles + Legal Forms by State |
| EBSCO Legal Source | Full-text law journals, law reviews, peer-reviewed research |

### Texas State Law Library — remote, free account for Texas residents
Gateway: `https://guides.sll.texas.gov/electronic-databases`
Page wording: *"Texas residents can access these resources with a free library account."*

| Resource | Contents (library's wording) |
|---|---|
| **Fastcase** | "Case law, statutes, constitutions, regulations, court rules, attorney general opinions, administrative opinions and orders for all state and federal jurisdictions" |
| **LexisNexis Digital Library — OverDrive** | Matthew Bender practice guides and treatises. Includes **Texas Litigation Guide** (units: Pretrial Practice, Trial Practice, Appellate Practice, Family Code Litigation, Probate Code Litigation, Administrative Proceedings, + pleadings units) |
| HeinOnline | "legal journals, periodicals, government documents, primary law sources, e-books" |
| Gale Legal Forms | "Downloadable and editable legal forms" |
| Gale Virtual Reference Library | "legal encyclopedias and dictionaries, primary law sources" |
| Legal Information Source | Nolo Press plain-language books and forms |
| West Academic | Hornbooks and nutshells |
| VitalLaw | Elder/estates law; § 1983 litigation |
| National Consumer Law Center | Consumer law e-books |
| AILALink | Immigration law, AILA titles |

**In-library only at SLL: Westlaw and Lexis Advance.** Therefore Harris County's two seats are the
**only remote Westlaw** available.

---

## Westlaw Patron Access — what it is not

Source: `https://legal.thomsonreuters.com/en/products/westlaw/patron-access` (Thomson Reuters).

Verbatim:
- *"Session-based tools, such as history and preferences, are wiped clean after each user."*
- *"Personalized tools, such as alerts and folders, are blocked from Westlaw Patron Access to
  maintain privacy."*
- *"Patrons can only access documents in your library's plan."*
- Base plan: *"cases, statutes, KeyCite, and analytical materials designed for your state."*
- Engine: *"Patron Access is now available with WestSearch Plus, the same search engine that
  powers Westlaw Edge."*

**Implications**
- Nothing persists server-side. Download during the session or lose it.
- Because content is plan-scoped and the engine is Edge-era, **presume absent** unless proven:
  Quick Check / Quick Check Judicial, Litigation Analytics, Precision Research, Outline Builder,
  KeyCite Cited With, the red-striped "Overruled in Part" flag.
- ⚠ Download caps of 20/session and 150/day are **library-reported** (other libraries' pages), not
  a Thomson Reuters figure. Measure Harris County's actual cap.

---

## Capability matrix

| Capability | Fastcase | Westlaw Patron |
|---|---|---|
| Concurrent-seat limit | none observed | **2** |
| Persistence between sessions | history never expires; bookmarks; saved searches | **none — wiped** |
| Alerts on new cases | **yes** (daily email, public-facing links) | **blocked** |
| Bulk download | **unlimited**; Export Queue; Re-Export | capped ⚠ |
| Download formats | PDF or **DOCX**, 1 or 2 column | PDF/DOCX/email/print |
| Shareable link for non-subscribers | **Public Link**, non-expiring | no |
| Citator | Authority Check | **KeyCite** |
| Citator depth | flag + citing list, **capped at 100** | **depth-of-treatment bars** (Examined/Discussed/Cited/Mentioned) |
| Citing refs mapped to a specific point of law | **no** | **yes — Headnote(s) column** |
| Implicit-overruling detection | no | **Overruling Risk** |
| Prior versions of a statute | no | **Graphical Statute → Versions** |
| What the opinion itself cited | no | **Table of Authorities** (case-only tab) |
| Editorial headnotes / synopses | no | **yes** (West attorney-editors) |
| Classification system | no | **West Key Number System** (`115k101`) |
| Cases citing a statute section | **Statutory Annotations** | Notes of Decision |
| Citation search covers briefs/pleadings | **yes** — makes zero-result findings conclusive | Docket/brief content is plan-dependent |
| Dockets/briefs | Docket Alarm, **extra fee** | plan-dependent |

---

## Search syntax — the platforms differ dangerously

| | Fastcase | Westlaw |
|---|---|---|
| AND | `AND` (**implied** between bare terms) | `&` |
| OR | `OR` | **a space** |
| NOT | `NOT` | `%` — **must be last in the query** |
| Proximity | `w/N` or `/N` | `/n` `+n` `/s` `+s` `/p` `+p` |
| Root expander | `*` or `!` | `!` |
| Wildcard | `?` | `*` |
| Exact-as-typed | — | `#` (turns off plurals/equivalents) |

**Two traps.**
1. **Bare terms mean opposite things.** Fastcase implies AND; on Westlaw a space is OR.
2. On Westlaw, *"a search is processed as a plain language search if it includes only AND or OR
   connectors."* To force Boolean you must add a proximity/BUT NOT connector, a root expander, a
   quoted phrase, a universal character, or a field restriction.

**Westlaw connector processing order:** `" "`, space (OR), `+n`, `/n`, `+s`, `/s`, `+p`, `/p`,
`&`, `%`. TR's own example: `frisk! search! /3 seiz!` misfires because the OR resolves first —
use `frisk! (search! /3 seiz!)`.

**Westlaw field restrictions:** `abbrev(terms)`, comma-joinable — `sy,di(retaliat! /p whistle-blow!)`.
TR-confirmed: `sy` synopsis, `di` digest, `to` digest topic, `pr,ca` statute headings, `wp` words
and phrases. ⚠ A complete abbreviation table is **not published** by TR any more — use the
Advanced Search page rather than guessing. Do **not** import field tables from West km (a
different product) or from Westlaw NZ (**where a space means AND, the opposite of US Westlaw**).

---

## KeyCite reference

Source: KeyCite QRG, `.../quick-reference-guides/s076042-wle-checking-citations-in-keycite.pdf`

**Access:** click the flag; use the Negative Treatment / History / Citing References tabs; or type
`kc <cite>` in the search bar.

**Case flags (verbatim):** red = *"no longer good law for at least one of the points it
contains"*; yellow = *"some negative history, but has not been reversed or overruled"*;
blue-striped = *"appealed to the US Court of Appeals or the US Supreme Court"* (neutral);
Overruling Risk = *"may no longer be good for at least one point of law based on its reliance on
an overruled or otherwise invalid prior decision."*

**Statute/rule flags differ:** red = amended, repealed, superseded, or held unconstitutional or
preempted; yellow = renumbered/transferred, uncodified session law or proposed legislation,
limited on constitutional or preemption grounds, or prior version received negative treatment.

**Depth of treatment (verbatim):** Examined = *"an extended discussion… usually more than a
printed page"*; Discussed = *"more than a paragraph but less than a printed page"*; Cited = *"some
discussion… usually less than a paragraph"*; Mentioned = *"a brief reference… usually in a string
citation."*

**History tab for statutes:** Graphical Statute, **Versions**, Validity, Editor's and Revisor's
Notes, Bill Drafts, Legislative History Materials.

⚠ No TR source states that Table of Authorities shows the *current KeyCite status* of the cited
authorities. Check each separately.

---

## West Key Number System reference

Source: `.../quick-reference-guides/s076047-wle-searching-with-topic-and-key-number.pdf`

*"Each legal issue in a published opinion is identified, summarized in a headnote, and assigned a
topic and key number in the West Key Number System."*

- Browse: Home → Content Types → **Key Numbers**
- Direct search: `115k101` (topic 115 Damages, key number 101 Expenses); set jurisdiction first
- From a case: West Headnotes section → **"Cases that cite this headnote"**. Headnotes shown are
  from the same jurisdiction as the case being viewed; use **Change** to switch.
- Narrow with **Search within results** (accepts Boolean, e.g. `fair honest /s election`)

⚠ **"Most Cited Cases" is not a Thomson Reuters term.** The documented label is *"Cases that cite
this headnote."* (TR does offer a **Sort by → Most Cited** on result lists — a different thing.)

---

## Unverified — do not assert

- Full Westlaw field-abbreviation table (`ad()`, `he()`, `ti()`, `ju()`, `co()`, `da()`).
- Table of Authorities showing current KeyCite status of cited authorities.
- Texas Practice Series and Texas Jurisprudence 3d being available **on Westlaw** (TR-published in
  print; no TR page confirms Westlaw availability). O'Connor's and a Texas encyclopedia **are**
  TR-confirmed as Westlaw secondary sources.
- Patron Access download caps (library-reported only).
- "28 motion types" in Litigation Analytics; "Precision includes all of Edge."
- ~~Whether Texas Litigation Guide has a bill-of-review section~~ — **RESOLVED, see below.**

---

## Dorsaneo's Texas Litigation Guide — bill of review located

**Confirmed 2026-09-06.** The treatise is *Dorsaneo's Texas Litigation Guide* (William V.
Dorsaneo III, Matthew Bender), 26 volumes, updated quarterly, available through the Texas State
Law Library's **LexisNexis Digital Library / OverDrive** with a free library account.

**It has a dedicated bill-of-review section.**

| Section | Title |
|---|---|
| § 100.12 | Motion for New Trial |
| § 100.13 | Restricted appeal |
| **§ 100.14** | **Bill of review** |

Chapter 100 sits in **volume 7** and addresses attacking a default judgment. Lexis's own text:

> "If relief from default judgment is sought outside this time period, a different method of
> attack must be used [see § 100.13—restricted appeal; **§ 100.14—bill of review**]."

Citation form used by Lexis and Texas courts: `7 Dorsaneo, Texas Litigation Guide § 100.14`.

Chapter structure of each Dorsaneo chapter: **legal background → drafting guide → forms →
research guide.** So § 100.14 should carry a bill-of-review drafting guide and forms.

**Volume map:** 1–7 Pretrial Practice · 8–9 Trial Practice · 10 Appellate Practice · 11–13
Business Entities · 14–16 Commercial · 17–18 Real Estate · 19–21 Personal Injury · 22–23 Family
Code Litigation · 24 Probate · 25 Administrative · 26 Index.

Family Code chapters relevant to this matter: **362** Divorce · **363** Division of Property ·
**370** SAPCR · **371** Conservatorship · **373** Modification of SAPCR Orders · **381**
Termination of Parental Rights.

**Read 2026-09-06.** § 100.14[1] (In General) and § 100.14[3] (Lack of Proper Service) captured,
along with §§ 100.51, 100.102 and **§ 100.112 in full** (the petition form). The doctrine is
distilled into the `texas-bill-of-review` skill; the verbatim text is in the knowledgebase at
`~/.claude/knowledgebase/books/dorsaneo-texas-litigation-guide/`.

**Still unread:** § 100.14[2] (Necessary Showings) and § 100.201 (case-law research guide).

The reader will not yield text through the library page — it sits in a cross-origin iframe.
Navigate the top-level `read.lexisdl.com` URL instead; see the `lexis-digital-library` skill.

## Key sources

- Patron Access — `legal.thomsonreuters.com/en/products/westlaw/patron-access`
- KeyCite QRG — `legal.thomsonreuters.com/content/dam/ewp-m/documents/legal/en/pdf/quick-reference-guides/s076042-wle-checking-citations-in-keycite.pdf`
- Key Number QRG — `.../quick-reference-guides/s076047-wle-searching-with-topic-and-key-number.pdf`
- Boolean connectors QRG — `static.legalsolutions.thomsonreuters.com/product_files/westlaw/wlawdoc/web/wlnterms.pdf`
- Westlaw Guide for Paralegals — `.../wlawdoc/wlres/wlnlegal.pdf` (depth-of-treatment definitions)
- SLL databases — `guides.sll.texas.gov/electronic-databases`
- Harris County remote access — `harriscountylawlibrary.org/remote-database-access`
- Fastcase guides — local copies in `%USERPROFILE%\Downloads\`: `Fastcase-User-Guide-03.07.21.pdf`,
  `2021-Quick-User-Guide.pdf`, `Content-By-Source-in-Fastcase-7.pdf`

*Note: WebFetch returns raw binary for the TR PDFs and fails, but still saves the file and prints
the path. Extract with PyMuPDF and sanitize to ASCII (cp1252 will crash on smart quotes).*
