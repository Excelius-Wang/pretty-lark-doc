# pretty-lark-doc

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

English | [简体中文](README.zh-CN.md)

`pretty-lark-doc` is a Claude Code / agent skill for creating Lark/Feishu cloud documents that are clear, trustworthy, and cleanly formatted. It sits on top of the official `lark-doc` skill from [`lark-cli`](https://github.com/larksuite/cli): `lark-doc` handles the document mechanics, while this skill adds the authoring quality gate.

Use this skill when the user asks an agent to create, draft, rewrite, or publish a user-facing Lark/Feishu document, such as a design doc, weekly report, review notes, meeting summary, or status update.

## Why this exists

Creating a document is not the same as producing a useful document. Raw `lark-doc` can create blocks correctly, but it does not force the agent to check whether the writing is concise, the claims are defensible, or the rendered document still looks right after conversion.

This skill adds four things:

- **Tighter writing**: lead with the conclusion, cut filler, use concrete numbers, and match depth to the audience.
- **Restrained formatting**: use callouts, tables, grids, diagrams, and colors only when they make the document clearer.
- **Substance review**: check same-apples claims, explicit non-goals, failure and rollback safety, alternatives, success metrics, and edge cases.
- **Rendering verification**: fetch the created document back with `--detail full` and fix dropped or degraded formatting before returning the link.

## Requirements

- [`lark-cli`](https://github.com/larksuite/cli)
- The official `lark-doc` skill installed for `lark-cli`
- A valid `lark-cli` auth session

Install the Lark skills if needed:

```bash
npx skills add larksuite/cli -y -g
```

Check auth:

```bash
lark-cli auth status
```

## Installation

Install from GitHub:

```bash
npx skills add Excelius-Wang/pretty-lark-doc -g
```

Or clone it into your skills directory:

```bash
git clone https://github.com/Excelius-Wang/pretty-lark-doc ~/.claude/skills/pretty-lark-doc
```

## Quick Start

Ask your agent to author a Lark/Feishu document:

```text
Create a Lark design doc for the new billing retry workflow.
```

```text
把这次评审整理成飞书文档。
```

The agent should use this skill before creating the document. It will frame the document, draft DocxXML, review the content, create the document through `lark-doc`, verify the rendered result, and return the final `doc_url`.

## Workflow

1. **Frame the document**: identify the document type, audience, and decision or outcome the reader needs.
2. **Choose a template**: start from `assets/templates/` when the type matches.
3. **Draft in DocxXML**: use XML instead of Markdown so rich Feishu blocks survive conversion. Flowcharts, sequence diagrams, architecture diagrams, state machines, and hierarchy diagrams should default to editable Feishu whiteboards (`<whiteboard type="mermaid">`), not screenshots or Mermaid code fences.
4. **Review before creating**: tighten the writing and pressure-test the substance.
5. **Create the document**: use `lark-cli docs +create --content - < body.xml`.
6. **Verify rendering**: fetch with `lark-cli docs +fetch --doc "<id>" --doc-format xml --detail full`.
7. **Fix targeted issues**: use `docs +update` for degraded blocks, colors, or text.

## Templates

| Template | Use for |
| --- | --- |
| `assets/templates/tech-design.md` | General engineering design docs, RFCs, implementation plans (decision-oriented: alternatives, success metrics, rollout, decision points) |
| `assets/templates/module-design.md` | Detailed design (LLD) of a single module/component for implementers (construction-oriented): responsibilities & boundary, API contract, data model, internal flow, failure & degradation, observability, tests |
| `assets/templates/ml-design.md` | Algorithm / ML design docs (recall, ranking, recommendation, regression, generation); metrics chosen by task family |
| `assets/templates/classification-design.md` | ML design docs judged by **P / R / F1** (binary classification, detection, risk control; multi-class → use `ml-design`); adds confusion matrix, PR/ROC-AUC, threshold selection, error analysis |
| `assets/templates/weekly-report.md` | Team or project weekly reports |
| `assets/templates/review-notes.md` | Review notes, meeting notes, decision records |

Templates are DocxXML skeletons. Adapt them to the specific document and remove authoring comments before publishing.

For a fully worked, filled-in example (not a skeleton), see `assets/examples/module-design.md` — a real module-design document with three rendered whiteboards.

## Repository Layout

| Path | Purpose |
| --- | --- |
| `SKILL.md` | Skill entry point, workflow, quality principles, and self-check |
| `references/feishu-docxxml.md` | Practical DocxXML block reference and conversion caveats |
| `assets/templates/` | Reusable DocxXML document skeletons |
| `assets/examples/` | Fully worked example documents (filled in, for reference) |
| `README.zh-CN.md` | Simplified Chinese README |

## Notes for Maintainers

- Keep `SKILL.md` as the source of truth for agent behavior.
- Keep both README files aligned when changing installation, workflow, or supported use cases.
- Prefer small, practical examples over broad documentation. The authoritative DocxXML syntax belongs to the installed `lark-doc` skill.
- Do not add local build steps unless the skill actually needs executable code.

## Troubleshooting

**The agent created a plain Markdown-looking document.**

It probably skipped DocxXML. Draft in XML when the document needs callouts, grids, colored text, colored table headers, checkboxes, or whiteboards.

**A diagram was inserted as an image or code block.**

Use a Feishu whiteboard block by default: `<whiteboard type="mermaid">`. Keep plain images for non-diagram visuals or diagrams that cannot be represented as editable whiteboards.

**Colors appear missing after fetch.**

Fetch with `--detail full`. Simple detail mode does not echo color attributes even when they persist in the document.

**A warning emoji changed to the default callout icon.**

Avoid variation-selector emoji such as `⚠️`. Use plain emoji such as `🚨`, `✅`, `🔴`, `❗`, or `⛔`.

**The document is too large to create in one command.**

Create a skeleton first, then insert sections with `docs +update --command block_insert_after`.

**Creating a document fails with `20069` or permission denied.**

Creating needs a **user** identity whose scope includes `docx:document:create`, and the Feishu app must be enabled on the open platform — otherwise both user and bot get `20069`. Check that `lark-cli auth status --json` shows `user.status` = `ready`.

## Contributing

Issues and pull requests are welcome. For content changes, please update both English and Simplified Chinese documentation when relevant, and keep examples consistent with `SKILL.md`.

## License

[MIT](LICENSE) © 2026 Excelius
