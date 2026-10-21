---
name: gdoc-export
description: Standard formatting rules for Google Doc exports. Use when creating or updating any Google Doc for Samir. Follows the 2-page rule and specific typography.
---

# Google Doc Export Format

## Reference Documents
- Style: always match the 2-page memo guide: read "Two Pages to Rule Them All" before writing — https://docs.google.com/document/d/1c87m837Axzp6pMj2B3eM3scAvNDn-QQNI7PFJEo6Vik/edit?tab=t.0

## Typography

| Element | Font | Size | Style |
|---------|------|------|-------|
| Title | Arial | 18pt | Bold |
| Heading 1 | Arial | 14pt | Bold |
| Heading 2 | Arial | 12pt | Bold |
| Body | Arial | 10pt | Normal |
| Captions | Arial | 9pt | Italic |

## The 2-Page Rule
- Main content: max 2 pages
- Everything else goes in Appendix
- If you can't fit it in 2 pages, you haven't distilled it enough
- Full reasoning and writing principles: see the 2-page memo guide above

## Formatting Rules

### Spacing
- Single line spacing within paragraphs
- One blank line between sections
- NO extra blank lines between heading and first paragraph
- NO double spacing anywhere

### Bullet Points
- Use standard bullets (not dashes)
- Proper indentation for sub-bullets
- Bold the first phrase if it's a label: **Key finding:** the rest of the text
- No period at end of bullets unless full sentence

### Tables
- Bordered (light gray borders)
- Header row: bold, light gray background
- No merged cells
- Consistent column widths
- Numbers right-aligned
- ALWAYS use `insertTable` via the Docs API to create real table elements. Never fake a table with spaced or tab-separated plain text — it looks wrong and cannot be styled.

### Numbers and Metrics
- Bold all key metrics
- Include units (%, $, IDR, km)
- Use consistent decimal places (1 decimal for %, 0 for counts)

## Document Structure (Standard)

```
Title
[Optional: PIC | Status | Date]

Executive Summary
- 3-5 bullet points 
- Most important and foundational takeaways only

Section 1: Context/Background
Section 2: Problem
Section 3: Observations - This section should consist of data, numbers quantification of claims, sources. 
Section 4: Core hypothesis -i.e what is this document trying to prove/disprove. If it is not trying to prove and disprove anything, put it in the exec summary. 
Section 5: Recommendations/Next Steps - this should be actionable by the reader

Appendix
- A. Data Tables
- B. SQL Queries
- C. Screenshots/Supporting Evidence
```

## Common Mistakes to Avoid
1. Using .md formatting in Google Docs (no `**bold**`, use actual bold)
2. Forgetting to remove line numbers from code
3. Tables that don't render properly
4. Inconsistent heading sizes
5. Too much white space between sections

## Pre-Export Checklist
- [ ] All fonts are Arial
- [ ] Heading sizes follow the table above
- [ ] Body text is 10pt
- [ ] 2-page rule respected
- [ ] Tables are properly bordered
- [ ] No orphan headings (heading at bottom of page, content on next)
- [ ] Key numbers are bold
- [ ] No jargon (use samir-comms skill)

## Post-Write Verification (REQUIRED)

After writing content, read the doc back via the Docs API and verify all of the following:

- Headings are bold and the correct size (not just tagged with `namedStyleType`)
- Body text is explicit 10pt Arial, not inherited
- Every section that should be a table is a `table` element in the response body — not a paragraph with spaced text
- Table header rows have bold text and a gray background

Known pitfalls:
- `namedStyleType: HEADING_1` does NOT auto-bold text runs. Bold and font size must be set separately via `updateTextStyle` on the same range.
- If you inserted a table as plain text paragraphs instead of via `insertTable`, it will look like a table in your head but render as unformatted lines in the doc. Always confirm `table` elements exist in the API response.
