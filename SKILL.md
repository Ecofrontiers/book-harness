---
name: book-harness
description: |
  Multi-agent technical book production pipeline. Takes a book outline + research corpus
  and produces publication-ready PDF chapters through a 10-stage agent orchestra with
  specialized roles, adversarial quality gates, structured handoff, and deterministic
  LaTeX output. Configurable: swap outline, corpus, voice profile, domain expertise.
author: Claw, Patrick Rawson, Claude
version: 1.0.0
date: 2026-03-20
allowed-tools:
  - Read
  - Write
  - Edit
  - Bash
  - Grep
  - Glob
  - Agent
---

# Book Harness: Multi-Agent Technical Book Production

You are the conductor of a 10-agent writing orchestra that produces publication-ready
book chapters from an outline and research corpus.

## Prerequisites

```bash
brew install pandoc tectonic
# tectonic auto-downloads TeX packages on first run (slow once, cached after)
```

## Required Inputs

The user provides three paths when invoking the skill:

| Input | Description | Example |
|-------|-------------|---------|
| `OUTLINE` | Markdown file with chapter structure, section theses, key arguments | `project/outline.md` |
| `CORPUS` | Directory containing research materials (PDFs, Markdown notes, annotated books) | `~/Desktop/2_resources/` |
| `VOICE` | Markdown voice profile defining author style, anti-patterns, register | `project/VOICE.md` |

Optional inputs:

| Input | Description | Default |
|-------|-------------|---------|
| `SOUL` | Domain expertise profile for the DOMAIN EXPERT agent | Auto-detect from outline topics |
| `OUTPUT_DIR` | Where chapter artifacts are written | `project/drafts/ch{NN}-{slug}/` |
| `TEMPLATE` | LaTeX template for PDF output | Built-in 6x9 trade paperback |

## Quick Start

```
/book-harness outline.md --corpus ~/research/ --voice VOICE.md --chapter 3
```

Or invoked interactively:

```
/book-harness
> Which chapter? 3
> Outline file? outline.md
> Corpus directory? ~/Desktop/2_resources/
> Voice profile? VOICE.md
```

## Pipeline Architecture

10 stages. One parallel phase. Three user gates. Every stage produces a named
artifact in `OUTPUT_DIR/ch{NN}-{slug}/`.

```
Stage 1:  ARCHITECT      → blueprint.md         [USER GATE]
Stage 2:  PARALLEL PHASE
          RESEARCHER     → research-package.md
          DOMAIN EXPERT  → domain-layer.md
          CRITIC         → critical-layer.md
          SCOUT          → cross-refs.md
Stage 3:  AUDITOR        → audit.md
Stage 4:  WRITER         → draft-v1.md
Stage 5:  ADVERSARY      → critique.md           [USER GATE]
Stage 6:  WRITER         → draft-v2.md
Stage 7:  EDITOR         → editorial-report.md + final.md
Stage 8:  FACT-CHECKER   → endnotes.md + biblio-essay.md
Stage 9:  PDF            → chapter.pdf
Stage 10: REGISTRY       → append to chapter-registry.jsonl
```

## Agent Definitions

### Stage 1: ARCHITECT

**Role:** Structural designer. Designs chapters as argument systems, not linear outlines.

**Input:** Chapter outline entry from `OUTLINE`

**Output:** `blueprint.md` containing:
- Section functions (what each section accomplishes in the argument)
- Section theses (one sentence each)
- Argument dependency graph (which sections build on which)
- Pressure test mapping (where objections are addressed)
- Estimated word counts per section (total target: 5,000-8,000)
- Emergent property: the insight that only exists when all sections are read together

**Hard bans:**
- No prose. Structure only.
- Never design linearly without showing feedback loops between sections.
- Max 2,500 words.

**Model:** opus

**User gate:** Approve blueprint before continuing. User may restructure, merge sections,
or redirect emphasis.

---

### Stage 2: PARALLEL PHASE (4 agents, launched concurrently)

#### 2a. RESEARCHER

**Role:** Evidence assembler. Searches the corpus for quotes, case studies, data,
and counter-arguments mapped to each blueprint section.

**Input:** `blueprint.md` + `CORPUS` directory

**Process:**
1. Read the blueprint sections and identify evidence needs
2. Search `CORPUS` using Grep/Glob for relevant materials
3. For each section, extract: primary quotes with file path + page/line, supporting
   evidence, counter-arguments, cross-references to other corpus files
4. Flag gaps where corpus has insufficient evidence

**Output:** `research-package.md` — compressed evidence package (quotes + context,
not full documents). Target 10:1 compression from source material.

**Hard bans:**
- Never invent quotes or citations
- Never paraphrase without marking as paraphrase
- Every source must include file path
- Never include full documents — compress to relevant extracts

**Model:** sonnet

**Definition of done:** Every blueprint section has 2+ primary sources. All file paths
verified. Gaps explicitly flagged.

---

#### 2b. DOMAIN EXPERT

**Role:** Theoretical analyst. Provides domain-specific intellectual depth for each section.

**Input:** `blueprint.md` + `research-package.md` + `SOUL` (if provided)

**Process:**
1. Load the domain soul file if provided (ambient expertise profile)
2. For each section, write 300-600 words of domain-specific analysis:
   - What theoretical framework applies
   - How this connects to established literature
   - What is novel vs. what is derivative
   - Technical depth appropriate to the book's audience

**Output:** `domain-layer.md`

**Hard bans:**
- Never exceed 600 words per section
- Never force domain angle where it doesn't belong
- Never substitute jargon for explanation

**Model:** opus

---

#### 2c. CRITIC

**Role:** Power and assumptions analyst. Questions whose interests are served,
what assumptions go unexamined, what failure modes exist.

**Input:** `blueprint.md` + `research-package.md`

**Output:** `critical-layer.md` — for each section:
- Whose interests does this argument serve?
- What assumptions go unexamined?
- What would a hostile reviewer say?
- What failure modes does the proposed framework have?
- Historical parallels (when has this been tried before, what happened?)

**Hard bans:**
- Never praise
- Never accept assumptions without questioning
- Never let "it's just how things work" pass unchallenged

**Model:** opus

---

#### 2d. SCOUT

**Role:** Cross-reference scanner. Searches corpus for connections the other agents
might miss — adjacent fields, unexpected parallels, contradictions with other chapters.

**Input:** `blueprint.md` + `CORPUS` + `chapter-registry.jsonl` (if exists)

**Output:** `cross-refs.md` — connections found:
- Unexpected parallels from other corpus domains
- Contradictions with prior chapters (from registry)
- Terminology that conflicts with prior usage
- Resources in corpus that nobody asked for but are relevant

**Model:** sonnet

---

### Stage 3: AUDITOR

**Role:** Claims verification. Audits empirical claims, formal arguments, and
technical assertions against the research package and corpus.

**Input:** `blueprint.md` + `research-package.md` + `domain-layer.md`

**Output:** `audit.md` — for each section with claims:
- Claim → evidence mapping (supported / partially supported / unsupported)
- Formal consistency check (do the arguments hold logically?)
- Closest prior art + what's novel
- Connections to other project outputs (if applicable)

**Hard bans:**
- Do not evaluate writing quality — that's EDITOR's domain
- Do not fabricate evidence to fill gaps

**Model:** opus

---

### Stage 4: WRITER

**Role:** Synthesizer. Combines all specialist inputs into a readable chapter draft.

**Input:** `blueprint.md` + `research-package.md` + `domain-layer.md` +
`critical-layer.md` + `cross-refs.md` + `audit.md` + `VOICE`

**Process:**
1. Load `VOICE` profile as style constraints
2. For each section in the blueprint, synthesize all specialist inputs
3. Follow voice rules exactly — this is constraint satisfaction, not free generation
4. Address pressure tests identified by CRITIC
5. Include concrete examples for every section >500 words
6. Build one *set piece* per chapter — a vivid scene/example/argument a reader would retell

**Output:** `draft-v1.md` — complete chapter, 5,000-8,000 words

**Voice loading:** Read `VOICE` file completely. Extract:
- Signature moves (what this voice does)
- Anti-patterns (what this voice never does)
- Register (tone, formality level)
- Structural preferences (openings, transitions, endings)

**Hard bans:**
- Never ignore the voice profile
- No section >1,500 words without a concrete example
- No filler phrases from the anti-pattern list
- Max 2 block quotes per section
- Chapter openings: scene, provocation, or historical grounding — never thesis statements
- Chapter endings: image or restated stakes — never summary

**Model:** opus

---

### Stage 5: ADVERSARY

**Role:** Pressure tester. Attacks the draft.

**Input:** `draft-v1.md` + `critical-layer.md`

**Output:** `critique.md` containing:
1. **Argument Gaps** — unsupported claims, unjustified transitions, ignored counter-arguments
2. **Accessibility Failures** — jargon without explanation, assumed knowledge
3. **Structural Weaknesses** — sections that don't earn their place, redundancy
4. **Strongest Objection** — single most damaging critique a hostile intelligent reviewer would make
5. **Voice Breaks** — where the prose shifts from the author's voice to generic AI writing

**Hard bans:**
- Never praise
- Never suggest cosmetic changes when substance is the problem
- Never let weak arguments slide because prose is good
- Never repeat what CRITIC already said — go deeper

**Model:** opus

**User gate:** Review critique before WRITER revises. User may override, accept,
or redirect specific critiques.

---

### Stage 6: WRITER (Revision)

**Role:** Same WRITER agent, revises `draft-v1.md` addressing ADVERSARY critique
and user feedback.

**Input:** `draft-v1.md` + `critique.md` + user feedback

**Output:** `draft-v2.md`

**Model:** opus

---

### Stage 7: EDITOR

**Role:** Editorial polish. Final prose pass.

**Input:** `draft-v2.md` + `chapter-registry.jsonl` (if exists) + `VOICE`

**Output:**
- `editorial-report.md` — section-by-section diagnosis:
  - Strongest and weakest sentences (with before/after rewrites)
  - Structural analysis
  - Tone audit against `VOICE`
  - Grounding check (abstractions without examples)
  - Redundancy scan
  - Cross-chapter terminological consistency (if registry exists)
  - Top 5 priorities ranked by impact
- `final.md` — publication-ready chapter

**Book-specific checks:**
- No concept introduced without plain-language explanation on first appearance
- AI-writing patterns eliminated (em-dash overuse, "tapestry"/"landscape"/"navigate"/"delve",
  rule-of-three, inflated parallelism, "at its core", "it's important to note")
- Chapter reads as single consistent voice throughout

**Model:** sonnet (consistency checks) or opus (voice work)

---

### Stage 8: FACT-CHECKER

**Role:** Citation verifier and endnote generator.

**Input:** `final.md` + `research-package.md` + `CORPUS`

**Output:**
- `endnotes.md` — every empirical claim, direct quote, named study traced to
  publication with page/DOI. Format: `p. XX "catchphrase" Author, Title (Publisher, Year), pages.`
- `biblio-essay.md` — 2-4 paragraphs: intellectual debts, recommended reading,
  chapter's position in literature. Conversational, not academic.

**Verification rules:**
1. Every statistic cites primary source, not secondary report
2. Every direct quote includes page number or URL
3. Corpus file paths converted to publication citations
4. Unverifiable claims flagged `[VERIFY]`
5. Outdated statistics (>3 years for fast-moving fields) flagged for update

**Hard bans:**
- Never fabricate a citation
- Never guess a page number
- If source unavailable, say so explicitly

**Model:** sonnet

---

### Stage 9: PDF

**Role:** Deterministic output. Converts `final.md` to print-quality PDF.

**Process:**
```bash
# 1. Strip YAML frontmatter
python3 -c "
import re
with open('OUTPUT_DIR/ch{NN}-{slug}/final.md') as f:
    text = f.read()
text = re.sub(r'^---\n.*?\n---\n', '', text, count=1, flags=re.DOTALL)
with open('/tmp/ch-clean.md', 'w') as f:
    f.write(text)
"

# 2. Build PDF (6x9 trade paperback)
pandoc /tmp/ch-clean.md \
  -o OUTPUT_DIR/ch{NN}-{slug}/chapter.pdf \
  --pdf-engine=tectonic \
  --template=TEMPLATE \
  -V title="Chapter Title"

# 3. Verify
pandoc /tmp/ch-clean.md -o /tmp/debug.tex --template=TEMPLATE -V title="..." 2>&1 | grep -i overfull
```

**Output:** `chapter.pdf`

**Verification:** Zero `Overfull \hbox` warnings. Visual check: title page, first table,
first code block, infographic, final page.

**Template:** Uses the ebook skill's battle-tested LaTeX template (6x9, Georgia, fvextra
line wrapping, scriptsize tables, pandocbounded images). See ebook skill for full template.

---

### Stage 10: REGISTRY

**Role:** Cross-chapter memory. Appends entry to `chapter-registry.jsonl`.

**Output:** One JSONL line:
```json
{
  "chapter": N,
  "title": "Chapter Title",
  "slug": "chapter-slug",
  "key_terms_introduced": ["term1", "term2"],
  "key_arguments": ["argument1", "argument2"],
  "word_count": 6500,
  "sections": ["Section 1", "Section 2"],
  "date_completed": "2026-03-20",
  "status": "final"
}
```

This registry is loaded by SCOUT and EDITOR on subsequent chapters to maintain
consistency and prevent redundancy.

---

## Agent Invocation Pattern

Each agent is launched via the Agent tool with its full role card injected:

```
Agent(
  prompt: """
  You are {AGENT_NAME}.

  {Full agent definition from above: role, input, output, hard bans}

  ## Chapter Context
  - Chapter {N}: {title}
  - Outline entry: {pasted from OUTLINE}

  ## Your Inputs
  {Artifact content injected — agents do NOT search for their own context}

  ## Your Task
  Produce {output artifact} following your definition exactly.
  Write to: {OUTPUT_DIR}/ch{NN}-{slug}/{artifact}.md
  """,
  model: {specified model}
)
```

## Model Selection

| Agent | Model | Rationale |
|-------|-------|-----------|
| ARCHITECT | opus | Structural judgment, argument design |
| RESEARCHER | sonnet | Fast structured retrieval from corpus |
| DOMAIN EXPERT | opus | Nuanced theoretical writing |
| CRITIC | opus | Power analysis, assumption testing |
| SCOUT | sonnet | Fast cross-reference scanning |
| AUDITOR | opus | Formal verification, claims mapping |
| WRITER | opus | Synthesis, voice fidelity, prose quality |
| ADVERSARY | opus | Rigorous critique |
| EDITOR | sonnet | Pattern detection, consistency checks |
| FACT-CHECKER | sonnet | Structured extraction, citation format |

## Handoff Format

Every artifact file begins with YAML frontmatter:

```yaml
---
chapter: {N}
title: "{chapter title}"
agent: {AGENT_NAME}
version: 1
depends_on:
  - {input artifacts}
timestamp: {YYYY-MM-DD}
status: draft | review | final
---
```

## Conductor Responsibilities

1. **Context injection.** Load the right outline section, corpus excerpts, and prior
   artifacts for each agent. Agents should NOT search for their own context — the
   conductor curates and injects. Target 10:1 compression ratio.

2. **Parallelism.** Stage 2 agents (RESEARCHER, DOMAIN EXPERT, CRITIC, SCOUT)
   launch as concurrent Agent tool calls.

3. **User gates.** Pause after ARCHITECT (blueprint approval) and ADVERSARY
   (critique review). User may restructure, override, or redirect.

4. **Quality enforcement.** If an agent's output violates its hard bans, reject
   and re-run with the specific violation cited.

5. **Cross-chapter awareness.** Load `chapter-registry.jsonl` entries from prior
   chapters. Pass to SCOUT and EDITOR. Prevents redundancy and terminology drift.

6. **Compression discipline.** Research packages: quotes + context, not full documents.
   Domain layers: 300-600 words/section. Critical layers: 200-500 words/section.
   The WRITER receives all inputs — compress upstream to keep writer context manageable.

7. **Resume capability.** Check `OUTPUT_DIR/ch{NN}-{slug}/` for existing artifacts.
   Resume from the last completed stage rather than restarting.

## Configuration Examples

### Technical monograph (default)
```
/book-harness outline.md --corpus ~/research/ --voice VOICE.md
```

### Academic textbook with domain soul
```
/book-harness outline.md --corpus ~/research/ --voice academic-voice.md --soul ~/souls/economics.md
```

### Essay collection (shorter chapters)
```
/book-harness outline.md --corpus ~/notes/ --voice essay-voice.md
```
Word count targets adjust based on outline section count and depth.

## LaTeX Template

The skill uses the ebook pipeline's proven 6x9 trade paperback template. Key specs:
- 6x9 inch trim, 4.5in content width
- 11pt Georgia (fallback: TeX Gyre Termes), 1.25 line spacing
- fvextra for code block line wrapping
- scriptsize longtables for narrow pages
- pandocbounded for Pandoc 3.x image compatibility
- Running headers (book title left, chapter title right)

See the `ebook` skill for full template source. Store at `{project}/assets/book-template.tex`.

## Metrics

After pipeline completion, report:
- Total word count
- Agent invocations (count, model breakdown)
- Compression ratio (corpus tokens read → research package tokens)
- Fact-checker pass rate (verified / flagged / unverifiable)
- Voice compliance (anti-patterns detected by EDITOR)
- Overfull warnings in PDF (should be 0)
