# Feishu DocxXML — block reference

Syntax cheatsheet for authoring Lark/Feishu docs via `lark-cli docs +create --content` (and `+update`). Read this when drafting (SKILL.md step 3) to pick correct syntax.

**DocxXML is the default and recommended format** — it is the *only* format that can express callouts (高亮块), grids (分栏), colored text, colored table headers, and checkboxes. Markdown import (`--doc-format markdown`) is a fallback for plain `.md` sources and **cannot carry those rich blocks**, so draft in XML.

**Authoritative source:** the exact, current syntax is owned by the installed `lark-doc` skill — `lark-cli skills read lark-doc references/lark-doc-xml.md`. This file is a practical subset. Always verify by fetching the doc back (SKILL.md step 6).

```bash
# create (XML is the default format — no flag needed)
lark-cli docs +create --content - < body.xml
# verify rendering
lark-cli docs +fetch  --doc "<doc_id>" --doc-format xml
# targeted fix after verification
lark-cli docs +update --doc "<doc_id>" --command str_replace --pattern "旧文本" --content "新文本"
```

## Contents
1. Standard blocks
2. Extended blocks (the "designed" ones — XML only)
3. Diagrams via whiteboard
4. Content → block map
5. Conversion caveats & escaping

---

## 1. Standard blocks

XML tags (an HTML subset). Stay within `h1`–`h4` for readable docs.

```xml
<h1>一级标题</h1>  <h2>二级标题</h2>  <h3>三级标题</h3>
<p>普通段落</p>
<p><b>加粗</b> <em>斜体</em> <del>删除线</del> <u>下划线</u> <code>行内代码</code></p>
<ul><li>无序项<ul><li>嵌套项</li></ul></li></ul>
<ol><li seq="auto">有序项一</li><li seq="auto">有序项二</li></ol>
<checkbox done="false">未完成事项</checkbox>
<checkbox done="true">已完成事项</checkbox>
<blockquote>引用块</blockquote>
<pre lang="bash" caption="可选说明"><code>echo hello</code></pre>
<hr/>
<a href="https://example.com">链接文本</a>
<img href="https://example.com/x.png"/>   <!-- 本地文件改用 docs +media-insert -->
```

- Tag every `<pre>` with a `lang` for syntax highlighting; `caption` is optional.
- Inline-style nesting order (outer → inner), closing reversed: `<a> → <b> → <em> → <del> → <u> → <code> → <span> → 文本`.
- `align="left|center|right"` works on `p` / `h1`-`h9` / `li` / `checkbox`.

## 2. Extended blocks (the "designed" ones — XML only)

These are what make a doc look *designed*. None of them exist in Markdown.

**Callout (高亮块)** — for the BLUF summary, key takeaways, warnings. Child blocks: text / heading / list / checkbox / quote only.

```xml
<callout emoji="✅" background-color="light-green" border-color="green">
  <p><b>推荐</b>：一句话结论放这里。</p>
</callout>
```

- `emoji` defaults to 💡. **Avoid variation-selector emoji**: `⚠️` (U+26A0 + U+FE0F) reverts to 💡 on conversion. Plain single-codepoint emoji survive — `✅ 🚨 🔴 ❗ ⛔` all verified. Use 🚨 for risk callouts instead of ⚠️.
- `background-color`: `gray` | `light-{色}` | `medium-{色}`. `border-color` / `text-color`: the 7 base colors.
- Colors **persist** (stored normalized as `rgb(...)`), but only appear when you fetch with `--detail full`; `--detail simple` (the default) does not echo them, so they look "missing" when they are actually fine.

**Grid (分栏)** — side-by-side panels, ≤ 3 columns, `width-ratio` sums to 1.

```xml
<grid>
  <column width-ratio="0.5"><p>左栏（Before）</p></column>
  <column width-ratio="0.5"><p>右栏（After）</p></column>
</grid>
```

**Colored text / background** (inline):

```xml
<p>环比 <span text-color="red">下降 12% ↓</span>，<span background-color="light-yellow">需关注</span></p>
```

**Colored table header / merged cells**:

```xml
<table>
  <colgroup><col width="120"/></colgroup>
  <thead><tr><th background-color="light-gray">表头</th><th background-color="light-gray">表头</th></tr></thead>
  <tbody><tr><td>单元格</td><td>单元格</td></tr></tbody>
</table>
```

- Merge cells: only the origin cell carries `colspan` / `rowspan`; merged-away cells are omitted.
- `<th>`/`<td>` also accept `vertical-align="top|middle|bottom"`.

**Formula**: `<latex>E = mc^2</latex>` (inline).

**Color semantics** — 7 base colors (`red, orange, yellow, green, blue, purple, gray`). Keep one semantic→color mapping consistent across the whole doc: 推荐→green · 风险/错误→red · 注意→yellow · 信息→blue · 中性→gray. For metrics, pair color with an explicit `↑↓` or `+/-` (color-blind safe).

## 3. Diagrams via whiteboard (verified)

Embed an **editable** Feishu whiteboard with a `<whiteboard>` tag — *not* a ```` ```mermaid ```` fence:

```xml
<whiteboard type="mermaid">
flowchart LR
  A[请求] --> B{鉴权?}
  B -->|是| C[处理]
  B -->|否| D[401]
</whiteboard>
```

- `type`: `mermaid` | `plantuml` | `svg` | `blank`.
- **Mermaid** covers flow / sequence / class / pie / gantt / mindmap. For custom or other charts use `type="svg"` with a fully self-contained `<svg viewBox="…">…</svg>` (no external refs; avoid `<filter>`/`<radialGradient>`/`<clipPath>` — the board can't render them).
- Inside mermaid, **avoid a literal `<`** (it starts an XML tag) — use directed edges `-->` only; `-->` is valid as text.
- Prefer the whiteboard over a screenshot of a diagram. Confirm it rendered by exporting an image:
  `lark-cli whiteboard +query --whiteboard-token "<token>" --output_as image --output ./preview.png` (the output path must be **relative to the current directory**).

## 4. Content → block map

| You have…                            | Use this block                                   |
| ------------------------------------ | ------------------------------------------------ |
| The main conclusion / a warning      | `<callout>` + emoji + background-color           |
| Rows-and-columns data, a comparison  | `<table>` (`<th background-color="light-gray">`) |
| Two things side by side              | `<grid>` (≤ 3 columns)                           |
| An ordered procedure                 | `<ol><li seq="auto">`                            |
| An unordered set                     | `<ul><li>`                                       |
| Action items / a checklist           | `<checkbox done="false">`                        |
| Code, shell, config, API payload     | `<pre lang="…"><code>`                           |
| A quote or external citation         | `<blockquote>`                                   |
| A process / architecture / hierarchy | `<whiteboard type="mermaid">` (or `svg`)         |
| Math                                 | `<latex>`                                        |
| A boundary between major sections    | `<hr/>`                                          |

## 5. Conversion caveats & escaping

- **Escape the text, never the tags.** In element text: `&` → `&amp;`, `<` → `&lt;` (required); `>` → `&gt;` (safe to always do); a literal newline → `<br/>`. Writing `&lt;p&gt;` for a tag is wrong — keep tags literal, escape only their inner text.
- **Verify styling with `--detail full`.** Colors persist but only show under `--detail full` (not `simple`) — they come back as `rgb(...)`. The one thing that truly degrades: variation-selector emoji (`⚠️`→`💡`); use plain single-codepoint emoji (`✅ 🚨 🔴 ❗`). Fetch back and check (step 6).
- **Long content → stdin**, not a giant inline string: `--content - < body.xml`. The `@file` form only accepts a path **relative to the current directory**. There are **no** `--title` / `--markdown` flags.
- **`docs +update` is block/pattern-based**: `str_replace` / `block_replace` / `block_insert_after` / `block_copy_insert_after` / `block_move_after` / `append` / `overwrite` (no `--title`).
- **Wiki vs docx tokens differ.** Resolve a `feishu.cn/wiki/...` URL to a `docx` token before fetch/update operations that need one.
