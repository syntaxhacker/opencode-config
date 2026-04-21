---
name: mermaid-diagram
description: Create Mermaid diagrams from descriptions and compile them to high-quality PNG images. Use when asked to create a diagram, flowchart, architecture diagram, branching strategy, or any visual representation using Mermaid.
---

# Mermaid Diagram Creator

Create Mermaid (.mmd) diagrams from natural language descriptions and compile them to high-quality PNG images using `@mermaid-js/mermaid-cli`.

## Input

Ask the user for (or infer from context):

1. **Diagram description** — what the diagram should represent (flowchart, sequence, architecture, branching strategy, etc.)
2. **Diagram type** (optional) — flowchart, sequence, class, state, ER, gantt, pie, mindmap, etc. Default: flowchart.
3. **Direction** (optional) — LR (left-to-right), TB (top-to-bottom), RL, BT. Default: infer from content.
4. **Output file name** (optional) — default: `<topic-slug>.mmd` and `<topic-slug>.png` in the working directory.
5. **Theme** (optional) — base, default, forest, dark, neutral. Default: base.
6. **Background** (optional) — transparent, white, or hex color. Default: white.
7. **Scale** (optional) — 1, 2, or 3 (higher = more pixels). Default: 2.
8. **Create gist** (optional) — upload to GitHub gist after creation. Default: false.

## Workflow

### Step 1: Install mmdc (if needed)

Check if `mmdc` is available. If not, install it:

```bash
npm install -g @mermaid-js/mermaid-cli
```

Verify:
```bash
mmdc --version
```

### Step 2: Design the Diagram

Based on the user's description, create the Mermaid diagram:

1. Choose the appropriate diagram type
2. Structure nodes and edges logically
3. Use subgraphs to group related elements
4. Add meaningful labels on connections
5. Apply colors using `style` directives for visual distinction

#### Diagram Type Reference

| Type | Use Case |
|------|----------|
| `flowchart` | Process flows, architectures, branching strategies |
| `sequenceDiagram` | API calls, user interactions, service communication |
| `classDiagram` | OOP class relationships |
| `stateDiagram` | State machines, status flows |
| `erDiagram` | Database schemas, entity relationships |
| `gantt` | Project timelines, sprint planning |
| `pie` | Distribution, percentages |
| `mindmap` | Brainstorming, hierarchical concepts |
| `gitgraph` | Git branching and merging |

#### Color Palette Suggestions

Use consistent, accessible colors:

```
Primary (Blue):    fill:#4A90D9, stroke:#2C5F8A, color:#fff
Success (Green):   fill:#27AE60, stroke:#1E8449, color:#fff
Warning (Orange):  fill:#F39C12, stroke:#D68910, color:#fff
Danger (Red):      fill:#E74C3C, stroke:#C0392B, color:#fff
Info (Purple):     fill:#8E44AD, stroke:#6C3483, color:#fff
Neutral (Gray):    fill:#95A5A6, stroke:#7F8C8D, color:#fff

Subgraph backgrounds:
Green section:     fill:#E8F8E0, stroke:#27AE60, color:#2C3E50
Blue section:      fill:#E8F0FE, stroke:#2E86DE, color:#2C3E50
Orange section:    fill:#FFF3E0, stroke:#F39C12, color:#2C3E50
Red section:       fill:#FFEAEA, stroke:#E74C3C, color:#2C3E50
Purple section:    fill:#F0E6FF, stroke:#8E44AD, color:#2C3E50
```

#### Layout Best Practices

- Use `flowchart LR` for horizontal flows (good for pipelines, branching)
- Use `flowchart TB` for vertical flows (good for hierarchies, sequences)
- Group related items in `subgraph` blocks
- Use `direction TB` inside subgraphs to stack items vertically
- Use `==>` for bold/highlighted connections, `-.->` for dashed/indirect connections
- Use `<b>bold</b>` and `<br/>line break` in node labels for multi-line text
- Keep node IDs short and descriptive (DEV, REL, PROD, F1, etc.)
- **Never use HTML comments** (`<!-- -->`) in .mmd files — mmdc cannot parse them. Use `%%` for comments instead.

### Step 3: Write the .mmd File

Write the Mermaid source file with:

```
%% Compile: mmdc -i <file>.mmd -o <file>.png -w 1600 -H 700 -s 2 -b white
%%{init: {'theme': 'base', 'themeVariables': { 'fontSize': '20px', 'fontFamily': 'Inter, sans-serif'}}}%%
<diagram content>
```

Always include the compile command as a `%%` comment at the top so anyone can reproduce the image.

### Step 4: Compile to PNG

```bash
mmdc -i <file>.mmd -o <file>.png -w 1600 -H 700 -s 2 -b white
```

Options:
- `-w` — width in pixels (default 800, recommend 1200-1600 for complex diagrams)
- `-H` — height in pixels (default 600, increase for tall diagrams)
- `-s` — scale factor (1, 2, or 3; higher = sharper, larger file)
- `-b` — background color (`white`, `transparent`, or hex like `#F0F0F0`)

For **high quality**: use `-s 3 -w 1600 -H 700 -b white`

### Step 5: Optionally Create Gist

If requested or if the user previously used `gh gist`:

```bash
gh gist create <file>.mmd --public --desc "<description>"
```

Note: `gh gist` does not support uploading binary files (PNG). Only the .mmd source can be uploaded. The compile command in the header comment lets anyone generate the image.

## Quality Checklist

Before delivering, verify:

- [ ] The .mmd file compiles without errors
- [ ] The PNG is generated and has reasonable file size
- [ ] Node labels are readable (font size 20px+ recommended)
- [ ] Colors are consistent and accessible
- [ ] Layout flows logically (LR or TB as appropriate)
- [ ] Subgraphs group related elements
- [ ] Connection labels explain relationships
- [ ] The compile command is included as a `%%` comment at the top
- [ ] No HTML comments (`<!-- -->`) — only `%%` comments
- [ ] No node ID conflicts with subgraph names

## Common Issues & Fixes

| Issue | Fix |
|-------|-----|
| `No diagram type detected` | Remove HTML comments, use `%%` comments only |
| `would create a cycle` | Node ID conflicts with subgraph name — rename one of them |
| Diagram too small | Increase `-w`, `-H`, and `-s` values |
| Font too small | Increase `fontSize` in `themeVariables` |
| Text cut off | Widen the image or use `<br/>` for line breaks |
| `mmdc` not found | `npm install -g @mermaid-js/mermaid-cli` |
