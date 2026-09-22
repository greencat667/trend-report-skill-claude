# Interview Insights Extraction

> **Utility prompt** | Variable length | Use when processing interview transcripts

## Prompt

Extract **key insights from this transcript** as they relate to the **{{TOPIC}}** trend report.

### Input

Paste the interview transcript below the instructions.

### Output Structure

Organise insights under these headings:

#### 1. [Theme heading] (choose based on content)
Examples: "Erosion of trust", "Climate-nature narrative gap", "Grassroots disinformation dynamics"

- 3-5 bullet points per theme
- Each bullet: 20-40 words, capturing a specific insight
- Bold key phrases

#### 2. [Next theme]...
Continue for 4-6 themes total.

#### Implications for the trend project
- 4-6 bullets connecting interview insights to the report
- Reference specific sections where insights could be used (e.g. "Confirms a grassroots disinformation frontier for Section 2")

### Style Rules

- Write as a colleague, not a consultant. First person plural ("we", "our").
- Plain, direct English. Short sentences (target under 15 words).
- Interpretive, not transcription — synthesise, don't summarise
- Use the interviewee's concepts and language where vivid
- Note tensions or contradictions
- Connect to existing report framework (signals, drivers, SWOT, scenarios)

## Output Format

```markdown
# Key Insights from [Interview Title/Date]

## 1 · [Theme heading]
- **[Key phrase]** — [Insight explanation]
- [Additional bullet]
- [etc.]

## 2 · [Theme heading]
- [Bullets...]

[Continue for all themes]

---

## Implications for the {{TOPIC}} project
- [Implication 1]
- [Implication 2]
- [etc.]

---

*Would you like me to turn these into a short "Interview Insights" annex formatted for the report?*
```

---

*After extraction, offer to format as a report annex.*
