---
name: cc-htmlwf
description: Deliver code changes, plans, and architecture docs as self-contained HTML pages with copy buttons and color diffs. Use when the user wants to review changes before applying them, or when step-by-step code delivery is preferred over direct file editing.
---

# HTML Code Delivery Workflow

Deliver code changes through self-contained HTML pages that open in the browser. The user reviews, copies code, and pastes into their editor. This keeps the user in full control of what gets written to disk.

## When to Use

Use this workflow when:
- The user wants to **review changes before applying** them
- Delivering **step-by-step implementation** of a larger plan
- Writing **architecture docs, plans, or analysis** reports
- The user explicitly says "don't edit files directly" or "let me paste manually"

Skip this workflow (edit files directly) when:
- The user says "just fix it" or "edit the file"
- Making single-line bug fixes
- The change is trivial and obvious

## HTML Page Structure

Every deliverable page MUST use the `TEMPLATE.html` from this skill directory as the starting point. Copy its full `<style>` block and body structure into every generated page.

### 1. Base CSS Framework

Use the CSS from `TEMPLATE.html` without modification. It already has the correct font sizes, color tokens, and spacing. Do NOT shrink fonts or override the :root variables.

### 2. Color Coding (CRITICAL)

Use these exact CSS classes to mark code changes (dark theme: black background `#0d1117`, white text `#e6edf3`):

```css
.hl-add { color: var(--add-fg); background: var(--add-bg); }  /* green #3fb950 — 新增代码 */
.hl-mod { color: var(--mod-fg); background: var(--mod-bg); }  /* blue #58a6ff — 修改代码 */
.hl-del { color: var(--del-fg); background: var(--del-bg); text-decoration: line-through; }  /* red #f85149 — 删除代码 */
.tag-add { background: #0d2137; color: #58a6ff; }  /* tag: NEW — 新增文件/模块 */
.tag-del { background: var(--bad-bg); color: var(--bad-fg); }  /* tag: DELETE — 删除文件/模块 */
.tag-mod { background: var(--warn-bg); color: var(--warn-fg); }  /* tag: MODIFY — 修改文件 */
.tag-keep { background: var(--good-bg); color: var(--good-fg); }  /* tag: KEEP — 保留不变 */
```

For inline diffs within a code block, wrap lines in spans:
```html
<span class="hl-add">polyhal = "0.4.0"</span>      <!-- 新加行 — 绿色 -->
<span class="hl-mod">use polyhal::MappingFlags;</span>  <!-- 修改行 — 蓝色 -->
<span class="hl-del">use crate::mm::MapPermission;</span> <!-- 删除行 — 红色 -->
```

For full-file code blocks, use the `.ln` span for line numbers:
```html
<pre><code id="code1"><span class="ln hl-add">// 新文件全部内容</span></code></pre>
```

**Line numbers** are automatic via CSS counter — each code line wrapped in `<span class="ln">` gets a line number on the left.

### 3. Copy Button (CRITICAL)

Every code block needs a copy button. The button text is ALWAYS just "Copy" — no extra words, no prefixes, no suffixes. On click it changes to "✓ 已复制" for 1.5 seconds, then returns to "Copy".

```html
<div class="copy-wrap">
  <button class="copy-btn" onclick="copyCode(this, 'code1')">Copy</button>
  <pre><code id="code1">...code...</code></pre>
</div>
```

The `copyCode` function is already in the template — do NOT write your own version. Do NOT change the button text to "copy追加内容" or any other variant.

### 4. Rich Chinese Comments in Code (CRITICAL)

All delivered code MUST include explanatory comments in Chinese. The user is learning — every non-obvious decision needs a one-line comment.

```rust
// ✓ GOOD: 注释解释 WHY — 为什么这样做、为什么选这个值
/// 分配物理帧并泄漏 FrameTracker，返回裸 PhysAddr。
/// 供 PageAllocImpl 使用，使 PolyHAL 能直接分配页面。
pub unsafe fn frame_alloc_persist() -> Option<polyhal::PhysAddr> { ... }

// 将跳板页映射到用户地址空间顶端，用于 trap 入口
memory_set.map_trampoline();

// ✗ BAD: 注释只是重复代码本身
// 调用 frame_alloc_persist
frame_alloc_persist();
```

Rules:
- New functions/types: one doc comment (`///`) explaining purpose in Chinese
- Non-obvious logic: a line comment (`//`) explaining WHY in Chinese
- Architecture decisions: a short comment block referencing the decision
- Do NOT comment what the code does (the code already says that)
- Rust `unsafe` blocks: MUST comment why it's safe in this context
- Comments should be in Chinese, helping the reader understand the design intent

### 5. Explanation Table

After each code block, explain **what changed and why**:

```html
<table>
  <tr><th>变更</th><th>旧</th><th>新</th><th>原因</th></tr>
  <tr>
    <td>依赖</td>
    <td><code>riscv = {...}</code></td>
    <td><code>polyhal = "0.4.0"</code></td>
    <td>多架构 HAL 替代 RISC-V 绑定</td>
  </tr>
</table>
```

### 6. Auto-Open

After writing the HTML file, always run:
```bash
xdg-open /path/to/docs/filename.html
```

## File Management (CRITICAL)

When delivering multi-file changes (e.g., an implementation guide with several steps), use **split files** organized in categorized directories. Use the file explorer / directory structure to organize:

```
docs/
├── 计划/PolyHAL迁移方案.html          # 总体规划
├── 实现指南/
│   ├── L1a-Cargo依赖配置.html          # 第一步
│   ├── L1b-编译配置与Vendor.html       # 第二步
│   └── L2-入口与启动.html              # 第三步
├── 参考/
│   └── PolyHAL-API参考.html            # 参考资料
└── 分析/
    └── 帧分配器接入链路.html            # 分析报告
```

Rules:
- One HTML file per logical step/topic — do NOT cram everything into a single giant page
- Use Chinese directory names for clarity (实现指南, 参考, 分析, 计划)
- Name files with layer prefix (L1a, L1b, L2) when delivering sequential steps
- After creating a new subdirectory, briefly mention the file structure to the user

## Step Granularity

Group **related file changes** into one step. Related means:
- The files are in the same module/layer
- Changing one requires changing the other
- The total diff is ~100-300 lines (digestible in one review session)

Do NOT split tightly-coupled changes (e.g., Cargo.toml + .cargo/config.toml) across steps. Do NOT group unrelated changes (e.g., page table rewrite + timer rewrite) into one step.

## Expected Compilation Result

After each step, explicitly state:
- What SHOULD compile
- What WILL break (with specific files)
- How many files are affected

This lets the user verify the step before proceeding.

## Workflow Summary

```
1. Read current code → understand what needs to change
2. Write HTML in docs/ with: color diffs, copy buttons, explanation tables
3. xdg-open the HTML
4. User reviews in browser, copies code, pastes into editor
5. User confirms step complete → next step
```

## TEMPLATE.html

The full template is at `skills/html-code-delivery/TEMPLATE.html`. Always start from that file — it has the correct font sizes, color tokens, copy button JS, and all CSS classes pre-defined.
