# gtmstack

Claude command pack for go-to-market specialists. Three canonical GTM
books (Crossing the Chasm, Obviously Awesome, Predictable Revenue)
encoded as runnable evals applied to real GTM situations. Multi-book
synthesis is the value layer single-book analysis cannot produce.

> Inspired by [gstack](https://github.com/garrytan/gstack) — Garry Tan's
> Claude command pack for builders. gtmstack is the same shape, for GTM
> specialists. Patterns extracted, infrastructure rebuilt clean.

## What it is

Each canonical GTM book is encoded as:
- A framework summary in plain language (own words, cited)
- An eval prompt template defining structured output
- A rubric that scores rigor of framework application (not framework agreement)
- A growing set of historical and current cases

You feed the analyst a GTM situation. Each book produces a structured
analysis (TALC stage, beachhead, whole product gap, positioning statement,
ICP diagnosis, outbound recommendation, etc.). A separate judge applies
the rubric and produces a weighted score against a 0.75 pass threshold.

When you run multiple books on the same case, the synthesis is where the
value compounds: convergent diagnoses across frameworks are the highest-
confidence signal in the analysis. When three independent lenses agree on
a buyer, that buyer is real.

## Quickstart

```bash
# From the repo root
bin/gtmstack list-books
bin/gtmstack run crossing-the-chasm slack-2014
bin/gtmstack synthesize cursor-2026
```

Runs use headless Claude Code (`claude -p`). No Anthropic API key required.
Requires `bun` and `claude` CLI installed.

## Install (one-time)

```bash
# Clone gtmstack
git clone https://github.com/jkkorn/gtmstack.git ~/gtmstack
cd ~/gtmstack

# Symlink the CLI somewhere on your PATH
ln -s "$(pwd)/bin/gtmstack" /usr/local/bin/gtmstack

# Symlink the skill so /gtm-office-hours works in Claude Code
ln -s "$(pwd)/gtm-office-hours" ~/.claude/skills/gtm-office-hours

# Verify
gtmstack list-books
```

## Walkthrough: what it feels like to use

You are a GTM lead. Your company is in the chasm and you don't know which
beachhead to commit to. You open a terminal, navigate to any project, and
run Claude Code:

```
$ claude
```

In the Claude Code conversation, you type:

```
/gtm-office-hours
```

The skill loads. Claude introduces itself in the gtmstack voice — direct,
specific, builder energy, anti-slop:

> *I'm gtmstack. I run four canonical GTM frameworks (Crossing the Chasm,
> Obviously Awesome, Predictable Revenue, Play Bigger) on your real GTM
> situation and produce a multi-book strategic diagnostic. The deliverable
> is a synthesis document — what to do next, sequenced in time, grounded
> in the specific places where the frameworks converge.*
>
> *I'll need ~10 minutes to capture your situation, then ~5 minutes to
> run the books. What's the GTM question you want to think through?*

You describe your situation in your own words. Maybe something like:

> *We're a Series B AI dev tools company doing $40M ARR, ~80% from
> bottom-up adoption at tech-forward startups. Our board wants us to
> "go enterprise." I'm not sure that's the right next move and I want
> to think through it rigorously.*

Claude pushes for specificity. This is where gtmstack earns its keep —
the same reflex YC partners use in office hours, applied to GTM:

> *Two things to sharpen before I run the books:*
>
> *(1) "Tech-forward startups" is a category, not a customer. Can you
> name an actual buyer role? Engineering leaders at Series-B-to-pre-IPO?
> Founders at YC-network companies? Heads of Platform Engineering at
> 500-2000-person dev-tool companies? The tighter the buyer, the
> sharper the diagnostic.*
>
> *(2) "Go enterprise" is the board's framing. What does that actually
> mean to them? Fortune 500 logos? \M+ ACVs? Direct sales motion
> replacing PLG? Each of those is a different strategic question, and
> the books will diagnose them differently.*

You answer with sharper specifics. Claude collects 8-12 signals — the
qualitative observations that ground the diagnostic — by asking
targeted questions:

- *What's your competitive landscape look like? Who shows up in pilots?*
- *What does your sales motion actually look like below \K, K-K,
  K+? Is procurement involved?*
- *What's your whole product missing for the next-tier buyer? SSO?
  SOC 2? Data residency? Specific integrations?*
- *Have any flagship enterprise prospects gone to production, or are
  they all still in pilot?*

When Claude has enough signal, it constructs a case file in JSON,
saves it to `gtmstack-output/cases/`, replicates it across the four
books' `cases/` directories, then runs:

```bash
gtmstack synthesize <your-case-id>
```

You watch four books run, one at a time, ~60-90 seconds each:

```
[crossing-the-chasm]
  → PASS 100.0%

[obviously-awesome]
  → PASS 100.0%

[predictable-revenue]
  → PASS 87.5%

[play-bigger]
  → PASS 100.0%

═══════════════════════════════════════════════════════════════════════
  Synthesis: my-company-2026
═══════════════════════════════════════════════════════════════════════

  crossing-the-chasm            PASS  100.0%
  obviously-awesome             PASS  100.0%
  predictable-revenue           PASS   87.5%
  play-bigger                   PASS  100.0%
```

Claude reads the four run reports and composes the synthesis
deliverable in `gtmstack-output/deliverables/<your-case-id>-multi-book-synthesis.md`.

You read it. The structure is consistent with what you'd get from a
strategy consulting engagement:

- **Executive verdict** (3 sentences — the headline diagnosis)
- **Where the books converge** (the high-confidence findings — same beachhead, same whole-product gap, same wrong-pin-now, same parallel-pursuit warning, named by 2-4 books independently)
- **Where the books diverge** (the strategic questions you need to resolve with your team's actual data)
- **The Q-by-Q plan** (Q2/Q3/Q4 sequenced workstreams; each step names which book(s) prescribe it)
- **Open questions to instrument** (the metrics that would change the diagnostic — build the data infrastructure to answer them)
- **What this diagnostic does NOT tell you** (honest limits)
- **Confidence summary** (calibrated 1-10 across 5-7 dimensions)

The most valuable thing in the synthesis isn't any single book's
finding. It's the *convergence pattern*: when four independent frameworks
arrive at the same beachhead through different reasoning paths, that
beachhead is more reliable than any one framework's solo opinion. Single-
book = one perspective. Multi-book = differential diagnosis.

Take the synthesis to your team. Push back on what's wrong. Update the
case file's signals with what you learn. Re-run.

Total time: ~25 minutes from first prompt to deliverable. Compare to
~3 weeks of strategy consulting at \K-K.

## A real example: Cursor 2026

The repo ships with `cursor-2026` as a public-info case. Run the
synthesis yourself:

```bash
gtmstack synthesize cursor-2026
```

Three books run on Cursor (AI code editor, ~M-M ARR Q1 2026,
contested chasm position). Compare the synthesis at
`evals/deliverables/cursor-2026-multi-book-synthesis.md` to what Cursor
is actually doing publicly. The convergent findings — engineering managers
at venture-backed software companies as the beachhead, F500 IT-led buyers
as wrong-pin-now, "AI-native code editor" not "AI coding assistant" as
the category claim — match Cursor's observable strategic moves.

That convergence with reality is the validation: the books, applied
rigorously, produce calls a thoughtful operator would also reach. The
multi-book synthesis is faster than the operator and explicit about
which framework each insight comes from.

## A live simulation: Anthropic-Brazil 2026

The other shipping deliverable is a live simulation:
`evals/deliverables/anthropic-brazil-2026-multi-book-synthesis.md`.

Four books on Anthropic's Brazilian expansion (Q2 2026), public-info
inputs only, no privileged information. The four frameworks converge on:
- **Beachhead:** Brazilian fintech engineering leadership (Nubank, Stone, Inter, XP, BTG, PagSeguro)
- **Category claim:** "regulated frontier AI" subcategory, not "another LLM API"
- **Whole-product gaps:** LGPD attestation, BRL pricing, Brazilian data residency, Portuguese product surface, local presence, named in-segment reference
- **Wrong-pin-now:** F500 IT-led, public-sector, SOE, SI-mediated incumbent banks
- **Q3 lightning strike:** same-day announcement of LGPD attestation + Brazilian residency + 1-2 anchor fintech reference customers, followed by SP office + Portuguese thought-leadership essay

Read it as a worked example of what a live simulation produces. If you
work on Anthropic's actual Brazil GTM and the diagnostic gets things
wrong, the wrongness is what gtmstack needs to learn from — DM the repo
maintainer.

## What gtmstack does NOT do

- **Tactical copy.** No cold-email subject lines, no LinkedIn ad copy,
  no sales-deck slides. Strategy is upstream of copy; copy writes itself
  once the strategy is locked.
- **Web research or competitor scraping.** Inputs come from you. The
  rubric scores rigor of framework application, not the freshness of
  external data.
- **Decisions for you.** The synthesis is a structured diagnostic with
  named open questions. The decisions belong to you and your team.
- **One-shot magic.** The first run is the starting point. Re-run with
  better inputs as you learn. The diagnostic compounds.

## The /gtm-office-hours skill (alternative invocation)

If you've installed the skill at `~/.claude/skills/gtm-office-hours`,
you can invoke gtmstack from any Claude Code session — not just from the
gtmstack repo. The skill walks through the same flow as the walkthrough
above, finds the gtmstack CLI on your PATH, and writes outputs to
`gtmstack-output/` in your current working directory.

## Books

- **crossing-the-chasm** (Geoffrey Moore) — adoption stage, beachhead,
  whole product, bowling pin sequence. *Where to play.*
- **obviously-awesome** (April Dunford) — competitive alternatives,
  unique attributes, value themes, market category, positioning statement.
  *How to position.*
- **predictable-revenue** (Aaron Ross) — ICP, role specialization
  (SDR/AE/AM), Seeds/Nets/Spears, Cold Calling 2.0, when NOT to apply
  outbound. *How to sell.*

## Cases

The current case library:

| Case | Books available | Type |
|------|-----------------|------|
| slack-2014 | crossing-the-chasm | with ground truth |
| zoom-2018 | crossing-the-chasm | with ground truth |
| cursor-2026 | crossing-the-chasm, obviously-awesome | blind |
| stripe-2011 | crossing-the-chasm, predictable-revenue | blind (productive misfit for PR) |
| anthropic-brazil-2026 | all three | blind simulation |

## Why books-as-evals

Most prescriptive frameworks have testable claims. *Crossing the Chasm*
has TALC stages, the chasm, beachhead, bowling pin, whole product —
specific concepts that a rigorous analysis either applies correctly or
doesn't. Encoding the framework as a rubric makes "does this analysis
apply Moore's lens" observable instead of vibes.

The pattern is general. The same shape (framework.md / prompt.md /
rubric.json / cases/*.json) can encode any prescriptive framework — legal
contracts, product strategy, investor memos, design systems. gtmstack is
one instance.

## Repo layout

```
gtmstack/
├── bin/
│   └── gtmstack             # CLI entry point
├── evals/
│   ├── runner.ts            # TypeScript runner (calls claude -p)
│   ├── package.json         # Bun config
│   ├── books/               # Encoded frameworks
│   ├── runs/                # Per-run reports (auto-generated)
│   └── deliverables/        # Multi-book synthesis documents
├── gtm-office-hours/
│   └── SKILL.md             # Claude Code skill for end-user flow
├── GTMSTACK.md              # this file
└── README.md                # repo-level (currently inherited from gstack seed)
```

## Status

v0.1 — three books, five cases, nine persisted runs, two synthesis
deliverables. CLI ships. Skill ships. No customer evidence yet — every
test is internal. The cursor-2026 and anthropic-brazil-2026 syntheses
are the strongest demos; both are public-info-only and explicitly note
that anyone with actual GTM context at those companies is the source
of truth for what the analysis gets right and wrong.

Roadmap: ship the runner, get one real GTM leader to run gtmstack on a
live plan, add a fourth book (Play Bigger or The Mom Test), iterate the
rubric based on customer feedback.
