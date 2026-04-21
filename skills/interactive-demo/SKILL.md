---
name: interactive-demo
description: Generate clean, interactive Vite + React + Bun demos that teach a library, API, or concept. Use when asked to create a demo, build an interactive example, or explain something with examples.
---

# Skill: interactive-demo

Generate clean, interactive Vite + React + Bun demos that teach a library, API, or concept. Each concept gets its own section with a live working example — no slop, no filler.

## When to Use

- "Create a demo for [library/concept]"
- "Build an interactive example of [X]"
- "Explain how [framework] works with a demo"
- "Teach [topic] with interactive examples"

## Workflow

### Step 1: Understand the Source

Research the target library/concept thoroughly:

1. **Web docs** — `webfetch` the README, docs, or official site
2. **Source code** — if available, explore the actual source to understand internals
3. **Key concepts** — identify 5-8 distinct concepts worth demonstrating

Use the `explore` agent for source code deep-dives. Always read real code — never guess APIs.

### Step 2: Plan Sections

Map out sections before coding. Each section must have:

- A **numbered title** (e.g., "3. How prepare() Works: Segmentation")
- A **short explanation** (2-4 sentences, no filler)
- A **live interactive demo** (not just code snippets)
- A **code snippet** showing the actual API (only when it clarifies)

Good section structure:
```
1. The Problem — why this exists, what was broken
2. The Idea — the core insight in one sentence + live demo
3-7. Individual concepts — one per section, each with its own demo
8. Real-world use case — ties it together
```

### Step 3: Scaffold the Project

Always use Vite + React + TypeScript + Bun:

```bash
cd /tmp
bunx create-vite@latest <demo-name> --template react-ts
cd <demo-name>
bun install
bun add <target-library>
bun add prismjs @types/prismjs
```

### Step 4: Write the Code

#### Design Principles

- **Dark theme default.** Dark background, clean typography. Always include a light mode toggle.
- **Split-panel layout.** Each section uses a two-column layout: live demo on the left, relevant code snippet on the right. Use the `Section` component (see below).
- **One concept per section.** Each section is self-contained.
- **Every section has a live demo.** Not just text — something the user can interact with.
- **Minimal UI.** No hero images, no animations, no gradients. Content-first.
- **No comments in code.** The surrounding text IS the documentation.
- **No emoji in UI.** Only in console output if requested.

#### File Structure

```
src/
  App.tsx       — all sections and logic (single file is fine)
  index.css     — global styles
  main.tsx      — entry point (don't touch)
```

Keep it in one `App.tsx` unless it exceeds ~1500 lines.

#### CSS Style Guide

Use CSS custom properties with dark as the default (`:root`) and a `[data-theme="light"]` override. Toggle via `data-theme` attribute on `<html>`. In React: `document.documentElement.setAttribute('data-theme', dark ? 'dark' : 'light')`.

```css
/* Dark theme (default) */
:root {
  --bg: #0f1117;
  --bg2: #1a1d27;
  --text: #e2e4e9;
  --text2: #9ca3af;
  --text3: #6b7280;
  --border: #2d3140;
  --border2: #2d3140;
  --code-bg: #1e2130;
  --note-bg: #2a1f0a;
  --note-border: #d97706;
  --note-text: #fbbf24;
  --accent: #3b82f6;
  --shadow: 0 1px 3px rgba(0,0,0,0.3);
}

/* Light theme override */
[data-theme="light"] {
  --bg: #fafafa;
  --bg2: #fff;
  --text: #1a1a1a;
  --text2: #555;
  --text3: #999;
  --border: #e0e0e0;
  --border2: #e5e5e5;
  --code-bg: #f0f0f0;
  --note-bg: #fffbeb;
  --note-border: #f59e0b;
  --note-text: #78350f;
  --accent: #0066cc;
  --shadow: 0 1px 3px rgba(0,0,0,0.06);
}

/* Base — always use variables, never hardcode colors */
body { background: var(--bg); color: var(--text); font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif; }
#root { max-width: 1200px; margin: 0 auto; padding: 40px 24px 80px; }

/* Typography */
h1 { font-size: 32px; font-weight: 700; letter-spacing: -0.5px; }
h2 { font-size: 22px; margin: 56px 0 16px; border-bottom: 2px solid var(--border2); }
p { color: var(--text2); margin-bottom: 16px; }
code { font-size: 13px; background: var(--code-bg); padding: 2px 6px; border-radius: 4px; color: var(--text); }

/* Demo boxes */
.demo { background: var(--bg2); border: 1px solid var(--border); border-radius: 12px; padding: 24px; margin: 20px 0 32px; box-shadow: var(--shadow); }
.demo-label { font-size: 11px; font-weight: 700; text-transform: uppercase; letter-spacing: 1px; color: var(--text3); margin-bottom: 16px; }

/* Inputs */
textarea, input[type=text] { background: var(--bg); border: 1px solid var(--border); padding: 10px 12px; border-radius: 8px; font-family: 'SF Mono', monospace; font-size: 14px; color: var(--text); }
input[type=range] { accent-color: var(--accent); }

/* Buttons */
.btn { padding: 8px 16px; border-radius: 8px; font-size: 13px; font-weight: 600; border: none; cursor: pointer; }
.btn-primary { background: var(--text); color: var(--bg); }
.btn-outline { background: var(--bg2); color: var(--text); border: 1px solid var(--border); }

/* Result cards */
.result { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }
.result-card { background: var(--bg); border-radius: 8px; padding: 12px 16px; }
.result-card .num { font-size: 24px; font-weight: 700; font-family: 'SF Mono', monospace; color: var(--text); }

/* Notes/callouts */
.note { background: var(--note-bg); border-left: 3px solid var(--note-border); padding: 12px 16px; border-radius: 0 8px 8px 0; margin: 16px 0; color: var(--note-text); }

/* Theme toggle button */
.theme-toggle { background: var(--bg2); border: 1px solid var(--border); border-radius: 8px; padding: 6px 14px; font-size: 13px; font-weight: 600; cursor: pointer; color: var(--text); }

/* Split-panel layout: demo left, code right */
.split-section { margin-bottom: 48px; }
.section-header { margin-bottom: 20px; }
.section-header h2 { font-size: 20px; font-weight: 700; color: var(--text); margin-bottom: 8px; display: flex; align-items: center; gap: 8px; }
.section-num { color: var(--accent); font-family: 'SF Mono', monospace; font-size: 16px; }
.section-header p { color: var(--text2); font-size: 14px; line-height: 1.6; max-width: 700px; }
.split-panel { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; align-items: start; }
.split-left { background: var(--bg2); border: 1px solid var(--border); border-radius: 10px; overflow: hidden; }
.split-right { border: 1px solid #3e3d32; border-radius: 10px; overflow: hidden; background: #272822; }
.panel-label { font-size: 10px; font-weight: 700; text-transform: uppercase; letter-spacing: 1.2px; color: var(--text3); padding: 12px 16px 0; }
.split-right .panel-label { color: #75715e; }
.demo-inner { padding: 16px; }
.code-block { padding: 16px; overflow-x: auto; background: #272822; }
.code-block pre { margin: 0; }
.code-block code { font-family: 'SF Mono', 'Cascadia Code', Consolas, monospace; font-size: 12.5px; line-height: 1.7; color: #f8f8f2; background: none; padding: 0; white-space: pre; display: block; }
.code-block .token.comment,.code-block .token.prolog,.code-block .token.doctype,.code-block .token.cdata { color: #75715e; }
.code-block .token.punctuation { color: #f8f8f2; }
.code-block .token.property,.code-block .token.tag,.code-block .token.boolean,.code-block .token.number,.code-block .token.constant,.code-block .token.symbol,.code-block .token.deleted { color: #ae81ff; }
.code-block .token.selector,.code-block .token.attr-name,.code-block .token.string,.code-block .token.char,.code-block .token.builtin,.code-block .token.inserted { color: #e6db74; }
.code-block .token.operator,.code-block .token.entity,.code-block .token.url { color: #f8f8f2; }
.code-block .token.atrule,.code-block .token.attr-value,.code-block .token.keyword { color: #f92672; }
.code-block .token.function,.code-block .token.class-name { color: #a6e22e; }
.code-block .token.regex,.code-block .token.important,.code-block .token.variable { color: #fd971f; }
.code-block .token.parameter { color: #fd971f; }

@media (max-width: 900px) {
  .split-panel { grid-template-columns: 1fr; }
}
```

#### Section Component

Every section uses a reusable `Section` component that renders a split-panel layout (demo left, code right):

```tsx
import Prism from 'prismjs'
import 'prismjs/components/prism-javascript'

function CodeBlock({ code }: { code: string }) {
  const html = useMemo(() => {
    const grammar = Prism.languages.javascript
    const tokens = Prism.tokenize(code, grammar) as (string | Prism.Token)[]
    return tokens.map((token) => {
      if (typeof token === 'string') return token
      const cls = `token ${token.type}`
      const content = Array.isArray(token.content)
        ? token.content.map((t) => typeof t === 'string' ? t : `<span class="token ${(t as Prism.Token).type}">${(t as Prism.Token).content}</span>`).join('')
        : String(token.content)
      return `<span class="${cls}">${content}</span>`
    }).join('')
  }, [code])

  return (
    <div className="code-block">
      <pre><code dangerouslySetInnerHTML={{ __html: html }} /></pre>
    </div>
  )
}

function Section({ num, title, description, children, code }: {
  num: number; title: string; description: string;
  children: React.ReactNode; code: string
}) {
  return (
    <section className="split-section">
      <div className="section-header">
        <h2><span className="section-num">{num}.</span> {title}</h2>
        <p>{description}</p>
      </div>
      <div className="split-panel">
        <div className="split-left">
          <div className="panel-label">Live Demo</div>
          {children}
        </div>
        <div className="split-right">
          <div className="panel-label">Code</div>
          <CodeBlock code={code} />
        </div>
      </div>
    </section>
  )
}
```

Usage:
```tsx
<Section
  num={1}
  title="Basic Counter"
  description="Each call creates independent private state via closures."
  code={`function createCounter() {
  let count = 0
  return {
    increment: () => ++count,
    get: () => count
  }
}`}
>
  <CounterDemo />
</Section>
```

#### Interactive Patterns

Use these proven patterns for demos:

| Pattern | When to use | Implementation |
|---------|-------------|---------------|
| **Slider** | Adjust a parameter and see results live | `<input type="range">` + `useState` |
| **Drag-to-resize** | Show how output changes with width/size | Custom `useDragResize` hook with `mousedown`/`mousemove` |
| **Draggable element** | Move something and see side effects | `mousedown` + `mousemove` + `mouseup` with offset tracking |
| **Editable text** | User types input, see processed output | `<textarea>` + `onChange` |
| **Toggle** | Show/hide a feature or comparison | `useState(false)` + button toggle |
| **Benchmark button** | Run a timed comparison | `performance.now()` before/after |
| **Step-through** | Click through pipeline stages | `useState(stepIndex)` + conditional rendering |
| **Mapping table** | Show how API X maps to concept Y | HTML `<table>` with `.mapping-table` CSS class |
| **Comparison grid** | Side-by-side comparison of two approaches | 2-column CSS grid with `.comparison-card` items |
| **Live log output** | Show sequential events with timestamps | Array of `{ time, msg, type }` rendered in a scrollable container |
| **Gantt/timeline** | Show scheduling or execution over time | Absolutely-positioned divs inside a relative container |
| **CPU/core bars** | Show utilization across cores | Array of progress bars, driven by `useInterval` |
| **Animated ring/orbit** | Show cyclical process (event loop phases) | Absolutely-positioned nodes around a circle using trigonometry |
| **Stack push/pop** | Visualize call stack or undo history | `transform: translateY` + `scaleY` with `@keyframes` enter/exit |
| **Queue animation** | Show items entering/leaving a queue | `transform: translateX` + opacity with `@keyframes` |
| **Code editor highlight** | Show which line of code is executing | Line-level background color change on active line |
| **Terminal console** | Show sequential output with typing effect | Dark background, monospace font, `border-right` blink animation |
| **Progressive timeline** | Step through events sequentially | Vertical timeline with dots, `opacity` + `translateY` reveal per step |
| **Quiz/game** | Test user understanding interactively | Click-to-order items, correct/wrong shake animation |
| **Floating particles** | Ambient background effect (use sparingly) | Fixed-position container with `pointer-events: none`, random `@keyframes` |

#### Custom Hooks to Reuse

**Drag resize** (drag right edge of a container):
```tsx
function useDragResize(initial = 320, min = 60, max = 700) {
  const [w, setW] = useState(initial)
  const active = useRef(false)
  const startX = useRef(0)
  const startW = useRef(0)

  const onDown = (e: React.MouseEvent) => {
    e.preventDefault()
    active.current = true
    startX.current = e.clientX
    startW.current = w
    const onMove = (e: MouseEvent) => {
      if (!active.current) return
      setW(Math.max(min, Math.min(max, startW.current + e.clientX - startX.current)))
    }
    const onUp = () => {
      active.current = false
      window.removeEventListener('mousemove', onMove)
      window.removeEventListener('mouseup', onUp)
    }
    window.addEventListener('mousemove', onMove)
    window.addEventListener('mouseup', onUp)
  }
  return { width: w, onDown, setWidth: setW }
}
```

**Draggable position** (move an element freely):
```tsx
function useDragPosition(initial = { x: 40, y: 20 }) {
  const [pos, setPos] = useState(initial)
  const dragging = useRef(false)
  const offset = useRef({ x: 0, y: 0 })

  const onDown = (e: React.MouseEvent) => {
    e.preventDefault()
    offset.current = { x: e.clientX - pos.x, y: e.clientY - pos.y }
    dragging.current = true
    const onMove = (e: MouseEvent) => {
      if (!dragging.current) return
      setPos({ x: e.clientX - offset.current.x, y: e.clientY - offset.current.y })
    }
    const onUp = () => {
      dragging.current = false
      window.removeEventListener('mousemove', onMove)
      window.removeEventListener('mouseup', onUp)
    }
    window.addEventListener('mousemove', onMove)
    window.addEventListener('mouseup', onUp)
  }
  return { pos, dragging, onDown }
}
```

#### Animation Patterns

When animations help teach a concept (event loop phases, stack operations, execution order), use CSS `@keyframes` — not JS animation libraries. Keep them subtle and purposeful.

**Key principles:**
- Always use `cubic-bezier(0.4, 0, 0.2, 1)` for smooth, natural easing
- Use `transform` and `opacity` for animations (GPU-accelerated, no layout thrash)
- Never animate `width`, `height`, `top`, `left` directly — use `transform` instead
- Use `transition: all 0.3s` on interactive elements for smooth state changes

**Reusable CSS animations:**

```css
/* Stack push: frame slides in from above */
@keyframes stack-push {
  0% { transform: translateY(-20px) scaleY(0.8); opacity: 0; }
  100% { transform: translateY(0) scaleY(1); opacity: 1; }
}

/* Queue add: item slides in from right */
@keyframes queue-add {
  0% { transform: translateX(30px); opacity: 0; }
  100% { transform: translateX(0); opacity: 1; }
}

/* Queue dequeue: item slides out to left */
@keyframes queue-dequeue {
  0% { transform: translateX(0); opacity: 1; }
  100% { transform: translateX(-40px); opacity: 0; }
}

/* Wrong answer shake */
@keyframes shake {
  0%, 100% { transform: translateX(0); }
  25% { transform: translateX(-8px); }
  75% { transform: translateX(8px); }
}

/* Blinking cursor for terminal */
.typing { border-right: 2px solid var(--accent); animation: blink 0.8s step-end infinite; }
@keyframes blink { 50% { border-color: transparent; } }

/* Pulse glow for active elements */
.pulse { animation: pulse-glow 2s ease-in-out infinite; }
@keyframes pulse-glow {
  0%, 100% { box-shadow: 0 0 5px rgba(99,102,241,0.2); }
  50% { box-shadow: 0 0 25px rgba(99,102,241,0.6); }
}

/* Spinning dashed ring (event loop) */
.loop-ring { border: 2px dashed var(--border); animation: ring-spin 60s linear infinite; }
@keyframes ring-spin { to { transform: translate(-50%, -50%) rotate(360deg); } }
```

**Triggering animations from React:**

```tsx
// Add entering class, remove it after animation completes
const pushFrame = (name: string) => {
  const id = nextId()
  setStack(prev => [...prev, { id, name, entering: true }])
  setTimeout(() => setStack(prev => prev.map(f => f.id === id ? { ...f, entering: false } : f)), 350)
}

// In JSX:
<div className={`stack-frame ${frame.entering ? 'entering' : ''}`} />
```

**Circular/orbital layout for cyclical processes:**

```tsx
const RADIUS = 150
const ANGLE_START = -90
const ANGLE_STEP = 360 / phases.length

{phases.map((phase, i) => {
  const angle = (ANGLE_START + i * ANGLE_STEP) * (Math.PI / 180)
  const x = centerX + RADIUS * Math.cos(angle)
  const y = centerY + RADIUS * Math.sin(angle)
  return <div style={{ position: 'absolute', left: x, top: y }} />
})}
```

### Step 5: Run and Verify

```bash
bun run --bun vite
```

Check for:
- No console errors
- All interactive elements work
- Text is readable and concise
- No unnecessary complexity

### Step 6: Present to User

Return:
- The local URL (http://localhost:5173)
- A brief summary of sections covered
- Any notable interactive features

## Anti-Patterns to Avoid

- **Don't add comments to code.** The prose around it explains everything.
- **Don't use emoji in the UI.** Keep it clean and professional.
- **Don't hardcode only one theme.** Always support both dark and light mode with a toggle.
- **Don't add gratuitous animations.** Only animate when it teaches something (e.g., event loop phases, stack push/pop, execution order). Avoid decorative animations.
- **Don't create multiple files** unless the component count warrants it.
- **Don't explain what you're going to do.** Just build it.
- **Don't add a footer with links** unless the user asks.
- **Don't copy marketing language** from the library's README. Explain in your own words.
- **Don't guess APIs.** Always verify against real source code or docs.
- **Don't use `<` or `>` in JSX text.** Escape them as `{'<'}` and `{'>'}` to avoid parse errors.
- **Don't use bare numbers in JSX inline styles.** Always include units as strings: `padding: '8px'`, not `padding: 8`. Vite's OXC parser will reject bare numbers.

## Multi-Topic Tabs

When a demo covers multiple related topics (e.g., "Threads vs Processes vs CPU"), use tab navigation to organize them:

```tsx
const [tab, setTab] = useState<'topic1' | 'topic2'>('topic1')

<div className="tabs">
  <button className={`tab ${tab === 'topic1' ? 'active' : ''}`} onClick={() => setTab('topic1')}>Topic 1</button>
  <button className={`tab ${tab === 'topic2' ? 'active' : ''}`} onClick={() => setTab('topic2')}>Topic 2</button>
</div>
{tab === 'topic1' && <Topic1Tab />}
{tab === 'topic2' && <Topic2Tab />}
```

Each tab should have 3-5 sections with its own intro paragraph. Tabs are preferable over one extremely long scrolling page.

## Quality Checklist

- [ ] Dark theme by default, clean design — looks like documentation, not a landing page
- [ ] Every section teaches one concept
- [ ] Every section has a live, interactive demo
- [ ] Explanations are 2-4 sentences, no filler
- [ ] Code snippets are minimal and annotated
- [ ] No comments in JSX/TSX code
- [ ] Works in the browser without errors
- [ ] All interactive elements are wired up
- [ ] Text reads like a tutorial, not a README

## Example Output Structure

```
http://localhost:5173/

1. The Problem — why this library exists
2. The Idea — the core insight + drag-to-resize demo
3. Concept A — slider demo showing X
4. Concept B — editable text demo showing Y
5. Concept C — draggable element demo showing Z
6. Performance — benchmark button comparing old vs new
7. Real-World Use Case — ties concepts together
```

Each section: ~20 lines of prose + 1 interactive demo + 1 code snippet (optional).
