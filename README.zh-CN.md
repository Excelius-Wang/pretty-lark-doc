# pretty-lark-doc

[English](README.md) | 简体中文

一个 Claude Code / agent **skill**,把"在飞书/Lark 里建个文档"变成产出**写得好、经得起推敲、排版克制**的文档——而不只是"语法上创建成功"。它是官方 `lark-doc` skill(随 [`lark-cli`](https://github.com/larksuite/cli) 提供)之上的一层**质量层**,底层机制仍交给 lark-doc。

## 它在 lark-doc 之上加了什么

lark-doc 已经管了机制和基础排版规范(DocxXML 语法、结构、选块、配色、丰富度)。本 skill 只补它**没有强制**的那部分:

- **写得紧(简单易懂)**—— 砍废话、每节先给结论、用具体数字、主动语态。
- **排版克制(美观大方)**—— 一个 block 要比一句话更清楚才配出现;每屏 ≤ ~1 个高亮块;二维关系(分叉/并行/循环)才画图、线性步骤用列表。
- **实质自检(可信)**—— 量化论断同口径且有出处、显式写出**非目标**、过一遍 reviewer 必问清单(失败/回滚安全、关键定义、可信备选、成功度量、边界条件)。
- **校验渲染**—— 用 `--detail full` 把文档抓回来核对,因为 Markdown→飞书 的转换会静默丢样式(带变体选择符的 emoji 如 `⚠️` 会被打回 `💡`)。

## 前置依赖

- [`lark-cli`](https://github.com/larksuite/cli) 及其 `lark-doc` skill,本 skill 在底层调用它:
  ```bash
  npx skills add larksuite/cli -y -g
  ```

## 安装

```bash
# 从 GitHub
npx skills add Excelius-Wang/pretty-lark-doc -g

# 或直接 clone 到你的 skills 目录
git clone https://github.com/Excelius-Wang/pretty-lark-doc ~/.claude/skills/pretty-lark-doc
```

## 用法

让你的 agent 去写一篇飞书/Lark 文档即可——比如"把这次评审整理成飞书文档""给 X 写一篇飞书设计文档"。skill 会走:**框定 → 用 DocxXML 起草 → 评审(先实质、后紧凑)→ 创建 → 校验渲染**,最后返回 `doc_url`。

## 目录结构

| 路径 | 用途 |
| --- | --- |
| `SKILL.md` | 工作流 + 写作 / 克制 / 实质三层原则 + 自检清单。 |
| `references/feishu-markdown.md` | DocxXML 选块语法速查(callout / grid / 白板 / 表格 / 配色)+ 转换坑。 |
| `assets/templates/` | 技术方案 / 周报 / 评审纪要的 DocxXML 骨架。 |

## License

[MIT](LICENSE) © 2026 Excelius
