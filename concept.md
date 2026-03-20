# Claw4S Submission: The Technical Book Harness

## Opportunity

- **Conference:** Claw4S (Stanford + Princeton) — "Submit skills, not papers"
- **Deadline:** April 5, 2026
- **Prize:** $50,200 pool, up to 364 winners ($5K grand)
- **Submit:** SKILL.md (executable) + Research Note (1-4pp LaTeX)
- **Judging:** Auto-execution (does it run?) → Agent review (5 criteria) → Human meta-review
- **Requirement:** Claw as co-author
- **Site:** https://claw4s.github.io/
- **Submissions:** clawrxiv.io

---

## Qualification

| Dimension | Score | Rationale |
|-----------|:-----:|-----------|
| Domain fit | 5 | Harness engineering IS the conference's thesis. We submit executable science — they want executable science. |
| Stage match | 5 | Production-tested. Endosphere pipeline has produced chapters. Ebook pipeline has produced PDFs. Resource scanner indexes 2,300+ files. |
| Buildability | 5 | Packaging existing skills, not building from scratch. All components exist and work. |
| Value/effort | 4 | $5K grand + category prizes. ~2-3 days to package + write research note. Good ROI. |
| Strategic value | 5 | Stanford/Princeton academic validation. Positions us as harness engineering practitioners with published methodology. Directly feeds book credibility + consulting pipeline. |
| **Total** | **24/25** | **QUALIFY** |

---

## The Skill: `book-harness`

### Thesis

Most AI-assisted writing produces slop because it treats the model as a single-turn text generator. The book harness treats book production as a multi-agent manufacturing pipeline with specialized roles, adversarial quality gates, structured memory, and deterministic output (PDF). The skill takes a book outline + a research corpus and produces publication-ready chapters through a 10-stage agent orchestra with 3 user gates.

### What Makes This Novel (for Claw4S)

1. **Multi-agent orchestration as methodology.** Not one agent writing a book — 10 agents with distinct cognitive roles (architect, researcher, philosopher, critic, editor, fact-checker) producing artifacts that flow through a DAG.
2. **Adversarial verification built into the pipeline.** ADVERSARY pressure-tests every chapter. ANARCHIST can halt the pipeline. Quality is structural, not optional.
3. **Resource corpus as grounded evidence.** The skill scans a structured resource library (~2,300 files, 37 categories) using domain-specific "souls" (ambient expertise profiles). Research is grounded in real sources with file paths and page numbers — not hallucinated citations.
4. **Deterministic output.** The pipeline terminates in pandoc + tectonic LaTeX → trade paperback PDF. Reproducible, verifiable, physical artifact.
5. **Voice as injected system.** Author voice is not "write like X" — it's a loaded profile (VOICE.md) with specific anti-patterns, registers, and signature moves. The writer agent doesn't generate style; it follows a constraint system.

### Pipeline Architecture (Adapted from Endosphere)

```
INPUT: outline.md + ~/Desktop/2_resources/ (corpus)
                    │
    ┌───────────────┤
    │               │
ARCHITECT ──────► blueprint.md (chapter as control system)
    │               │
    │    ┌──────────┼──────────┬──────────┐
    │    │          │          │          │
    │ RESEARCHER  DOMAIN    CRITIC    SCOUT
    │ (evidence)  EXPERT   (power    (cross-ref
    │             (theory)  analysis)  corpus)
    │    │          │          │          │
    │    └──────────┼──────────┴──────────┘
    │               │
    │    AUDITOR ───► audit.md (claims verification)
    │               │
    │    WRITER ────► draft-v1.md (synthesize all inputs)
    │               │         [USER GATE 1]
    │    ADVERSARY ─► critique.md (pressure test)
    │               │         [USER GATE 2]
    │    WRITER ────► draft-v2.md (revision)
    │               │
    │    EDITOR ────► final.md + editorial-report.md
    │               │
    │    FACT-CHECK ► endnotes.md + biblio-essay.md
    │               │
    │    EBOOK ─────► chapter.pdf (pandoc + tectonic)
    │               │
    └───────────────┘
OUTPUT: Publication-ready PDF with endnotes
```

### Generalization from Endosphere

The endosphere pipeline is book-specific (Sloterdijk, thermorealism, sovereignty). The Claw4S submission generalizes it:

| Endosphere-specific | Generalized book-harness |
|---------------------|------------------------|
| PETER (topologist) | DOMAIN EXPERT (loads relevant soul from corpus) |
| GAIA (nonhuman voice) | Removed — domain-specific |
| ANARCHIST (sovereignty) | CRITIC (configurable critical lens) |
| CLAUDE (co-creator) | Removed — book-specific |
| ECOFRONTIERS (economics) | AUDITOR (claims verification against corpus) |
| Fixed voice profile | Configurable VOICE.md |
| Endosphere outline | Any outline.md |
| Hardcoded resource paths | Configurable corpus directory |

### Evaluation Criteria Mapping

| Criterion | Weight | How we score |
|-----------|--------|-------------|
| **Executability** | 25% | Skill runs end-to-end: outline → PDF. All tools are standard (pandoc, tectonic, Claude Code Agent tool). No external APIs. |
| **Reproducibility** | 25% | Deterministic pipeline. Same outline + corpus → same structure. PDF output is verifiable artifact. JSONL chapter registry tracks state. |
| **Scientific rigor** | 20% | Grounded in harness engineering literature (Guo 2026, Anthropic context engineering, OpenAI agent patterns). Adversarial verification prevents hallucination. Fact-checker traces every claim. |
| **Generalizability** | 15% | Works for ANY technical book, not just ours. Configurable: swap outline, corpus, voice profile, domain expert soul. Tested on two distinct book projects (Endosphere + Green Crypto Handbook). |
| **Clarity for agents** | 15% | Skill IS a Claude Code skill — written in the format agents already execute. Step definitions, commands, expected outputs are native. |

---

## Deliverables

### 1. SKILL.md
The executable book harness skill. A cold agent reads it, gets an outline + corpus path, and produces a chapter PDF.

### 2. Research Note (1-4pp LaTeX)
**Title:** "The Book Harness: Multi-Agent Orchestration for Technical Book Production"
**Authors:** Claw, Patrick Rawson, Claude

**Structure:**
1. **Motivation:** Books are the last creative artifact still produced by single-agent workflows. The book harness applies manufacturing pipeline principles — specialization, adversarial QA, structured handoff — to long-form technical writing.
2. **Design:** 10-stage pipeline with parallel phase, 3 user gates, 4 memory types (episodic/semantic/procedural/working), configurable domain expertise via soul files, deterministic PDF output.
3. **Results:** Production-tested on 2 book projects. Quantify: agent count, stages, artifacts per chapter, compression ratios (10:1 from corpus to researcher output), citation accuracy (fact-checker pass rate), word count targets hit.
4. **Related work:** Guo (2026) "Emerging Harness Engineering Playbook" — architecture as guardrails, AGENTS.md as feedback loop. Anthropic (2026) "Effective Context Engineering" — progressive disclosure, cache optimization. CommonGround (2026) — multi-agent coordination protocols.

---

## Components Reused

| Component | Source | Adaptation |
|-----------|--------|------------|
| 10-agent pipeline | `/endosphere/AGENTS.md` | Generalize roles, remove book-specific agents |
| PDF production | `/ebook/SKILL.md` | Use as final stage — pandoc + tectonic |
| Resource scanning | `~/2_resources/RESOURCES.md` | Corpus ingestion for RESEARCHER agent |
| Domain souls | `~/2_resources/*/SOUL.md` | Configurable expertise loading |
| Voice profile | `/distribution/VOICE.md` | Make swappable |
| One-shot PRD pattern | `/oneshot/SKILL.md` | Blueprint structure for ARCHITECT |
| Infographic generation | `/infographic/SKILL.md` | Optional: chapter figures |

---

## Action Plan

| Step | Task | Effort | Owner |
|------|------|--------|-------|
| 1 | Write generalized SKILL.md (adapt endosphere + ebook) | 1 day | Claude + Pat |
| 2 | Test execution: run skill on a sample chapter | 0.5 day | Claude |
| 3 | Write LaTeX research note (1-4pp) | 0.5 day | Claude + Pat |
| 4 | Download LaTeX template from claw4s.github.io | 10 min | Pat |
| 5 | Run humanizer on research note | 30 min | Claude |
| 6 | Submit via clawrxiv.io / OpenClaw | 30 min | Pat |

**Total effort:** ~2-3 days
**Deadline:** April 5, 2026 (16 days)

---

## Strategic Value Beyond Prize Money

1. **Stanford/Princeton validation** of our harness engineering methodology
2. **Published at clawrxiv.io** — citable reference for consulting + book credibility
3. **The skill itself is reusable** — we use it for Endosphere AND Green Crypto Handbook
4. **Positions Ecofrontiers as harness engineering practitioners**, not just users
5. **Distribution asset** — blog post, X thread, LinkedIn article about "how we produce books with 10 AI agents"
6. **Feeds T&F book credibility** — academic conference acceptance for the methodology behind the book
