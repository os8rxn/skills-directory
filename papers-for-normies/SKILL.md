---
name: papers-for-normies
description: "Explain and critically assess academic papers in plain English. Use when a reader wants to understand a research paper's idea, method, evidence, and practical impact without specialist knowledge."
---

# Papers for Normies

Explain the paper so the reader can understand the idea and judge the evidence.

Use the supplied paper URL, PDF, or text. Ask for it if missing. Read the full paper, including relevant figures, tables, and supporting appendices. If access is incomplete, state what you read and limit your conclusions.

Start with the paper title and source link when available. Use four sections:

- **Idea:** Lead with the main takeaway. What problem does it address? What does it contribute?
- **Method:** Explain how it works in a few steps. Always add a mind branch diagram (see Diagram below), or a system diagram if the method is a pipeline or loop. Use a simple analogy that a first-year college student can understand to break the concept down; preserve important limits.
- **Evidence:** Does the evidence support the main claim? Give key results and reported numbers. Check comparisons, test conditions, and uncertainty when relevant; assess proof assumptions for theoretical work. Cite pages, sections, or figures for substantive claims. Separate reported results, author claims, and your interpretation. Identify material limitations or missing checks without inventing faults.
- **Impact:** Explain the significance and one open question. Give a practical implication if supported; label proposed uses as speculative.

## Diagram

A mind branch diagram is a left-to-right tree that maps the whole paper on one page, from problem to bottom line, not only the method. Build it like this:

- **Root:** "Summary of [Paper title] by [Authors]". It sits at the far left.
- **Main branches (5 to 7):** Labels of 2 to 6 words in title case. The first branch is the core problem. The middle branches follow the method in order; number them ("1.", "2.") when order matters. The last branch is the bottom line.
- **Sub-branches (2 to 4 per main branch):** Short labels for the parts of each step. A label can be a short formula: "Input → Encoder → Score".
- **Leaves (2 to 4 per sub-branch):** One full plain sentence each, under 20 words. Add a concrete example in "(e.g., ...)" when it helps.
- **Lists:** For 3 to 6 parallel items (stages, components, metrics), add one more level of very short leaves. Use "Term: definition" for definitions.
- **Depth:** 4 levels at most, counting the root. Lists may add a 5th.
- **Notation:** "→" for sequences and cause and effect. "=" for definitions. Follow the paper's own structure and terms.

Render it as an indented text tree in a code block:

```
Summary of [Paper title] by [Authors]
├─ Core Problem
│  └─ [Sub-branch]
│     ├─ [Leaf sentence]
│     └─ [Leaf sentence]
├─ 1. [First step]
│  ├─ [Sub-branch]
│  │  └─ [Leaf sentence (e.g., ...)]
│  └─ [Sub-branch]
│     └─ [Leaf sentence]
└─ Bottom Line
   └─ [One-sentence takeaway]
```

The diagram does not count toward the word limit.

## Style

Use Samir comms style: lead with the main point. Write short sentences with plain words, consistent terms, and active voice. Define necessary jargon. Merge overlapping points. Use bullets for lists and tables for comparisons. Be direct, respectful, and warm. No filler or em dashes.

Aim for under 400 words unless the user requests more detail. Preserve caveats and technical detail that change the conclusion, even if this needs more words. Do not invent facts, citations, or time savings. Return only the analysis.
