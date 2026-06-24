# pretty-lark-doc

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

[English](README.md) | 简体中文

`pretty-lark-doc` 是一个 Claude Code / agent skill，用来创建写得清楚、内容可信、排版克制的 Lark/飞书云文档。它位于 [`lark-cli`](https://github.com/larksuite/cli) 官方 `lark-doc` skill 之上：`lark-doc` 负责文档创建和更新机制，本 skill 负责创建前后的质量把关。

当用户要求 agent 创建、起草、重写或发布面向读者的 Lark/飞书文档时，应优先使用本 skill，例如技术方案、周报、评审纪要、会议纪要、状态同步等。

## 为什么需要它

创建成功不等于文档有用。直接使用 `lark-doc` 可以正确创建块，但它不会强制检查文字是否简洁、结论是否可信、量化口径是否一致，也不会要求创建后拉回文档确认渲染结果。

本 skill 补上四件事：

- **写得更紧**：先给结论，砍掉废话，用具体数字，按受众选择深度。
- **排版克制**：只有在更清楚时才使用高亮块、表格、分栏、图和颜色。
- **实质审查**：检查量化口径、非目标、失败与回滚安全、备选方案、成功指标和边界条件。
- **渲染校验**：创建后用 `--detail full` 拉回文档，确认样式没有丢失或劣化，再返回链接。

## 前置要求

- [`lark-cli`](https://github.com/larksuite/cli)
- 已安装 `lark-cli` 官方 `lark-doc` skill
- `lark-cli` 已完成认证

如需安装 Lark skills：

```bash
npx skills add larksuite/cli -y -g
```

检查认证状态：

```bash
lark-cli auth status
```

## 安装

从 GitHub 安装：

```bash
npx skills add Excelius-Wang/pretty-lark-doc -g
```

或直接 clone 到 skills 目录：

```bash
git clone https://github.com/Excelius-Wang/pretty-lark-doc ~/.claude/skills/pretty-lark-doc
```

## 快速开始

让 agent 创建一篇 Lark/飞书文档：

```text
Create a Lark design doc for the new billing retry workflow.
```

```text
把这次评审整理成飞书文档。
```

agent 应先使用本 skill，再真正创建文档。它会框定文档目标、使用 DocxXML 起草、审查内容、通过 `lark-doc` 创建文档、校验渲染结果，最后返回 `doc_url`。

## 工作流

1. **框定文档**：确认文档类型、读者，以及读者需要知道或决定什么。
2. **选择模板**：匹配时优先从 `assets/templates/` 开始。
3. **使用 DocxXML 起草**：不要先写 Markdown 再转换，否则丰富块容易丢表达能力。流程图、时序图、架构图、状态机和层级图默认使用可编辑的飞书画板（`<whiteboard type="mermaid">`），不要用截图或 Mermaid 代码块替代。
4. **创建前审查**：先检查内容可信度，再压缩文字。
5. **创建文档**：使用 `lark-cli docs +create --content - < body.xml`。
6. **校验渲染**：使用 `lark-cli docs +fetch --doc "<id>" --doc-format xml --detail full`。
7. **定点修复**：用 `docs +update` 修复劣化的块、颜色或文字。

## 模板

| 模板 | 适用场景 |
| --- | --- |
| `assets/templates/tech-design.md` | 通用工程技术方案、RFC、实现计划（决策导向：备选方案、成功指标、上线计划、决策点） |
| `assets/templates/module-design.md` | 单个模块 / 组件的详细设计（LLD），面向实现者（构造导向）：职责边界、接口契约、数据模型、内部流程、异常与降级、可观测性、测试 |
| `assets/templates/ml-design.md` | 算法 / ML 方案（召回、排序、推荐、回归、生成）；指标按任务族选取 |
| `assets/templates/classification-design.md` | 以 **P / R / F1** 为核心指标的 ML 方案（二分类、检测、风控；多分类用 `ml-design`）；含混淆矩阵、PR/ROC-AUC、阈值选择、误差分析 |
| `assets/templates/weekly-report.md` | 团队或项目周报 |
| `assets/templates/review-notes.md` | 评审纪要、会议纪要、决策记录 |

模板是 DocxXML 骨架。使用时应按实际内容改写，并在发布前删除写作提示注释。

## 仓库结构

| 路径 | 用途 |
| --- | --- |
| `SKILL.md` | skill 入口、工作流、质量原则和自检清单 |
| `references/feishu-docxxml.md` | 实用 DocxXML 块语法和转换注意事项 |
| `assets/templates/` | 可复用的 DocxXML 文档骨架 |
| `README.md` | 英文 README |

## 维护说明

- `SKILL.md` 是 agent 行为的事实来源。
- 修改安装方式、工作流或适用场景时，同步更新中英文 README。
- 优先保留小而实用的示例；权威 DocxXML 语法由已安装的 `lark-doc` skill 维护。
- 除非 skill 真的需要可执行代码，否则不要增加本地构建步骤。

## 常见问题

**创建出来的文档看起来像普通 Markdown。**

大概率跳过了 DocxXML。需要高亮块、分栏、彩色文字、彩色表头、复选框或白板时，应直接用 XML 起草。

**图被插成了图片或代码块。**

默认使用飞书画板块：`<whiteboard type="mermaid">`。只有非图表类视觉素材，或无法用可编辑画板表达的图，才使用普通图片。

**拉回文档时看不到颜色。**

使用 `--detail full`。默认的 simple detail 不会回显颜色属性，即使颜色已经保存在文档里。

**警告 emoji 被替换成默认高亮块图标。**

避免使用带 variation selector 的 emoji，例如 `⚠️`。可使用 `🚨`、`✅`、`🔴`、`❗`、`⛔`。

**文档太长，一次创建失败。**

先创建包含标题和章节的骨架，再通过 `docs +update --command block_insert_after` 分段插入内容。

## 贡献

欢迎提交 issue 和 pull request。涉及说明文档的改动，请在相关时同步更新英文和简体中文版本，并确保示例与 `SKILL.md` 一致。

## License

[MIT](LICENSE) © 2026 Excelius
