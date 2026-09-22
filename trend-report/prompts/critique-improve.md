# Section Critique & Improvement

> **Utility prompt** | Use after drafting each detailed section (A1, A2, A4, A5)

## Purpose

Before moving to synthesis, run each detailed section through a critique cycle to catch gaps, weak evidence, and unclear arguments.

## Prompt

Review the section below and provide:

### 1. Gaps & blind spots
What's missing from this analysis? Consider:
- Actors or perspectives not represented
- Signals or drivers that might be relevant but weren't mentioned
- Geographic or demographic blind spots
- Counter-arguments or alternative interpretations

### 2. Evidence quality (BLOCKING)
Which claims need stronger support? Flag:
- Assertions without citations
- **Assertions with unlinked citations — this is a BLOCKING issue.** Every citation must be a parenthesized linked markdown reference: `([Source](https://...))`. Unlinked source names (e.g. "Source: IPCC" or "according to the OECD") must be converted to linked format. A loose "Sources" or "References" list at the end of a section is not acceptable — every citation must be inline. **Do not produce the improved version until all citations are linked.**
- Outdated references (pre-2024)
- Single-source claims that should be triangulated
- Vague attributions ("some experts say...")

### 3. Clarity, voice & argument
Where could the writing be sharper? Note:
- Jargon that needs explanation
- Paragraphs that try to do too much
- Dense or abstract phrasing that can be simplified (e.g. "equity outcomes" → "who benefits", "regulatory framework" → "rules")
- Missing connections between points
- Unclear implications for {{ORG}}
- Any list formatting that may fail in docx export (inline lists, missing blank lines)
- **Sentences over 25 words** — flag and split
- **Third-person references to the organisation** ("the organisation should…") — rewrite as first person ("we should…")
- **Passive constructions** — rewrite in active voice
- **Consultant-speak** — flag and rewrite: "strategic positioning", "leverage", "ecosystem", "stakeholder alignment", "paradigm", "systemic implications", "cascading impacts", "multi-dimensional". Replace with plain English that says the same thing concretely.
- **Undefined technical terms** — flag any specialist term used without a brief definition on first use
- **Meta-text leaks** — flag and remove any "Word count:", "Sources:" dump sections, drafting notes, or other artefacts that should not appear in the final report

### 4. Consistency check
Does this section align with others? Consider:
- Do the signals/drivers match what's in the systems context?
- Does the SWOT reflect the three horizons trajectory?
- Are there contradictions between sections?

---

## After critique, produce:

**Improved version** — A revised section addressing the issues identified above.

Mark significant changes with `[REVISED]` so the human reviewer can spot them.

**Pre-flight checks before producing the improved version:**
1. ✅ Every citation is a parenthesized linked markdown reference: `([Source](https://...))`. No unlinked source names remain.
2. ✅ No loose "Sources" or "References" list at the end of the section. Every citation is inline.
3. ✅ No "Word count:", drafting notes, or meta-text artefacts remain.
4. ✅ No consultant-speak terms survive without being rewritten in plain English.
5. ✅ All technical terms are defined on first use.

If any of checks 1-3 fail, fix them before producing the improved version.

---

## When to use

Run this after drafting:
- A1 (Signals & Drivers detail)
- A2 (Systems Context)
- A4 (SWOT detail)
- A5 (Three Horizons)

The synthesis sections (scenarios, learning priorities) and summaries don't need this step — they're distillations, not original analysis.
