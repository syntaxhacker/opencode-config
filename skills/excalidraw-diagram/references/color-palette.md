# Color Palette & Brand Style

**This is the single source of truth for all colors and brand-specific styles.** To customize diagrams for your own brand, edit this file — everything else in the skill is universal.

---

## Shape Colors (Semantic)

Colors encode meaning, not decoration. Each semantic purpose has a fill/stroke pair.

| Semantic Purpose | Fill | Stroke |
|------------------|------|--------|
| Primary/Neutral | `#a855f7` | `#6d28d9` |
| Secondary | `#c084fc` | `#6d28d9` |
| Tertiary | `#d8b4fe` | `#6d28d9` |
| Start/Trigger | `#fed7aa` | `#c2410c` |
| End/Success | `#a7f3d0` | `#047857` |
| Warning/Reset | `#fee2e2` | `#dc2626` |
| Decision | `#fef3c7` | `#b45309` |
| AI/LLM | `#ddd6fe` | `#7c3aed` |
| Inactive/Disabled | `#f3e8ff` | `#a855f7` (use dashed stroke) |
| Error | `#fecaca` | `#b91c1c` |

**Rule**: Always pair a darker stroke with a lighter fill for contrast.

---

## Text Colors (Hierarchy)

Use color on free-floating text to create visual hierarchy without containers.

| Level | Color | Use For |
|-------|-------|---------|
| Title | `#6d28d9` | Section headings, major labels |
| Subtitle | `#a855f7` | Subheadings, secondary labels |
| Body/Detail | `#64748b` | Descriptions, annotations, metadata |
| On light fills | `#374151` | Text inside light-colored shapes |
| On dark fills | `#ffffff` | Text inside dark-colored shapes |

---

## Evidence Artifact Colors

Used for code snippets, data examples, and other concrete evidence inside technical diagrams.

| Artifact | Background | Text Color |
|----------|-----------|------------|
| Code snippet | `#0f1322` | Syntax-colored (language-appropriate) |
| JSON/data example | `#0f1322` | `#22c55e` (green) |

---

## Default Stroke & Line Colors

| Element | Color |
|---------|-------|
| Arrows | Use the stroke color of the source element's semantic purpose |
| Structural lines (dividers, trees, timelines) | Primary stroke (`#6d28d9`) or Slate (`#64748b`) |
| Marker dots (fill + stroke) | Primary fill (`#a855f7`) |

---

## Background

| Property | Value |
|----------|-------|
| Canvas background | `#ffffff` |
