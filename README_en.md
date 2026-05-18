# HTML Code Delivery — Claude Code Skill

## What is this?

A Claude Code skill that makes the AI agent deliver answers through **self-contained HTML files** instead of editing your code directly. Each HTML file includes syntax-highlighted code blocks, one-click copy buttons, change comparison tables, and auto-opens in your browser when done.

## Why HTML instead of Markdown?

HTML does things Markdown can't:

- **Color diffs** — new lines in green, deleted lines in red with strikethrough, changes at a glance
- **Copy buttons** — every code block has a copy button in the top-right corner
- **Rich layout** — tables, tags, callout boxes for structured change presentation
- **Self-contained** — a single `.html` file is the complete document, no renderer needed

## Use Cases

| Scenario | Example |
|----------|---------|
| Design proposals | Deliver an architecture plan as HTML for review before writing any code |
| Architecture docs | Diagram call chains, component relationships, compare old vs new approaches |
| Step-by-step guides | Break a large refactor into L1→L2→L3 HTML files, ~150 lines of changes each |
| Code analysis | Analyze design issues or performance bottlenecks, referencing specific files and line numbers |
| Reference docs | Compile API interfaces and module conventions as team reference material |

## Output is Files, Not Commands

This skill does **not modify your code directly**. It writes the changes into HTML files, and you copy-paste them into your editor yourself. This matters especially for learners — at each step, you can see exactly what changed, why, and where it goes.

Every code block includes **comments** explaining the design intent — defaulting to Chinese, but you can ask the agent to use English comments instead. These aren't "what does this line do" filler comments; they're "why was this design chosen" intent comments.

## File Structure

Output HTML is organized into categorized directories:

```
docs/
├── 计划/ (Plans)        # Architecture proposals, migration strategies
├── 实现指南/ (Guides)   # Step-by-step implementation (L1a, L1b, L2...)
├── 参考/ (References)   # API docs, module conventions
├── 分析/ (Analysis)     # Code analysis reports, side-by-side comparisons
└── 模板.html            # CSS framework template
```

## Contributing

Issues, PRs, and forks are welcome — this project is young and any improvement helps.

- Author: 复羽叶栾 (256530373@qq.com)
- Repository: https://github.com/fuyuyeluan/cc-htmlwf

## Installation

Place `claude-html-workflow/` in your Claude Code plugins directory:

```
~/.claude/plugins/claude-html-workflow/
```

## Usage

Just describe what you need in Claude Code — the skill triggers automatically. For example:

- "Design an auth system architecture"
- "Write up the L3 memory management migration plan"
- "Analyze the trap flow call chain"

Every HTML file auto-opens in your browser via `xdg-open` when complete.
