---
name: trend-report
description: >
  Research and draft structured exploration/trend reports from a reusable
  prompt pack, then export to both Markdown and Word (.docx). Use when asked
  to produce a trend report, exploration report, horizon scan, or structured
  analysis on a topic, especially when a specific Word template is required.
  Triggers on: "trend report", "exploration report", "horizon scan", "write a
  report on", "create a trend report for".
---

# Trend Report Skill (Cowork)

Generate structured trend reports using the prompt pack in this skill, then produce:
- A complete Markdown source report
- A Word (.docx) version styled using `assets/report-template.docx`

---

## Cowork Workflow

### Step 1 — Clarify before starting
Use `AskUserQuestion` to confirm:
- Topic and scope
- Organisation name — no default; always ask
- Primary audience (and whether learning priorities are for this audience specifically)
- **Strategic pillars** — the organisation's current strategic priorities that should frame all analysis. Ask the user to provide 3-5 pillars with short descriptions. There is no default set — every organisation's pillars are different; never assume a set from a previous project.
- Whether a custom Word template should be used (default: `assets/report-template.docx`)
- Author name and target date

### Step 2 — Set up project file
Copy `PROJECT.md` to the report's output folder and fill all `{{PLACEHOLDER}}` fields.

Output folder convention:
```
<workspace>/outputs/<report-name>/
```

Where `<workspace>` is the user's selected folder (e.g. `projects/urban-heat-trend-report`).

### Step 3 — Track progress with TodoWrite
Create a `TodoWrite` list at the start of every report run, covering the full drafting sequence below.

### Step 4 — Draft appendix sections in parallel

The four detailed appendix sections (A1, A2, A4, A5) are independent research tasks. Use the `Agent` tool to launch all four simultaneously in a **single message** (one Agent tool call per section). This cuts the research phase from four sequential passes to one parallel pass.

Each sub-agent needs:
1. The full content of the relevant prompt file from `prompts/` (paste it into the agent instructions)
2. Project context: topic, scope, org name, audience, output folder path
3. Instructions to use `WebSearch` and `WebFetch` to find and verify sources
4. A clear save path: `outputs/<report-name>/work/<section>-draft.md`

**Sub-agent prompt template (adapt per section):**
```
Draft [section name] for a trend report on [topic].
Organisation: [org name]. Audience: [audience]. Scope: [scope].
Output folder: [full path to outputs/<report-name>/work/]

Follow this prompt exactly:
[paste full content of the relevant prompts/ file here]

Citation rules: parenthesized Markdown links — ([Source Name](https://url)).
Save the completed draft to: [full path]/work/<section>-draft.md
```

**Launch all four in one message:**
- Agent 1 → A1 Signals & Drivers (`prompts/A1-signals-drivers-detail.md`)
- Agent 2 → A2 Systems Context (`prompts/A2-systems-context.md`)
- Agent 3 → A4 SWOT Detail (`prompts/A4-swot-detail.md`)
- Agent 4 → A5 Three Horizons (`prompts/A5-three-horizons.md`)

Wait for all four to complete before proceeding.

> **Note:** A3 (Other Scenarios) depends on the Lead Scenario (04) and is drafted later in sequence — do not include it here.

### Step 4b — Critique and rewrite in parallel

Once all four draft files exist, launch **four more parallel sub-agents** in a single message — one per appendix — each running the full critique loop:

1. Read `outputs/<report-name>/work/<section>-draft.md`
2. Read `prompts/critique-improve.md` and apply it to the draft
3. Save critique output to `outputs/<report-name>/work/<section>-critique.md`
4. Rewrite the draft incorporating critique findings
5. Save final version to `outputs/<report-name>/work/<section>-final.md`

Wait for all four to complete. Only `*-final.md` files are used in final assembly.

### Step 4c — Synthesis sections (sequential in main session)

Synthesis sections depend on appendix finals and must be drafted in order. Follow the traceable workflow below. Use the prompt files in `prompts/` exactly.

**A6 (Experts) and 07 (Methodology) are independent** — launch each as a sub-agent alongside the synthesis phase (alongside 04 or later) rather than waiting until the end.

### Step 5 — Pre-assembly clean (Required)

Before assembling the final Markdown, strip all internal artefacts from draft files. This catches critique markers, meta-text leaks, and loose source lists that should never appear in the published document.

```bash
for f in outputs/<report-name>/work/*-final.md outputs/<report-name>/*.md; do
  # Strip [REVISED — ...] markers left by the critique loop
  sed -i 's/ \[REVISED[^]]*\]//g' "$f"
  # Strip "Word count:" lines (case-insensitive)
  sed -i '/^[Ww]ord [Cc]ount:/d' "$f"
  # Strip standalone "Sources:" or "References:" dump headers and the lines that follow them
  # (inline citations are preserved — only end-of-section dumps are removed)
  sed -i '/^## *Sources *$/,/^## \|^# \|^$/{ /^## *Sources *$/d; /^[^#]/d; }' "$f"
  sed -i '/^## *References *$/,/^## \|^# \|^$/{ /^## *References *$/d; /^[^#]/d; }' "$f"
  # Strip lines that are just "Sources:" without a heading marker
  sed -i '/^Sources: *$/d' "$f"
  sed -i '/^References: *$/d' "$f"
done
```

Do not skip this step. Run it on both `work/*-final.md` files and any synthesis section files in the output folder. Then visually scan the assembled markdown for any surviving artefacts before export.

### Step 6 — Export to Word

Use **pandoc** with your organisation's reference template. Do **not** use the docx skill for this step — it does not preserve hyperlinks.

```bash
pandoc --standalone \
  --from markdown+yaml_metadata_block \
  outputs/<report-name>/<report-name>.md \
  -o outputs/<report-name>/<report-name>.docx \
  --reference-doc=assets/report-template.docx
```

See `references/word-export.md` for full export checks. Validate after generating:

```bash
python3 -c "
import zipfile
with zipfile.ZipFile('outputs/<report-name>/<report-name>.docx') as z:
    with z.open('word/document.xml') as f:
        n = f.read().count(b'<w:hyperlink')
    print(f'Hyperlinks: {n}')
"
```

Expect at least 20 hyperlink elements. If fewer, check citations in the assembled markdown before sharing.

### Step 7 — Share outputs
Use `present_files` to deliver:
- `outputs/<report-name>/<report-name>.md`
- `outputs/<report-name>/<report-name>.docx`

---

## Traceable Drafting Workflow (Required)

### Drafting order

**Phase 1 — Parallel (4 agents simultaneously):**
```
A1 ┐
A2 ├── draft in parallel → wait for all four
A4 ┤
A5 ┘
```

**Phase 2 — Parallel (4 agents simultaneously):**
```
A1 ┐
A2 ├── critique + rewrite in parallel → wait for all four
A4 ┤
A5 ┘
```

**Phase 3 — Sequential (main session) + 2 independent agents:**
```
04 → A3 → 05 → 06 → 01 → 02 → 03 → 00
```
Launch A6 and 07 as parallel sub-agents alongside Phase 3 (e.g. alongside 04).

### For detailed appendices (A1, A2, A4, A5) — keep three artefacts each:
```
outputs/<report-name>/work/<section>-draft.md
outputs/<report-name>/work/<section>-critique.md
outputs/<report-name>/work/<section>-final.md
```

Loop:
1. Draft section using the relevant prompt file.
2. Critique using `prompts/critique-improve.md`.
3. Rewrite using critique output — save as `-final.md`.
4. Use only `*-final.md` files in final assembly.

### Synthesis and summary sections (04, A3, 05, 06, 01, 02, 03, 00, A6, 07)
Save directly to `outputs/<report-name>/<section>.md`. No critique loop required.

---

## Document Scaffold (Required)

Add YAML front matter at the top of the assembled final Markdown:

```yaml
---
title: "<Full Title>"
subtitle: "<Subtitle>"
author: "<Author>"
date: "<Date>"
---
```

Heading hierarchy:
- Main sections (`Executive Summary`, `Trend Snapshot`, etc.) → `#` (Heading 1)
- First-level subsections within a section (e.g. `Why this matters now`, `Scenario A`, `Horizon 1`) → `##` (Heading 2)
- Second-level subsections (deeper nesting only) → `###` (Heading 3)
- Appendices → `# Appendix ...` (Heading 1)

**Important:** All sub-headings within a section or appendix default to `##` unless there is genuine nesting below them. Do not use `###` as the first level of subsection inside a `#` block — this renders as Heading 3 in Word and loses visual weight.

---

## Assembly Order (Publication Order)

```
00 Executive Summary
01 Trend Snapshot
02 Signals & Drivers Summary
03 SWOT Summary
04 Lead Scenario
05 Second-Order Effects
06 Learning Priorities
07 Methodology
A1 Signals & Drivers (Detailed) — includes weak signals
A2 Systems Context
A3 Other Scenarios
A4 SWOT Detail
A5 Three Horizons (expanded — H3 centrepiece)
A6 Experts & Actors
```

Save final assembled report to:
```
outputs/<report-name>/<report-name>.md
```

---

## Word Export (Cowork)

Use **pandoc** with your organisation's reference template. Do **not** use the docx skill for this step — it does not preserve hyperlinks.

Pre-export Markdown quality checks (run via Bash before export):

```bash
# Check for lists not preceded by a blank line
awk 'NR>1 && ($0 ~ /^([[:space:]]*-[[:space:]]|[[:space:]]*[0-9]+\.[[:space:]])/) && (prev !~ /^([[:space:]]*-[[:space:]]|[[:space:]]*[0-9]+\.[[:space:]])/) && (prev !~ /^[[:space:]]*$/) {print NR ":" $0} {prev=$0}' outputs/<report-name>/<report-name>.md

# Check for unlinked parenthetical citations
grep -nE "\([A-Za-z][^)]{2,}\)" outputs/<report-name>/<report-name>.md

# Count linked citations — must be 20 or more
grep -oE "\(\[[^\]]+\]\(https?://[^)]+\)\)" outputs/<report-name>/<report-name>.md | wc -l

# Check for meta-text artefacts that should have been stripped
grep -niE "^(Word [Cc]ount:|Sources: *$|References: *$|\[REVISED)" outputs/<report-name>/<report-name>.md
```

If the citation count is below 20, add citations before exporting.

Export command:

```bash
pandoc --standalone \
  --from markdown+yaml_metadata_block \
  outputs/<report-name>/<report-name>.md \
  -o outputs/<report-name>/<report-name>.docx \
  --reference-doc=assets/report-template.docx
```

If the template file is missing, export without `--reference-doc` and flag that styling is pending.

Validate hyperlinks after generating:

```python
import zipfile
with zipfile.ZipFile('outputs/<report-name>/<report-name>.docx') as z:
    with z.open('word/document.xml') as f:
        n = f.read().count(b'<w:hyperlink')
    print(f'Hyperlinks: {n}')
```

Expect at least 20 hyperlink elements. If fewer, check that citations use the format `([Source Name](https://url))` — outer parentheses are literal text, inner brackets and parens form the markdown link.

---

## Voice & Stance (Required)

- Write in first person plural ("we", "our", "us") as a member of the organisation, not a third-party analyst. Do not refer to your organisation in the third person ("the organisation should…") — rewrite as "we should…".
- Frame the report as a working document that invites disagreement, not a finished analysis that presents conclusions. The executive summary should include an explicit invitational sentence (e.g. "This report is intended to provoke discussion, not settle it").
- Prefer short, direct sentences (target: under 15 words average). Break compound sentences into two. No sentence should exceed 25 words without good reason.
- Choose everyday words over policy vocabulary: "use" not "leverage," "gap" not "deficit," "who benefits" not "equity outcomes," "rules" not "regulatory framework."
- Use question framing for section introductions where it fits naturally (e.g. "What can we learn?" rather than "Key findings"). Section *headings* themselves must stay consistent with the template.

---

## Drafting Rules

- Follow each file in `prompts/` exactly for structure, word budget, and style.
- Keep citations verifiable, current, and linked.
- Use `WebSearch` and `WebFetch` to find and verify sources.
- Pause for review at each prompt boundary.
- Run `prompts/critique-improve.md` after A1, A2, A4, and A5 before writing synthesis.
- Write executive summary (`00-exec-summary.md`) last.

---

## Citation Rules (Required)

- Use parenthesized Markdown links: `([Source Name](https://...))`.
- Do not use unlinked citations like `(Source Name)` in final outputs.
- Do not use bare links without parentheses.
- Include at least one linked citation per detailed appendix section (A1–A5) and per expert row in A6.

---

## Accessibility Rules (Required)

- Plain English, short sentences (target average under 15 words; no sentence over 25 words without good reason).
- Active voice throughout. Rewrite passive constructions.
- First person plural ("we", "our") — write as colleagues, not consultants.
- Explain technical terms on first use.
- Short paragraphs (2–3 sentences in most sections).
- Replace dense phrasing: "utilize" → "use", "leverage" → "use", "in order to" → "to", "with respect to" → "for", "equity outcomes" → "who benefits."
- Prefer questions to statements for section-opening sentences (not headings).

---

## Markdown Rendering Rules for DOCX (Required)

- Insert one blank line before every list.
- Keep each list item on its own line (`- ` or `1. `).
- Do not put list items on the same line as the lead-in sentence.
- Leave one blank line after every list before the next paragraph.
- Use hyphen bullets (`-`) and standard numbered lists, not inline numbering in prose.

---

## Required Inputs Per Project

- Topic and scope definition
- Primary audience
- Verifiable sources
- Organisation name and strategic context
- **Strategic pillars** with short descriptions (used to frame implications, SWOT, and learning priorities throughout the report)
- Template path (default: `assets/report-template.docx`)

---

## Output Contract

Produce both files for every completed report:
- `outputs/<report-name>/<report-name>.md`
- `outputs/<report-name>/<report-name>.docx`

When sharing completion with `present_files`, include:
- Both output file paths
- Whether template styling was applied
- Any sections that still need human evidence review

---

## Files in This Skill

| File | Purpose |
|------|---------|
| `SKILL.md` | Workflow and output contract (this file) |
| `PROJECT.md` | Reusable project brief template |
| `prompts/` | Section-by-section prompt pack |
| `assets/report-template.docx` | Word reference template (ships blank — swap in your own organisation's branded template, or replace this file directly) |
| `examples/` | Completed example project |
| `references/word-export.md` | Export quality checks |
