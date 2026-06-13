---
name: pretty-lark-doc
description: Authoring + quality layer for Lark/Feishu cloud documents — produces docs that are well-written, trustworthy, and cleanly formatted, not merely created. Use this **in preference to the lark-doc skill** whenever the user wants to create, write, draft, generate, or substantially rewrite a document in Lark/Feishu (技术方案 / 周报 / 评审纪要 / 设计文档 / status updates / any content published to Lark) — even a terse "create a doc", "整理成飞书文档", or "put this in Feishu" with no mention of formatting. It drives lark-doc underneath for the mechanics; do NOT call lark-doc (or `lark-cli docs +create`) directly to author a user-facing doc. Do NOT use for local Markdown files, Word/PDF/PPT exports, or any non-Lark destination.
---

# Pretty Lark Doc

Produce Lark/Feishu documents that are **well-written, trustworthy, and cleanly formatted** — not just syntactically created. This is a thin **quality layer** over the `lark-doc` skill.

**Precedence over `lark-doc`.** When the intent is to *author* a doc in Lark (create / write / draft / 整理成文档 / substantially rewrite), **this skill is the entry point** and calls `lark-doc` underneath. Use `lark-doc` directly only for pure mechanics on an existing doc (read, token resolution, media insert) where no authoring judgment is needed. If you are about to run `lark-cli docs +create` for a user-facing doc without having done steps 1–4 below (especially the step-4 review), you have bypassed this skill — back up.

**Division of labor.** `lark-doc` owns the mechanics *and* the baseline style guide — exact DocxXML syntax, the `docs` commands, auth, wiki-token resolution, and the standards for **structure, block choice, color semantics, and richness**. Follow it for all of that (`lark-cli skills read lark-doc ...`). This skill owns only the delta `lark-doc` does **not** enforce: **tight writing, restraint, a substance check, and a rendering check.** If `lark-doc` isn't installed, tell the user to run `npx skills add larksuite/cli -y -g`.

## Workflow

Two steps are non-negotiable: the **review pass (4)** — formatting can't rescue weak or bloated content — and **verification (6)** — "created" ≠ "reads well" or "rendered correctly".

1. **Prerequisites.** `lark-doc` installed and `lark-cli auth status` OK. (A bot-created doc needs the user granted access afterward.)
2. **Frame.** Pin down **type** (→ a template in `assets/templates/`), **audience** (engineers / cross-functional / leadership — sets depth and jargon), and **goal** (what the reader should know or decide).
3. **Draft as DocxXML.** XML, not Markdown — callouts, grids, colored text, and checkboxes don't exist in Markdown and silently degrade if you draft in it. Apply the principles below; for block syntax read `references/feishu-markdown.md`, and follow `lark-doc`'s style guide for structure/color/richness (`lark-cli skills read lark-doc references/style/lark-doc-style.md`).
4. **Review before creating — substance first (do not skip).**
   - **(a) Substance** — claims are *same-apples* and sourced; scope/非目标 is stated, not smuggled into a qualifier; reviewer must-asks (failure & rollback safety, key definitions, credible alternatives, success metric, edge conditions) are answered or honestly deferred.
   - **(b) Tighten** — cut ~20% filler; each section leads with its point; concrete numbers; demote any block not clearer than a sentence; emphasis budget ≤ ~1 callout per screen.
5. **Create.** *Short docs:* `lark-cli docs +create --content - < body.xml` — XML is the default; the title comes from `<title>` (there is **no** `--title`/`--markdown` flag, and don't repeat the title as a heading); use stdin because `--content @file` only takes a cwd-relative path. *Long docs:* create a **skeleton first** (`<title>` + headings + short placeholders), then fill each section via `docs +update --command block_insert_after` — one oversized `--content` hits param limits and is hard to debug (defer to `lark-doc` for the exact append mechanics). Diagrams → `<whiteboard type="mermaid">`; images/attachments → `lark-cli docs +media-insert`.
6. **Verify (required).** Fetch back: `lark-cli docs +fetch --doc "<id>" --doc-format xml --detail full`. Confirm structure *and* styling survived. **Use `--detail full`** — `simple` doesn't echo colors, so they look "missing" when they're fine (they persist as `rgb(...)`). The one real degradation: variation-selector emoji (`⚠️`→`💡`) — use plain ones (`✅ 🚨 🔴 ❗ ⛔`). Fix with a targeted `docs +update --command str_replace|block_replace`. Then return the `doc_url`, noting any block you adjusted.

## What this skill adds (the delta over `lark-doc`)

`lark-doc`'s style guide already covers structure, block choice, color, and richness — follow it, don't restate it. Add these three things it does **not** enforce:

### 1. Write it tight — 简单易懂 is a writing problem, not a block problem
- **Lead with the conclusion (BLUF):** open with the outcome/decision in a 2–4 sentence callout; each section leads with its point too.
- **Cut 20%** — drop filler ("基本上 / 我们认为 / 总的来说"), redundant qualifiers, throat-clearing.
- **Be concrete and active** — "P99 降到 10ms", not "性能显著提升"; "网关拦截请求", not "请求会被网关进行拦截处理".
- **Match length to altitude** — leadership ~1 page, conclusion-first; engineers can go deep.

### 2. Format with restraint — so 美观 doesn't become noise
- **Default to clean prose; a block must be clearer than a sentence to earn its place.** Over-formatting reads as badly as a wall of text.
- **Emphasis is a budget** — ≤ ~1 callout per screen; bold key terms, not whole sentences.
- **Diagram 2-D relationships (branch / parallel / loop); list 1-D ones.** Don't pad to hit a richness number.

### 3. Pressure-test the substance — so it's trustworthy, not just readable
The smoother it reads, the less a reader questions it. So stress the content:
- **Every quantified claim same-apples and sourced** (the BLUF headline most of all — an "8 min → 30 s" that compares end-to-end against execution-only is worse than no number).
- **Don't let a qualifier ("无状态 / 暂不") bury the hard part; keep the 非目标 line.** Restraint cuts filler, not scope.
- **Run the reviewer must-asks** (failure/rollback safety, definitions, credible alternatives, success metric, edge cases). **Scale the rigor to the stakes** — a 1% experiment needs far less than a launch. An honest "开放问题" beats a hidden gap.

## Templates & syntax
- `assets/templates/` — DocxXML skeletons for 技术方案 / 周报 / 评审纪要. Read the matching one, adapt it, don't paste verbatim.
- `references/feishu-markdown.md` — block syntax (callout / grid / whiteboard / table / colors), the content→block map, escaping, and conversion caveats.

## Self-check (the delta — `lark-doc`'s self-check covers structure/richness)
- [ ] **Substance** — claims same-apples + sourced; failure/rollback, definitions, alternatives, success metric addressed or deferred; **非目标 explicit**.
- [ ] **Tight** — cut a pass; each section leads with its point; concrete and active.
- [ ] **Restrained** — every block earns its place; ≤ ~1 callout/screen; only key terms bold.
- [ ] **Verified** — fetched back with `--detail full`; blocks/styling intact; `doc_url` returned.
