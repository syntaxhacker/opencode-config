---
name: react-bundle-analyze
description: Analyze React/Vite build output to find the biggest modules, spot bundle bloat, and identify optimization opportunities. Use when asked to analyze bundle size, check what's bloating the build, or optimize the bundle.
---

# Skill: react-bundle-analyze

Analyze a React/Vite project's production build to identify the biggest modules, dependencies, and optimization opportunities.

## When to Use

- "Analyze the bundle"
- "What's making my build so big?"
- "Check bundle size"
- "Find the biggest modules"
- "Optimize the bundle"
- "Why is my bundle so large?"

## Workflow

### Step 1: Detect the Project Setup

1. Read `package.json` to identify:
   - Build tool (Vite, Webpack, CRA, Next.js, etc.)
   - Package manager (npm, bun, yarn)
   - Key dependencies
2. Read the build config (`vite.config.js`, `webpack.config.js`, `next.config.js`, etc.)
3. Check if `rollup-plugin-visualizer` is already installed

### Step 2: Set Up the Analyzer

#### For Vite projects (most React apps):

1. Install `rollup-plugin-visualizer` if not present:
   ```bash
   bun add -d rollup-plugin-visualizer
   ```
   Use the project's package manager (check for `bun.lockb`, `yarn.lock`, `package-lock.json`).

2. Add the visualizer plugin to the build config. Use the `raw-data` template approach — it generates JSON during build, then the CLI converts it to multiple views:

   **vite.config.js:**
   ```js
   import { visualizer } from 'rollup-plugin-visualizer';

   // Add to plugins array (last position):
   visualizer({
     filename: 'stats.json',
     template: 'raw-data',
     sourcemap: true,
   }),
   ```

3. Add an `analyze` script to `package.json`:
   ```json
   "analyze": "npm run build && rollup-plugin-visualizer --filename stats.html --template treemap --open --sourcemap stats.json && rollup-plugin-visualizer --filename stats-sunburst.html --template sunburst --sourcemap stats.json && rollup-plugin-visualizer --filename stats-flamegraph.html --template flamegraph --sourcemap stats.json"
   ```

#### For Webpack projects:

Use `webpack-bundle-analyzer` instead:
```bash
npm install --save-dev webpack-bundle-analyzer
```

Add to webpack config:
```js
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;

plugins: [
  new BundleAnalyzerPlugin({ analyzerMode: 'static', openAnalyzer: true }),
],
```

#### For Next.js projects:

```bash
npm install --save-dev @next/bundle-analyzer
```

Add to `next.config.js`:
```js
const withBundleAnalyzer = require('@next/bundle-analyzer')({ enabled: true });
module.exports = withBundleAnalyzer({});
```

### Step 3: Run the Build and Analyze

```bash
npm run analyze
```

This produces:
- `stats.html` — Treemap view (opens automatically, best for spotting large modules)
- `stats-sunburst.html` — Sunburst ring chart (good for hierarchical overview)
- `stats-flamegraph.html` — Top-down flame chart (familiar from perf tooling)

### Step 4: Parse the Raw Data

Read `stats.json` to extract actionable insights. Parse the tree structure and produce a sorted list of the top modules by size.

```bash
# Quick top-10 biggest modules from stats.json
node -e "
const data = require('./stats.json');
const modules = [];
function walk(node, depth = 0) {
  if (node.nodeParts) {
    Object.entries(node.nodeParts).forEach(([name, parts]) => {
      parts.forEach(p => {
        modules.push({ name: name + (p.name !== name ? '/' + p.name : ''), size: p.renderedLength || 0 });
      });
    });
  }
  if (node.children) node.children.forEach(c => walk(c, depth + 1));
}
walk(data.tree);
modules.sort((a, b) => b.size - a.size);
modules.slice(0, 20).forEach((m, i) => {
  const kb = (m.size / 1024).toFixed(1);
  const bar = '#'.repeat(Math.min(Math.round(m.size / 10240), 40));
  console.log((i+1).toString().padStart(2) + '. ' + kb.padStart(8) + ' KB  ' + bar + '  ' + m.name);
});
"
```

### Step 5: Provide Optimization Recommendations

Based on the analysis, suggest specific optimizations:

1. **Code splitting** — Use `React.lazy()` + `import()` for large route-level components
2. **Manual chunks** — Split vendor libraries into separate chunks via `rollupOptions.output.manualChunks`
3. **Tree shaking** — Check if large imports can be replaced with specific imports (e.g., `lodash/debounce` instead of `lodash`)
4. **Duplicate detection** — Look for the same dependency appearing in multiple chunks
5. **Replace heavy libraries** — Suggest lighter alternatives (e.g., `dayjs` instead of `moment`, already using dayjs? good)
6. **CSS optimization** — Check for large CSS files that could be purged or split

### Step 6: Open Reports

```bash
open stats.html
open stats-sunburst.html
open stats-flamegraph.html
```

## Output Format

Present findings as:

```
## Bundle Analysis

**Total size:** X MB (Y MB gzipped)

### Top 10 Biggest Modules

| # | Module | Size | Gzipped |
|---|--------|------|---------|
| 1 | node_modules/antd/... | 850 KB | 210 KB |
| 2 | node_modules/@mui/... | 620 KB | 180 KB |
| ... | ... | ... | ... |

### Recommendations

1. **Code split antd** — It's X% of your bundle. Use `React.lazy()` for pages that use antd components.
2. **Replace lodash with specific imports** — You're importing the full lodash (X KB) but only use 3 functions.
3. **Split MUI into separate chunk** — Add to `manualChunks` to load separately.

### Generated Reports

- `stats.html` — Treemap (interactive, click to zoom)
- `stats-sunburst.html` — Sunburst chart
- `stats-flamegraph.html` — Flamegraph
```

## Anti-Patterns

- Don't modify the build config permanently without asking — the visualizer plugin should ideally be opt-in via a separate script
- Don't suggest removing dependencies without checking if they're actually used (grep the codebase)
- Don't recommend micro-optimizations that save < 10 KB — focus on the big wins
- Don't run the build without checking if it's already been built recently
- Don't forget to clean up generated `stats*.html` and `stats.json` files — add them to `.gitignore`

## .gitignore Addition

Always add these to `.gitignore` after setup:
```
stats.html
stats-sunburst.html
stats-flamegraph.html
stats.json
```
