# Trend Report

A skill for [Claude Code](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview) and [Cowork](https://claude.ai) that researches and drafts a structured, 14-section trend/horizon-scan report from a reusable prompt pack, then exports it to both Markdown and Word (`.docx`).

## What it does

Give it a topic, scope, and audience, and it will:

1. Set up a project file (`PROJECT.md`) capturing scope, key questions, your organisation's strategic pillars, and source materials
2. Draft the four detailed appendix sections (signals & drivers, systems context, SWOT detail, three horizons) in parallel, each through a draft → critique → rewrite loop
3. Draft the synthesis sections in a fixed dependency order (lead scenario → other scenarios → second-order effects → learning priorities → trend snapshot → signals summary → SWOT summary → executive summary written last)
4. Assemble the final report and export it to a branded `.docx` via pandoc, with citation and hyperlink validation built into the process

The prompt pack enforces a consistent voice throughout: first-person plural, plain English, no unlinked citations, no consultant-speak — with before/after examples in each prompt file showing exactly what to avoid.

See `examples/community-tool-libraries-project.md` for a completed project file, and `examples/community-tool-libraries-report.md` for the report the skill produced from it, including 61 linked citations.

## Installation

**Ask Claude to set it up for you.** If you're using Claude Code or Claude Cowork, you can just say something like *"install the trend-report skill from github.com/greencat667/trend-report-skill-claude"* and Claude will clone the repo and put it in the right place — you don't need to do this by hand.

Or do it yourself: copy the `trend-report/` folder into your project's `.claude/skills/` directory:

```bash
git clone https://github.com/greencat667/trend-report-skill-claude.git
cp -r trend-report-skill-claude/trend-report/ your-project/.claude/skills/trend-report/
```

Claude will pick it up automatically from `available_skills` next time you start a session.

## Example prompt

Once installed, just ask Claude something like:

> "Can you write a trend report on the growth of four-day work week pilots? Our strategic pillars are advocacy, impact, engagement, and sustainability — primary audience is our programme staff."

## A note on the reference template

`assets/report-template.docx` ships as a **genuinely blank** pandoc reference template — page setup, header, and named styles (`Title`, `Heading1`, `Heading2`, table styles), no body content and no organisation branding. Swap in your own organisation's branded template (any `.docx` with matching style names works) before using this for real reports.

## Repository structure

```
trend-report-skill-claude/
├── trend-report/
│   ├── SKILL.md                       # Copy this folder to .claude/skills/
│   ├── PROJECT.md                     # Reusable project brief template
│   ├── prompts/                       # Section-by-section prompt pack (14 files)
│   ├── assets/
│   │   └── report-template.docx       # Blank pandoc reference template
│   ├── examples/
│   │   ├── community-tool-libraries-project.md  # Completed project file (the input)
│   │   └── community-tool-libraries-report.md   # The full report it produced (the output)
│   └── references/
│       └── word-export.md             # Export quality checks
├── README.md
├── CONTRIBUTING.md
└── LICENSE
```

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) — note this repo isn't actively maintained, so response times on issues and PRs will be slow to nonexistent.

## License

MIT — see [LICENSE](LICENSE).
