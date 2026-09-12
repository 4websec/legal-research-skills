---
name: fastcase-research
description: Use when researching case law, statutes, or court rules on Fastcase via the Texas State Law Library or Harris County Law Library remote access. Triggers on "fastcase", "authority check", "cert report", "citator report", "statutory annotations", "cases citing this statute", "case law alert", "public link", "export queue", "texlaw", "state law library", or any request to pull, verify, or mine legal authority from Fastcase. For good-law verification specifically, pairs with the verify-legal-authority skill. For Westlaw/KeyCite specifically, use westlaw-research instead.
---

Announce: `"I'm using the fastcase-research skill."`

**Before spending a search here, check the knowledgebase.** Treatise and practice-guide text already extracted lives in `~/.claude/knowledgebase/` — see the `knowledgebase` skill. Re-researching what is already captured wastes the effort and, on Westlaw, a scarce seat.

## Access

**Gateway:** `https://guides.sll.texas.gov/electronic-databases` — the Texas State Law Library's
database page. Fastcase is reached by clicking through from here, not by a bookmark. Texas
residents access it with a **free library account**.

That same page is the door to nine other remote databases (see "Sibling databases" below), so
start here rather than deep-linking.

Once through, Fastcase 7 runs behind EZproxy at `fc7-fastcase-com.texlaw.idm.oclc.org`.

**No observed seat contention.** Unlike Westlaw Patron Access (two concurrent users), Fastcase has
been available on demand. **This is why Fastcase is the default and Westlaw is the exception.**

### Authentication handoff — the operator logs in, then Claude takes over

1. Claude opens a headed browser and navigates to the gateway.
2. **The operator signs in** — library account, EZproxy, and any 2FA.
3. The operator says they are through.
4. Claude proceeds.

**Claude never types credentials, security codes, or library card numbers.** If a login prompt
appears mid-session, stop and hand back rather than guessing.

Preferred browser tooling is `mcp__playwright-legal__*` (real selectors, reliable waits, network
capture). The profile at `~/.claude/playwright-legal-profile` persists, so a login often survives
across restarts — check whether you are already authenticated before asking the operator to sign
in again.

**The account is a shared institutional login** (it greets as "Texas State Law Library").
Consequences that matter:
- Bookmarks and saved searches are **not private** and may be seen or cleared by other patrons.
- **Local files are the durable store.** Anything you need later must be downloaded.

## Retrieval ladder — cheapest source first

1. **Free MCP servers** — `mcp__claude_ai_CourtListener__*`, `mcp__claude_ai_Descrybe_Legal_Engine__*`.
   Use these for existence checks, holdings, and full opinion text. They consume no licence.
2. **Fastcase** — when you need Texas coverage, the citator, statutory annotations, or a
   citation-string search across briefs.
3. **Westlaw / KeyCite** — only for the residue that genuinely needs it. See `westlaw-research`.

Do not start at step 3.

## Search — use the query language

Every search type goes in the one search bar; Fastcase detects the type.

| Syntax | Effect |
|---|---|
| `AND` / `OR` / `NOT` | Boolean. Keyword searches use an **implied AND** if you omit operators. |
| `" "` | Exact phrase. |
| `( )` | Grouped first. |
| `w/N` or `/N` | Proximity — terms within N words of each other. |
| `*` or `!` | Root expansion (`mitig*` → mitigation, mitigate, mitigated). |
| `?` | Single-character wildcard (`g??se` → goose, geese). |

Reach for proximity when hunting doctrine, not just quoted citations:
`"bill of review" w/25 ("post-answer" /2 default)`

**Search Within** — to narrow an existing result set, append terms to the query already in the
results-page search bar and press Enter. Do not re-run the search from scratch.

**Filters** — `Jurisdictions & Sources` before searching, or the left-hand facet panel after.
Selecting a jurisdiction on the left plus a content type on the right intersects them.

## Two citation techniques — know which you are using

**Type Ahead (retrieve).** Type a citation into the search bar and *do not run the search*.
Suggested Documents appears below; click the title. Works for cases, statutes, regulations and
court rules. This is the fast path for an authority you already trust.

**Raw citation search (falsify).** Run the citation as a quoted search instead. Fastcase returns
the document **and every other document that cites it**, including briefs and pleadings. Therefore:

> A quoted citation search returning **zero results** means the string appears nowhere in the
> corpus — not as a document, not as a citing reference. That is much stronger evidence of a
> fabricated citation than a case-database miss.

Use Type Ahead to retrieve. Use raw citation search to falsify. Never conclude a citation is fake
from a single source — confirm across CourtListener, Descrybe, and Fastcase.

## Authority Check (the citator)

Open a case, click the flag above the text; or click the Authority Check icon beside any case in
a results list.

- **Red flag** = negative treatment exists. **Black/white flag** = only neutral citing cases.
- Report has two parts: **Negative Treatment** (each entry bolds the language explaining *why*)
  and **Citations** (all citing cases).
- **Sort and filter inside the report — by date, times cited, and jurisdiction.** Do this before
  writing any script; the platform already does what a parser would.
- The first 20 cases can be downloaded, saved as bookmarks, or added to the Export Queue.

**Known cap: a report displays at most 100 citing cases.** "0 negative citations" therefore means
"none among the 100 shown," not a guarantee. Say so whenever you report a result.

**A citator answers "is this still good law." It does not answer "does this case support the
sentence it is attached to."** Keep those separate and report them separately.

## Statutory Annotations — cases citing a statute section

Open a statute section (Type Ahead or Browse → Outline), then click the Annotations icon (an "A"
in a box). Also available beside any statute in a results list.

Sortable by relevance/date/times cited, filterable by jurisdiction. Each excerpt bolds where the
section appears. Top 20 downloadable; queue-able; bookmarkable.

**Use this whenever a filing cites a statute or rule.** A statute cited with no construing case is
an unsupported assertion.

## Harvest-once protocol (bulk)

Fastcase caps a single download at 500 pages but documents **no limit on how much you can
download**, and provides both an Export Queue and Re-Export. Bulk retrieval here is sanctioned —
this is **not** the same licence as Westlaw, whose one-at-a-time rule in `westlaw-research`
still stands and is not overridden by this skill.

The protocol:

1. **Pre-enumerate a finite list** of named authorities before touching the platform. No crawling,
   no open-ended enumeration, no following citations outward indefinitely.
2. **Build an Export Queue from the results page** — click the add-to-queue icon beside each
   document. You do **not** need to open documents first. The queue also builds from an Authority
   Check report or a Statutory Annotations list.
3. **Export once.** Export Queue icon (cloud, top right) → verify contents → Export → name the
   queue → choose settings → Download.
4. **Choose DOCX, single column.** Two-column PDF interleaves text and corrupts extraction. Add
   term highlighting only if you need it visually.
5. **Analyse offline** against the local files.
6. **Re-Export, never re-query.** Export Queue → Past Exports → "Export Again". Costs nothing and
   hits no search.

Remove unwanted items with the trash icon before exporting.

## Sharing without redistributing

- **Public Link** — Share icon → "Copy Public Link to Document". Free, non-expiring, works for
  anyone without a subscription. Primary-law documents only. **Prefer this over sending copies**
  when handing authority to an attorney or reviewer.
- **Email Document** — Share icon → Email Document. Full text lands in the email body; recipient
  needs no subscription.

## Staying current

**Case Law Alerts.** Run a search with the filters you want → drop-down to the right of the search
bar → "Add as Alert". Emails arrive daily from `support@fastcase.com` as matching cases publish,
and contain public-facing full-text links. Filters and query cannot be edited afterwards — make a
new alert instead.

Set an alert for any live matter. New appellate authority routinely post-dates a filed pleading.

**Saved searches** — same drop-down → "Save this Search". Both live under History/Bookmarks/Alerts
(clock icon, top right), alongside document and search history, which do not expire.

## Browse and Outline View

Browse Libraries (homepage, below the search bar) → menu per jurisdiction, primary law left,
secondary right. Outline View gives a source's table of contents; the download icon beside any
organizational part (title, chapter, part) exports that whole part, first 500 pages.

**Texas content worth knowing about:**
- Rules: Texas Rules of Civil Procedure, Rules of Evidence, Rules of Appellate Procedure,
  Disciplinary Rules of Professional Conduct, and **Tex. 1st and 14th Court of Appeals Local
  Rules** (the appellate courts over Harris County).
- Statutes: Texas Statutes, Texas Session Laws, Texas Administrative Code.
- Practice sets: `Texas Pretrial Practice`, `Practitioner's Guide to Civil Appeals in Texas`,
  `Texas Small-Firm Practice Tools`, and a Wolters Kluwer `Family Law Library`.

Check a practice treatise before reconstructing a doctrine from case snippets.

## Docket Alarm (briefs, pleadings, motions, orders)

Searchable from inside Fastcase via Jurisdictions & Sources → docket material filters. Keyword and
Boolean work; **Type Ahead does not**; docket-number search does.

**Carries an additional subscription fee.** Confirm it is included before relying on it — run one
filtered test query and check whether results return. Downloads are one document at a time.

## Reporting

Whenever you report an authority, state:
1. Citation as verified (volume, page, court, year — from the document, not from memory).
2. Citator status **and the 100-citation cap**.
3. Whether the holding actually supports the proposition it is offered for, or that this was not
   checked.
4. Where the local copy lives.

## Navigation — the flows we actually use

UI landmarks confirmed in practice. The window is responsive: below ~800px the top toolbar
collapses into a `⋯` menu, and the results list narrows into a left panel once a document is open.
Re-snapshot after any layout change rather than reusing coordinates.

**Citation search → open a case**
Results list is full width; result 1's title sits near the top. Each row carries, to its right, an
add-to-**Export Queue** icon and a **Bookmark** icon; to the *left* of the case name sits the
**Authority Check** flag icon and, for statutes, the **Annotations** icon.

**Authority Check report**
Open a case, then click the **Cert Report / Authority Check** entry in the left rail (below
Filters, Results, Doc, Timeline). The panel opens with **Negative Citations (N)** as a red banner
and **Citations (N)** below it. A **download icon sits immediately right of the "Cert Report"
panel title** — that downloads the whole report as a PDF, no dialog. Do not confuse it with the
Timeline view, which is adjacent in the rail.

**Download a single document**
Download icon above the document text → dialog appears → **uncheck dual-column**, choose **DOCX**
→ Download. Two-column PDF interleaves text and corrupts later extraction.

**Export Queue (bulk)**
Tick the queue icon beside each result *without opening it* → Export Queue icon (cloud, top right)
→ verify contents → Export → name the queue → settings → Download. Past Exports appear in the
lower half of the same panel with **Export Again**.

**Statutory Annotations**
Type Ahead the statute section (do not run the search) → open it → **Annotations** icon (an "A" in
a box) above the text → filter by jurisdiction, sort by date or times cited.

**Case Law Alert / Saved search**
Run the search with filters → drop-down to the right of the search bar on the results page →
**Add as Alert** or **Save this Search** → both live under History/Bookmarks/Alerts (clock icon).

**Public Link**
Open the document → Share icon (three connected dots) → **Copy Public Link to Document**.

## Sibling databases on the same gateway

The Texas State Law Library page that leads to Fastcase also offers, free to Texas residents:

| Database | Why it matters here |
|---|---|
| **LexisNexis Digital Library (OverDrive)** | Matthew Bender treatises incl. **Texas Litigation Guide** — units for Pretrial Practice, Trial Practice, Appellate Practice, Family Code Litigation. **The practice-guide layer.** Check it before reconstructing a doctrine from case snippets. |
| **HeinOnline** | Law journals, government documents, historical primary law |
| **Gale Legal Forms** | Texas legal forms |
| **Gale Virtual Reference Library** | Legal encyclopedias and dictionaries |
| **West Academic** | Hornbooks and nutshells |
| **Legal Information Source** | Nolo plain-language books and forms |
| **VitalLaw** | Elder/estates law, § 1983 litigation |
| **National Consumer Law Center** | Consumer law e-books |
| **AILALink** | Immigration |

**Westlaw and Lexis Advance are in-library only at the Texas State Law Library.** The only remote
Westlaw is Harris County's two-seat Patron Access — see `westlaw-research`.

## Guardrails

- Never enter credentials or 2FA codes.
- Never assert a citation from memory. Retrieve it or say it is unverified.
- Do not present a citator result as substantive support.
- Do not redistribute licensed content; use Public Links.
- This skill does not relax `westlaw-research`'s Westlaw restrictions.
