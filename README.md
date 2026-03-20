# Book Harness

Multi-agent technical book production pipeline. 10-stage agent orchestra that takes a book outline and research corpus and produces publication-ready PDF chapters.

Submitted to [Claw4S Conference 2026](https://claw4s.github.io/) (Stanford + Princeton).

**Authors:** Claw, Patrick Rawson ([Ecofrontiers](https://ecofrontiers.xyz)), Claude (Anthropic)

## What it does

- **ARCHITECT** designs chapter structure as argument systems
- **RESEARCHER** searches a corpus for evidence (10:1 compression)
- **DOMAIN EXPERT** provides theoretical depth via loadable expertise profiles
- **CRITIC** questions assumptions and failure modes
- **WRITER** synthesizes all inputs under a loaded voice profile
- **ADVERSARY** pressure-tests the draft
- **EDITOR** catches voice drift, AI-writing artifacts, cross-chapter inconsistency
- **FACT-CHECKER** traces every claim to a source with page numbers
- **PDF** renders via pandoc + tectonic to 6x9 trade paperback

See [SKILL.md](SKILL.md) for the full executable pipeline. See [research-note.pdf](research-note.pdf) for the research note.

## Important disclaimer

This tool produces *drafts*, not finished books. AI-generated text requires human editorial review, fact-checking, and judgment before publication. Specifically:

- **AI agents hallucinate.** The FACT-CHECKER agent reduces but does not eliminate fabricated citations. Every claim, quote, and page number in the output must be verified by a human against primary sources before publication.
- **Voice profiles are approximations.** The WRITER agent follows style constraints, but the output is not equivalent to human authorship. A human author or editor must review for tone, accuracy, and appropriateness.
- **Domain expertise is simulated.** The DOMAIN EXPERT and AUDITOR agents provide structured analysis, but they cannot replace subject-matter expertise. Technical claims require review by qualified professionals.
- **The pipeline accelerates drafting, not publishing.** It is designed to get a human author from outline to reviewable draft faster. The human remains responsible for the final published text.

This tool was developed alongside two book projects. In both cases, every chapter produced by the pipeline undergoes full human editorial review before publication. The pipeline is one stage in a larger writing process that includes human co-authors, professional editors, and subject-matter review.

## License

MIT
