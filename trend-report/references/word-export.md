# Word Template Export

Use this reference when generating `.docx` outputs from the assembled markdown report.

## Default template location

`assets/report-template.docx`

Replace this path if the user supplies a template elsewhere.

## Export command

```bash
pandoc --standalone --from markdown+yaml_metadata_block outputs/<report-name>.md \
  -o outputs/<report-name>.docx \
  --reference-doc assets/report-template.docx
```

## Fallback command (no template provided)

```bash
pandoc --standalone --from markdown+yaml_metadata_block outputs/<report-name>.md -o outputs/<report-name>.docx
```

## Checks

- Validate the template file before export: `unzip -t assets/report-template.docx`
- Validate generated docx before sharing: `unzip -t outputs/<report-name>.docx`

- Confirm heading styles are mapped correctly in the generated Word file.
- Confirm tables from sections 3 and A6 keep readable formatting.
- Confirm appendix headings remain in final document order.

## Pre-export quality checks (markdown)

Run these checks before creating `.docx`:

```bash
# Find list starts that are not preceded by a blank line
awk 'NR>1 && ($0 ~ /^([[:space:]]*-[[:space:]]|[[:space:]]*[0-9]+\\.[[:space:]])/) && (prev !~ /^([[:space:]]*-[[:space:]]|[[:space:]]*[0-9]+\\.[[:space:]])/) && (prev !~ /^[[:space:]]*$/) {print NR ":" $0} {prev=$0}' outputs/<report-name>.md

# Find unlinked parenthetical citations (potentially missing URLs)
grep -nE "\\([A-Za-z][^)]{2,}\\)" outputs/<report-name>.md

# Find linked citations that are missing outer parentheses
grep -nE "(^|[^\\(])\\[[^]]+\\]\\(https?://[^)]+\\)" outputs/<report-name>.md
```

Expected result: no matches for the list check. Citation matches should be manually reviewed and converted to `([Source](https://...))` where they are evidence citations.
