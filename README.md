# Legal research skills for Claude Code

[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Skills](https://img.shields.io/badge/skills-5-brightgreen.svg)](#contents)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-skills-8A63D2.svg)](https://docs.claude.com/en/docs/claude-code/skills)
[![Companion](https://img.shields.io/badge/companion-private%20repo-lightgrey.svg)](#what-is-deliberately-not-here)

Five [Claude Code skills](https://docs.claude.com/en/docs/claude-code/skills) covering
**legal research platform access, citation verification, and treatise extraction** — the
methodology, not the results.

These were built by a self-represented litigant working Texas civil matters through public law
library subscriptions. They encode what it actually takes to get authority out of Westlaw,
Fastcase, and the LexisNexis Digital Library reliably, including the failure modes that cost
hours to find the first time.

---

## Install

Drop the skill directories into `~/.claude/skills/` (global) or `.claude/skills/` (per project):

```bash
git clone https://github.com/4websec/legal-research-skills.git
cp -r legal-research-skills/*/ ~/.claude/skills/
```

Claude Code auto-discovers them on next start. Each skill announces itself when it fires.

### If you are committing to this repo

Install the guard first — **git does not clone hooks**:

```bash
cp hooks/pre-commit .git/hooks/pre-commit && chmod +x .git/hooks/pre-commit
```

It blocks API-key literals, absolute home paths, and the party names and cause numbers of the
live matter these skills were built for, plus any large run of new blockquotes that might be
verbatim treatise text. Every check exits non-zero rather than warning: this repo is public, and
rewriting history does not un-publish a bad commit.

---

## Contents

| Skill | Lines | What it is |
|---|---:|---|
| [`verify-legal-authority/`](verify-legal-authority/) | 153 | The five-gate verification workflow — does the case exist, is it good law, does it say what you claim. Platform-neutral: routes to CourtListener/Descrybe MCP first, then Fastcase, then Westlaw. Ships a [platform comparison](verify-legal-authority/references/platform-comparison.md) (234 lines) mapping which database answers which question. |
| [`fastcase-research/`](fastcase-research/) | 261 | Fastcase via state/county law library remote access. Authority Check, citator reports, statutory annotations, the bulk harvest protocol. |
| [`westlaw-research/`](westlaw-research/) | 235 | Westlaw Patron Access. KeyCite, depth of treatment, Overruling Risk, West Key Numbers, prior statute versions. **Two concurrent seats for the whole county** — the skill is built around not wasting one. |
| [`lexis-digital-library/`](lexis-digital-library/) | 175 | The LexisDL / OverDrive reader. Extracting full text from a canvas reader that yields nothing to ordinary scraping — **screen reader mode is the key** — plus the shortcut map and whole-book sweep procedure. |
| [`knowledgebase/`](knowledgebase/) | 147 | The retrieval-before-research protocol and a three-tier write-back rule, so a treatise read once stays read across projects. |

---

## The two ideas worth stealing

**Check what you already know before you spend a search.** Every research skill here opens by
consulting a local knowledgebase. On a two-seat Westlaw subscription, re-researching something
already captured doesn't just waste your time — it blocks another patron.

**Reading a book produces files; learning it produces a skill.** The `knowledgebase` skill's tier 2
is the step that gets skipped and is the entire point. Raw extraction is tier 1. Distilling it into
a topic skill that loads automatically is what makes it durable.

---

## Scope and honesty about it

- **Texas-flavored.** The doctrine examples and the specific library subscriptions are Texas and
  Harris County. The platform mechanics — how KeyCite's depth-of-treatment bars actually read, why
  the LexisDL reader won't give up its text — are not jurisdiction-specific.
- **Access is credential-gated.** These skills assume *you* hold a valid library card or patron
  account. They do not bypass authentication, and the operator logs in by hand: Claude never types
  a password or a 2FA code. Set `WESTLAW_USER` / `WESTLAW_PW` as environment variables; nothing is
  hardcoded.
- **Claims are marked by confidence.** Behavior verified in-session is stated as such. Behavior
  read from vendor documentation but not yet reproduced is labeled a lead. Trust the labels.
- **Platform terms are reproduced, not editorialized.** Westlaw Patron Access restricts bulk
  automation and limits use to self-represented litigants and non-attorneys; the skill says so
  and tells Claude to honor it. Fastcase's harvest protocol is *not* transferable to Westlaw.

## What is deliberately not here

A companion private repo holds skills that can't be published: a doctrine skill containing
verbatim Matthew Bender treatise text (licensed for personal research, no redistribution), and
investigation skills naming real individuals. Cross-references to `texas-bill-of-review` in these
files point there. The methodology in this repo stands on its own without it.

## Not legal advice

These are research tooling. They help you find and verify authority; they do not tell you what it
means for your case. Verify everything before it goes in a filing — which is, in fact, what
`verify-legal-authority` is for.

## License

MIT — see [LICENSE](LICENSE). Applies to the skill instructions in this repo. It does not and
cannot grant any rights to third-party content on the platforms they describe.
