# yan-skills

> Agent Skills for SEO and indie hackers: **build sites, pick keywords, build backlinks, query data, and review what works** — one chain, install and run.

[![English](https://img.shields.io/badge/Docs-English-black)](README.md)
[![中文](https://img.shields.io/badge/Docs-%E4%B8%AD%E6%96%87-red)](docs/README.zh-CN.md)
[![License: MIT](https://img.shields.io/badge/License-MIT-black.svg)](LICENSE)

Built by [Yan](https://github.com/yan-labs) for personal use, battle-tested in real runs. Compatible with Claude Code, Codex CLI, Cursor, and any platform that reads `SKILL.md`. MIT-licensed — take it and use it.

```bash
npx skills add yan-labs/yan-skills -g --all
```

> **Two repos, one codebase.** The install source is the canonical [`yan-labs/yan-skills`](https://github.com/yan-labs/yan-skills). The maintained mirror and releases live in the [`tokenaissance/yan-skills`](https://github.com/tokenaissance/yan-skills) fork. Both point at the same skills; install from either.

## What problem this solves

A solo site-builder's day goes like this: in the morning you want to try a new niche, so you check whether the keyword has volume and how hard it is — the tool site costs $200/month, so you open a dozen tabs and compare by hand. You settle on a word, then spend two days wiring up scaffolding, Cloudflare, domain, GA, GSC — and next time you build a site you walk through the same traps again. In the afternoon you go build backlinks: you open someone's "500 free backlink sites" list, and of the first twenty, seventeen are dead, two need sign-up review, and one demands a reciprocal link first. At night you open GSC, see "Crawled – currently not indexed", and don't know whether to change the content or resubmit.

Three months later you switch projects and start all over.

**This repo exists to kill "start all over."** It compiles each step into scripts, data files, and decision rules an AI Agent executes directly.

Two main skills, clearly split:

| | Owns | One-liner |
|---|---|---|
| [`rankup`](rankup/) | a website's **full lifecycle** | from "can this keyword work" to "which page should change three months after launch" |
| [`backlink`](backlink/) | **backlinks + logged-in data** | where to submit, whether it can be submitted, and whether it actually worked after submission |

## `rankup` — full-lifecycle site control

Version `2.32.0`. It does not re-implement Wrangler, Stripe, or trend tools — it chains those capabilities into one maintainable workflow and remembers what you did on every project.

### The eleven stages it covers

```
0  Restore project context and reconcile against live state
1  Opportunity research: trends, keywords, competition, paid space
2  Product definition: pages, data model, architecture, implementation plan
3  Initialize Monorepo (new sites use the approved TanStack Start scaffold)
4  Cloudflare: SSR, API, D1, R2, environment and bindings
5  Small-step development, four verifications: types / tests / build / migrations
6  Wire in Stripe, email, analytics, search platforms as needed
7  Deploy and verify real domain, SSR, API, uploads, auth, callbacks
8  Technical SEO, content, index, CTR optimization
9  Compliant distribution and backlinks (handed to `backlink`)
10 Monitoring, experiments, review, record — into the next round
```

Existing sites can join at their current stage; nothing forces a restart from stage 1.

### Two commands cover 90% of daily work

**`rankup init`** — onboard a project. New projects, and (more common) projects that have been running for a while with no project memory. It reads `package.json`, route lists, deploy config, `git log`, and queries the live domain, Cloudflare, GSC, and payments in real time — **it trusts no doc's claims**. Then it builds a `.rankup/` project-memory directory, backfills traffic/index/performance/revenue baselines for live sites, produces a technical audit, and lands a roadmap with P0–P2 plans. Greenfield projects create and push their repo as soon as the scaffold runs — **remote is private by default**, because an unreleased project's repo carries your niche and competitor research.

**`rankup review`** — periodic lookback. First a read-only audit script gives mechanical findings: missing files, expired records, expired script verification dates, duplicate experience entries. Then it does something few places do: **it mines your own conversations**.

```bash
node scripts/sessions.mjs --project-root . --days 14 --new-only --dump
```

The most valuable experience is often still in chat, never in any doc. This script condenses Claude Code and Codex sessions down to just the human's words and conclusions, tracks a byte-offset watermark, and resumes where it left off. You look for four things: user corrections, verified conclusions, pitfalls with root causes, and facts that overturned older records.

### Keyword & data out of the box

**`scripts/seo-webcafe.mjs`** — one script across the [哥飞](https://seo.web.cafe) SEO toolbox's backend tools:

| Subcommand | Answers |
|---|---|
| `kd` | How hard is this keyword; what's the top-9 landscape |
| `serp` | Why each result on Google's first page ranks there |
| `audit` | 40+ checks on this page — where it loses points |
| `backlink` | Is the offered link worth its price |
| `worth` | How much this site is worth by traffic and monetization |
| `history` | Who used this domain in a previous life |
| `chat` | Ask the in-site SEO Agent directly |

**Zero config, anonymous 10/day.** The interface map was captured 2026-08-07 by clicking through each tool's network panel in a real browser session, one request per tool — no loops, no login, no quota-bypassing (see [`references/seo-webcafe.md`](rankup/references/seo-webcafe.md)).

**`scripts/gt.py`** — Google Trends: compare, regions, rising, daily top. Auto-creates a venv for pytrends on first run. [`references/trends.md`](rankup/references/trends.md) holds three workflows: small-language market probing, converging a fuzzy direction into a buildable word, and catching emerging trends.

**`references/webcafe-experiences.md`** — fifteen executable rulings from 哥飞's public posts, translated into Agent-decisionable form: don't rescue old sites, change domains and restart; N sites on one template with swapped brand words = repeating yourself N times; no one-click multi-language translation, re-find words per language; never launch an unfinished site on a real domain; "crawled but not indexed" is a content problem, resubmitting won't fix it; GSC only keeps 16 months, rotate; and more.

### Project memory: `.rankup/`

Every site keeps a `.rankup/` in its own repo: project facts, architecture, decisions, baselines, experiments, releases, logs. Secret files record only name, purpose, environment, and Secret-system location — real values never enter git.

Ownership splits into three non-overlapping layers:

- **Skill layer** carries only methods that survive without the site. `scripts/validate-rankup.mjs` asserts mechanically — a site name, absolute path, local proxy, or credential location breaks the build.
- **Project layer** facts, numbers, rulings, and reusable scripts stay in each `<project>/.rankup/`.
- **Local layer** `registry.md` is a cross-project asset index generated by `scripts/registry.mjs scan`. It contains project paths, so it's gitignored, and an assertion blocks `git add -f`.

Check that table before starting work: if another project already wrote the script, take that path — don't rewrite it.

### Default stack

```bash
pnpm dlx shadcn@latest init \
  --preset b1D0eCA4 \
  --template start \
  --monorepo \
  --rtl \
  --pointer
```

Cloudflare-first: SSR and API on Workers, transactional data on D1, files and exports on R2, read-heavy config on KV, async multi-step tasks on Queues/Workflows, strong-consistency coordination on Durable Objects, real secrets in Worker Secrets or CI Secrets. Enable resources by actual need, never "in case we need it later." The four scaffold traps are documented (each was actually hit) in [`references/lifecycle.md`](rankup/references/lifecycle.md) stage 3.

## `backlink` — backlinks and logged-in data

The `SKILL.md` is XML-structured (v3.1). The reason is in the file header: this Skill is mostly laws and routing, and a law that is easy to skim past is a law that gets broken. Tagged blocks make "which rule did I just violate" answerable by name.

**34 scripts + 19 methodology docs + 4 machine-readable data files.**

### The data assets: the most expensive part of this Skill

Data files are assets; reference docs are "how to use it, and how not to fool yourself." All machine-readable, PR-able, JSON-schema'd, CI-gated.

**`data/submission-targets.json` — 492 submittable entry points, classified by gate type:**

| Gate | Count | Meaning |
|---|---:|---|
| `account` | 162 | Requires account sign-up |
| `open-form` | 131 | Open form, fill directly |
| `captcha-interactive` | 112 | Interactive captcha, human passes it |
| `reciprocal` | 38 | Requires a reciprocal link |
| `captcha-passive` | 19 | Invisible captcha |
| `personal-contact` | 18 | Need to talk to a human |
| `email-verify` | 4 | Email verification |
| other | 8 | Manual review / no entry found / unknown |

One source is community lists, but every entry is re-verified. **Example: after de-dup a third-party list of 235 directories, three parallel batches, judged by "free + no signup + no captcha", only 13 were directly submittable — a 5.5% open rate; 48 were dead (20%), some still returning 200 while their content had been replaced with crypto-promo pages.** That ratio is itself the most valuable information — it stops you from wasting an afternoon on a "743 free backlinks" list.

**`data/free-channels.json`** — 27 channels where a link can be placed directly, 25 without signup.
**`data/paid-platforms.json`** — 141 platforms observed hosting paid placement, ranked by how many independent sites use them. Want to know where competitors buy links? This table answers.
**`data/index-submission.json`** — URL-only, no-link submission channels. It's separate because it must never enter the link ledger, and it hard-codes one rule: `indexed` must name an engine — write `indexed@google` or `indexed@brave`, never a bare "indexed".

### Five browser laws

Read [`references/browser-runtime.md`](backlink/references/browser-runtime.md) before any browser action. The core ones:

1. **Only the owner's browser.** Reuse the authorized session through OpenCLI; scripts never type passwords. When a panel is logged out, the script reports the error and asks the human to log in — it does not act on its own.
2. **Always through scripts, never click the UI by hand.** Hand-clicks produce differently-shaped results each time — incomparable, and you re-hit the same traps.
3. **The browser is not a superset of pure HTTP.** Invisible captchas only reveal themselves in raw HTML; after rendering they're gone.
4. **Sessions are isolated per conversation.** Tabs must not cross.
5. **Output that looks right may already be wrong.** Modern data grids have no `<table>`/`<tr>`, and virtual scrolling silently drops rows. So row-count self-checks are two-level, reporting virtual-scroll and regex-blind spots separately.

### Submission safety: three gates

`inspect-page.mjs` probes whether the page has a submittable form → `safe-fill.mjs` fills a payload you've already reviewed, **never submitting** → `release-submit-guard.mjs` only releases when you explicitly approve that one submission.

Batch campaigns have a separate lane. Above a hundred entries, a per-target loop is a textbook "each target right, whole campaign wrong": the right move is a read-only pre-flight of the whole batch, gathering all captchas into one human queue — not letting the whole batch stall on the first captcha. Idempotency keys, queue sharding, resume, and per-action authorization all live in [`references/batch-campaign.md`](backlink/references/batch-campaign.md).

### Evidence-based verification: a directory mention is not a backlink

Submitting isn't enough. The ledger (`ledger.mjs`) runs this chain:

```
candidate → qualified → filled → submitted → public → indexed@<engine> → rel_verified
```

The final public page must be checked item by item for URL, redirect, and `rel` attribute. A link that bounces to `/out.php?id=123`, and a link with `rel="nofollow ugc"`, are not the things you thought they were.

### A general problem solved along the way: harvesting from no-API backends

[`references/harvest.md`](backlink/references/harvest.md) plus three `harvest-*` scripts; `harvest.browser.js` is a **generic virtual-scroll table extractor** that rebuilds rows by Y-coordinate clustering with adaptive columns. This knowledge is link-agnostic — ad-platform backends, e-commerce backends, any no-API SaaS report. Load `backlink` for those tasks too, and read just that one reference.

### Authorized data sources

[`references/authorized-data-sources.md`](backlink/references/authorized-data-sources.md) records how to drive third-party data panels with scripts inside your own logged-in browser: batch traffic screening (one login, N domains, 5s each, resumable), single-keyword volume/difficulty with per-country splits, and four table reports that don't offer export.

Table reports paginate; scripts either pass `--all-pages` or warn explicitly — they never quietly give you a few hundred rows short.

**The repo contains no accounts and no keys.** Panel entry points are public URLs; accounts live in your own browser session; a reader of this file gets nothing.

## The other three skills

### [`autopilot`](autopilot/) — one sentence to unattended completion

Give it "fix the bug" or "optimize performance" and it investigates, classifies, breaks the task into an XML phased plan, picks skills, defines completion criteria, then runs unattended to the end — including auto deploy, auto E2E, auto code review, no skipped stages. Invoking it authorizes fully unattended execution; it won't ask mid-flight. No dependencies.

### [`skill-link-check`](skill-link-check/) — skill directory audit

Checks whether `.agents/skills` and `.claude/skills` follow the "real source in `.agents/skills`, symlink mirror in `.claude/skills`" convention, and outputs orphan directories, missing links, duplicated directories, broken links, and wrong targets with review-only fix commands. JSON evidence output supported. **It only reports; it never touches your directories.**

Once you have many skills, eight times out of ten "why doesn't this skill work" is a link problem. Requires Python 3.10+.

### [`skillsmp`](skillsmp/) — search 1.6M+ SKILL.md files for skills

Filter by keyword, category, profession, or language; deliberately surface well-written but under-known skills. Search before you reinvent the wheel.

## Installation

```bash
# Interactive selection
npx skills add yan-labs/yan-skills

# Global, all skills
npx skills add yan-labs/yan-skills -g --all

# Just rankup
npx skills add yan-labs/yan-skills --skill rankup -g -y

# Just backlink
npx skills add yan-labs/yan-skills --skill backlink -g -y

# Update
npx skills update rankup -g -y
```

### Natural-language examples

After installing, just talk to your agent in plain language — the skills trigger themselves:

```
帮我看看 "ai headshot generator" 这个词能不能做
新建一个做生日石含义的内容站
rankup review
找找 example.com 的外链是从哪来的
这个站有哪些地方能提交
把这个后台的表格数据导出来
```

## Prerequisites

| Component | Needed by | Notes |
|---|---|---|
| Node.js 18+ | both main skills | runtime for all scripts |
| Python 3.10+ | `rankup`'s `gt.py`, `skill-link-check` | `gt.py` auto-builds a venv on first run |
| [OpenCLI](https://github.com/) + Chrome extension | all `backlink` browser actions | reuses your logged-in Chrome |
| Wrangler / Stripe CLI | per task | only when you actually reach that stage |

Check before you start:

- [ ] Node.js 18+ — `node --version`
- [ ] Python 3.10+ for `rankup`'s `gt.py` and `skill-link-check`
- [ ] OpenCLI + Chrome extension for any `backlink` browser task
- [ ] `npx skills add yan-labs/yan-skills --list` finds all five skills

Before any `backlink` browser task, run a health check:

```bash
node backlink/scripts/health.mjs
```

## Token configuration

**Tokens live only in each skill's root `.env`, never in the repo.** The `.gitignore` excludes them, and `rankup`'s `validate-rankup.mjs` asserts this at build time.

```bash
# rankup/.env
SEO_WEBCAFE_TOKEN=     # only kd needs it, wc_mcp_ prefix, generate at /kd/docs
                       # legacy KD_TOKEN= also recognized
SEO_WEBCAFE_COOKIE=    # optional; raises quota from guest 10/day to login 100/day, VIP 500/day
                       # chat forces login; anonymous is 401

# backlink/.env
TOOLS_SHARE_DASHBOARD_URL=      # your own data-panel entry
TOOLS_SHARE_APP_ORIGIN=         # landing origin, used to verify which product opened
TOOLS_SHARE_APP_ORIGIN_SEMRUSH= # another card's origin

# skillsmp/.env (see skillsmp/.env.example)
SKILLSMP_API_KEY=
```

Most of it works unconfigured: every `rankup` subcommand except `kd` and `chat` runs anonymous, and `backlink`'s database, methodology, and fill guards depend on no panel.

## Quick command reference

### rankup

```bash
# Keyword difficulty + top-9 landscape
node rankup/scripts/seo-webcafe.mjs kd "ai headshot generator"

# Page audit / SERP attribution / link valuation / domain history
node rankup/scripts/seo-webcafe.mjs audit https://example.com/page
node rankup/scripts/seo-webcafe.mjs serp "keyword"
node rankup/scripts/seo-webcafe.mjs backlink https://example.com
node rankup/scripts/seo-webcafe.mjs history example.com

# Google Trends
python3 rankup/scripts/gt.py compare "keyword a" "keyword b"

# Project audit (read-only)
node rankup/scripts/review.mjs --project-root . --days 30

# Mine sessions for unsedimented experience
node rankup/scripts/sessions.mjs --project-root . --days 14 --new-only --dump
node rankup/scripts/sessions.mjs --project-root . --days 14 --mark   # only advance watermark after digesting

# Cross-project asset index
node rankup/scripts/registry.mjs scan --roots ~/Project

# Must-run after changing the Skill
node rankup/scripts/validate-rankup.mjs
```

### backlink

```bash
# Before any browser action
node backlink/scripts/health.mjs

# What's in the 492-entry library right now
node backlink/scripts/targets-select.mjs --stats

# Take a cohort (open / captcha / account …)
node backlink/scripts/targets-select.mjs --cohort open

# Probe a page's form, login, and captcha state
node backlink/scripts/inspect-page.mjs --url https://example.com/submit

# Fill a reviewed payload, never submit
node backlink/scripts/safe-fill.mjs --session <name> --scan ./scan.json --payload ./payload.json

# A third-party link list: normalize + diff
node backlink/scripts/third-party-list-ingest.mjs --input ./list.md --out ./leads.json --new-only

# Batch traffic screening: one login, N domains, resumable
node backlink/scripts/similarweb-batch.mjs --domains-file domains.txt --out traffic.jsonl

# Ledger
node backlink/scripts/ledger.mjs list --state public

# Must-run after changing data; CI runs this
node backlink/scripts/validate-data.mjs
```

## Contributing to this repo

Data files welcome — that's the most valuable part of this project. Rules in [`backlink/CONTRIBUTING.md`](backlink/CONTRIBUTING.md); the core is one rule:

**The evidence rule.** Every channel's state must be backed by a live observation. "I saw it on someone's list" doesn't count. You say `open-form`, you opened that form yourself; you say `indexed`, you name the engine.

Run the gate before submitting:

```bash
node backlink/scripts/validate-data.mjs   # must exit 0
```

## Local development

To change the skills themselves, link the global skill directory to this repo so only one real source exists globally:

```bash
git clone https://github.com/yan-labs/yan-skills.git
cd yan-skills

# Create or repair links (back up replaced real dirs first, don't delete them)
node scripts/link-skills.mjs

# Check-only; exit 1 on drift; good for CI or scheduled checks
node scripts/link-skills.mjs --check
```

Once linked, repo changes take effect immediately — **and don't run `npx skills update` on these skills anymore**, it replaces symlinks with real-dir copies and the double maintenance comes back.

Two safeguards:

- `rankup`'s auto-update detects the repo-root `.skill-source` marker, recognizes it's running from source, and refuses to update (`blocked / source-checkout`), so scheduled checks never overwrite your local edits. The marker sits at repo root; `skills add/update` only copies single skill subdirectories, so it never ships with an installed copy and never hurts project-level installs.
- If a link is replaced anyway, rerun `node scripts/link-skills.mjs` to restore.

### Verify a sub-skill against the Production gate

Each sub-skill ships fastagent-meta-skill evidence — `manifest.json`, `agents/interface.yaml`, `evals/trigger_cases.json`, and `reports/{skill-ir,trigger-eval,prior-art-research,creation-handoff}`. Run the gate before pushing a change:

```bash
python3 /path/to/fastagent-meta-skill/scripts/validate_skill.py rankup
python3 /path/to/fastagent-meta-skill/scripts/trigger_eval.py rankup --cases evals/trigger_cases.json --output reports/trigger-eval.json
```

## Troubleshooting

**Q: Can `backlink` be used without OpenCLI?**
Yes. The 492-entry library, 141 paid platforms, 19 methodology docs, link-quality scoring, and outreach templates are all pure data and methods — no browser needed. Only actually driving the browser for harvesting and filling requires it.

**Q: Can `rankup` be used without tokens?**
Mostly. Every `seo-webcafe.mjs` subcommand except `kd` and `chat` runs anonymous, guest quota 10/day. `kd` needs a self-served public API token; `chat` needs login state. `gt.py` needs no token.

**Q: The skill is installed but not triggering?**
Run `skill-link-check`. Eight times out of ten it's a symlink problem.

**Q: Will `rankup` write my project info into the Skill?**
No, and there's a mechanical gate. `validate-rankup.mjs` asserts the Skill must not contain site names, domains, traffic numbers, property IDs, absolute paths, or credential locations — it fails the build if they appear. Project-side facts stay in each `.rankup/`; the cross-project `registry.md` index is gitignored.

**Q: How often does this data update?**
`backlink/data/` is rewritten on every live verification; `updatedAt` is authoritative. Methodology docs carry a "verified date"; expired ones get flagged in `rankup review`.

## Design philosophy

Skills aren't a set of immutable "standard answers." They're personal experience compiled into source code an Agent can execute. Install it, run a real task, then fork: delete the rules that aren't yours, add your own judgment, tools, style, evals, and release boundaries. A skill that increasingly resembles you is one that actually works for you.

## Credits and sources

These skills absorb work from other open-source projects:

- **[flaqai/backlink_skills](https://github.com/flaqai/backlink_skills)** (MIT, Flaq AI) — `backlink`'s batch-campaign operations layer: idempotency keys and queue sharding, **verify-first** (read-only pre-flight of the whole batch, captchas gathered into one human queue instead of stalling the batch), per-action authorization, resumable state sets, anchor-text strategy, and the reporting discipline that published entries report separately from filled forms. See [`backlink/references/batch-campaign.md`](backlink/references/batch-campaign.md). Their public `Free-backlink-list.md` (743 channels) is also the largest third-party lead list this Skill has tested; the normalization and diff results are in [`backlink/references/instant-publish.md`](backlink/references/instant-publish.md). Note that list and those two submit skills are separate assets in their repo — the Skill itself ships no channels, URLs come from the user.
- **[aaron-he-zhu/seo-geo-claude-skills](https://github.com/aaron-he-zhu/seo-geo-claude-skills)** (Apache-2.0) — the quality-scoring matrix, analysis templates, and outreach templates under `backlink/references/`.
- **[哥飞](https://seo.web.cafe)** — `rankup`'s keyword and audit capabilities build on his SEO toolbox; the fifteen rulings in `references/webcafe-experiences.md` also come from his public posts.

### Two skills merged into `backlink` (2026-08-16)

The former `backlink-analyzer` and `browser-harvest` merged into `backlink` and were deleted, going from three to one:

- **`backlink-analyzer`** (link-quality, toxicity, and competitor-gap analysis) was a set of pure prompt templates — no scripts, no browser channel; it could describe a link profile but not obtain one. Now it's `backlink/references/`'s `link-quality-rubric.md`, `analysis-templates.md`, `outreach-templates.md`, keeping the upstream Apache-2.0 license and attribution.
- **`browser-harvest`** (batch table extraction from logged-in backends) is now `backlink/references/harvest.md` plus three `scripts/harvest-*`. **The merge cost is stated as usual**: this knowledge is general-purpose yet now hangs under a backlink-named Skill. For non-backlink backend harvesting, load `backlink` anyway and read that one reference.

Likewise, `gt` (Google Trends and keyword workflows) merged into `rankup` and was deleted on 2026-08-16, now `rankup/references/trends.md` plus `scripts/gt.py`. The merge found that `gt/scripts/kd.py` and the existing `scripts/seo-webcafe.mjs` hit the same endpoint with the same token under two different variable names, so the duplicate was removed. The cost: `gt`'s former trigger surface is now reached through `rankup`, and the description picked up that vocabulary.

## License

Except for separately-noted third-party content, the repo is MIT-licensed. The three analysis templates under `backlink/references/` come from an upstream Apache-2.0 project; the license copy and attribution live in that directory's `LICENSE-analysis-templates-Apache-2.0`.
