---
name: paper-to-gist
description: Turn any arXiv paper, blog post, or technical document into a layman-friendly blog-style gist with ASCII diagrams and fun analogies. Use when asked to explain a paper, create a deep dive, or gist-ify a technical topic.
---

# Paper to Gist

Transform dense technical papers/documents into engaging, layman-friendly markdown explainers with ASCII diagrams, fun analogies, and zero assumed knowledge. Publish as a public GitHub gist.

## Input

Ask the user for (or infer from context):

1. **Source URL** — arXiv link, blog post, GitHub README, or PDF path
2. **Analogy theme** (optional) — e.g. "Dragon Ball Z", "cooking", "sports", "Star Wars". Default: pick something fitting based on the topic.
3. **Output path** (optional) — local `.md` file to save before gisting. Default: `<topic-slug>-deep-dive.md` in the working directory.

## Workflow

### Step 1: Fetch & Parse the Source

**Priority order for reading:**
1. If arXiv URL: download PDF via `curl -L -o paper.pdf <url>`, then parse with `lit parse paper.pdf`
2. If blog post / web URL: `webfetch` the page (markdown format)
3. If local PDF: `lit parse <path>`
4. If local `.md` / `.txt`: read directly

**LiteParse** (preferred for PDFs):
```bash
lit parse paper.pdf          # full text extraction with OCR fallback
lit parse paper.pdf --format markdown  # structured markdown output
```

Install if missing: `brew install llamaindex-liteparse`

### Step 2: Extract Key Information

From the parsed content, identify and extract:

- **Title and authors**
- **The core problem** — what gap/issue does this solve?
- **The proposed solution** — the key idea in one sentence
- **How it works** — the mechanism/algorithm (step by step)
- **Why it matters** — theoretical justification or intuition
- **Results** — benchmarks, numbers, comparisons
- **Ablations / tradeoffs** — what they tested, what matters most
- **Limitations** — what doesn't work, open questions
- **Code / repo links** — if available

**Do NOT summarize from memory.** Always verify against the actual source text.

### Step 3: Write the Deep Dive

Write a standalone `.md` file with this structure:

```markdown
# <Title> — The <Theme> Edition

> *"<Catchy one-liner that captures the core idea>"*

**Paper:** <URL>
**Code:** <GitHub URL if available>
**Authors:** <Authors / Team>
**Date:** <Publication date>

---

## The Problem (<Theme> Analogy)

<Introduce the problem with the chosen analogy. Explain what exists today and why it's broken/suboptimal. Use ASCII diagrams to illustrate.>

<ASCII diagram showing the current approach and its flaws>

---

## The Solution: <Key Idea Name>

<Explain the proposed fix using the same analogy. Show how it addresses the problem.>

<ASCII diagram comparing old vs new approach>

### How It Works

<Step-by-step explanation with ASCII diagrams for each major component.>

---

## Why This Matters

<Formal argument / theoretical insight, still in layman terms.>

<ASCII diagram illustrating the key insight>

---

## Results: The Numbers

<Benchmark table with gains. Include emoji indicators for impact level.>

---

## Ablations: What They Tested

<Component-by-component breakdown of what matters.>

---

## TL;DR

<Boxed summary with all key takeaways, results, and the core insight.>

---

*Paper: [arXiv link](URL) | Code: [GitHub](URL) | Date*
```

### Writing Style Rules

- **Zero assumed knowledge.** Explain every term the first time it appears.
- **Fun analogies throughout.** Pick one theme and stick with it. Every section should reference the analogy.
- **ASCII diagrams for EVERY major concept.** Use box-drawing characters (╔═╗║╚╝│─┌┐└┘├┤┬┴┼). No images.
- **Emojis for visual hierarchy** — use them sparingly for impact, not decoration.
- **No filler.** Every paragraph teaches something. Cut ruthlessly.
- **Code/math blocks** for formulas — keep them simple, annotate each term.
- **Tables** for comparisons and results — use ASCII box tables.
- **Self-contained.** The gist should be understandable without reading the original paper.

### ASCII Diagram Guidelines

```
    ╔══════════════════════════════════════════╗
    ║  TITLE IN CAPS                           ║
    ╠══════════════════════════════════════════╣
    ║                                          ║
    ║  Visual content here                     ║
    ║  Use arrows (→ ← ↑ ↓ ↔), boxes,         ║
    ║  and tree structures                      ║
    ║                                          ║
    ╚══════════════════════════════════════════╝
```

Keep diagrams under 60 chars wide. Test by reading in a terminal.

### Analogy Theme Examples

| Topic Type | Good Themes |
|---|---|
| Neural network architecture | Dragon Ball Z, Pokemon battles, cooking recipes |
| Systems / infrastructure | City planning, traffic, logistics |
| Cryptography | Spy movies, locked boxes, secret messages |
| Distributed systems | Restaurant kitchen, orchestra, sports team |
| Optimization | Training montage, diet plan, budget planning |

### Step 4: Save & Gist

1. Save the `.md` file locally
2. Create a public gist:

```bash
gh gist create <file.md> --public --desc "<Title> - <Paper URL> explained in layman terms"
```

3. Return the gist URL to the user.

## Quality Checklist

Before finalizing, verify:

- [ ] Every technical term is explained on first use
- [ ] At least one ASCII diagram per major section
- [ ] The analogy is consistent throughout (not dropped midway)
- [ ] Results/benchmarks are included with actual numbers from the paper
- [ ] The TL;DR captures all key points
- [ ] All claims are verified against the source (not from memory)
- [ ] The file renders well in a terminal (lines under 80 chars)
- [ ] No markdown rendering issues (tables aligned, boxes closed)
