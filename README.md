# pretty-lark-doc

English | [简体中文](README.zh-CN.md)

A Claude Code / agent **skill** that turns "create a doc in Lark/Feishu" into a *well-written, trustworthy, cleanly-formatted* document — not just a syntactically-created one. It is a thin **quality layer** on top of the official `lark-doc` skill (shipped with [`lark-cli`](https://github.com/larksuite/cli)), which it drives for the mechanics.

## What it adds

`lark-doc` already owns the mechanics and a baseline style guide (DocxXML syntax, structure, block choice, color, richness). This skill adds the delta that guide does **not** enforce:

- **Write it tight** (简单易懂) — cut filler, lead every section with its point, concrete numbers, active voice.
- **Format with restraint** (美观大方) — a block must be clearer than a sentence to earn its place; ≤ ~1 callout per screen; diagram 2-D relationships, list 1-D ones.
- **Pressure-test the substance** (可信) — same-apples claims, an explicit 非目标, and a reviewer must-ask list (failure/rollback safety, definitions, credible alternatives, success metric, edge cases).
- **Verify the rendering** — fetch the doc back with `--detail full`, because Markdown→Feishu conversion can silently drop styling (and variation-selector emoji like `⚠️` revert to `💡`).

## Requirements

- [`lark-cli`](https://github.com/larksuite/cli) with its `lark-doc` skill. This skill calls it underneath:
  ```bash
  npx skills add larksuite/cli -y -g
  ```

## Install

```bash
# from GitHub
npx skills add Excelius-Wang/pretty-lark-doc -g

# or clone straight into your skills dir
git clone https://github.com/Excelius-Wang/pretty-lark-doc ~/.claude/skills/pretty-lark-doc
```

## Usage

Ask your agent to author a Lark/Feishu doc — e.g. "把这次评审整理成飞书文档" or "create a Lark design doc for X". The skill runs: **frame → draft as DocxXML → review (substance, then tighten) → create → verify rendering**, then returns the `doc_url`.

## Layout

| Path | Purpose |
| --- | --- |
| `SKILL.md` | Workflow + the writing / restraint / substance principles + self-check. |
| `references/feishu-markdown.md` | DocxXML block-syntax cheatsheet (callout / grid / whiteboard / table / colors) + conversion caveats. |
| `assets/templates/` | DocxXML skeletons for 技术方案 / 周报 / 评审纪要. |

## License

[MIT](LICENSE) © 2026 Excelius
