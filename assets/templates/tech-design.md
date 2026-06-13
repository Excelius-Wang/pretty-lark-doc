<!--
技术方案 / Design Doc 模板（DocxXML，默认格式）
用法：读取后按实际内容改写，不要原样粘贴。HTML 注释是给你（撰写者）的提示，写入飞书前请删除。
标题由 docs +create 从 <title> 自动提取（无 --title flag）；正文不要再重复标题。
本骨架用 DocxXML，可承载 callout / 表格 / 白板等 Markdown 无法表达的富 block。
-->

<title>___ 技术方案</title>

<!-- BLUF：开头先给结论，用高亮块承载（推荐语义 → 绿） -->
<callout emoji="✅" background-color="light-green" border-color="green">
  <p><b>TL;DR</b>：用 ___ 解决 ___ 问题，预期收益 ___。本文给出设计、备选方案与上线计划，需评审 ___ 决策点。</p>
</callout>

<h1>背景与问题</h1>
<!-- 2-4 句说清现状、痛点、为什么现在要做。避免大段铺垫。 -->
<p>___</p>

<h1>目标与非目标</h1>
<!-- 目标尽量可量化；非目标用于划清边界，避免评审跑题。 -->
<table>
  <thead><tr><th background-color="light-gray">类型</th><th background-color="light-gray">内容</th></tr></thead>
  <tbody>
    <tr><td>目标</td><td>___</td></tr>
    <tr><td>目标</td><td>___</td></tr>
    <tr><td>非目标</td><td>___</td></tr>
  </tbody>
</table>

<h1>方案设计</h1>
<!-- 架构 / 流程务必画图：<whiteboard type="mermaid"> 自动转飞书白板（可编辑矢量图），不要用 ```mermaid 代码围栏。 -->
<whiteboard type="mermaid">
flowchart LR
  A[输入] --> B{判断}
  B -->|是| C[处理]
  B -->|否| D[兜底]
</whiteboard>

<h2>关键设计点</h2>
<!-- 每个设计点一段，必要处用表格列“方案 / 取舍 / 原因”。 -->
<p>___</p>

<h2>接口 / 数据结构</h2>
<!-- 代码、schema、API payload 放代码块并标语言。 -->
<pre lang="json" caption="示例"><code>{ "field": "value" }</code></pre>

<hr/>

<h1>备选方案与取舍</h1>
<!-- 用表格对比，比文字更清晰。 -->
<table>
  <thead><tr><th background-color="light-gray">方案</th><th background-color="light-gray">优点</th><th background-color="light-gray">缺点</th><th background-color="light-gray">是否采纳</th></tr></thead>
  <tbody>
    <tr><td>方案 A</td><td>___</td><td>___</td><td>✅</td></tr>
    <tr><td>方案 B</td><td>___</td><td>___</td><td>❌</td></tr>
  </tbody>
</table>

<h1>风险与应对</h1>
<!-- 高风险项用高亮块单独强调（风险语义 → 红）。 -->
<callout emoji="🚨" background-color="light-red" border-color="red">
  <p><b>最高风险</b>：___。应对：___。</p>
</callout>
<table>
  <thead><tr><th background-color="light-gray">风险</th><th background-color="light-gray">影响</th><th background-color="light-gray">应对</th></tr></thead>
  <tbody><tr><td>___</td><td>___</td><td>___</td></tr></tbody>
</table>

<hr/>

<h1>上线计划</h1>
<!-- 分阶段、可勾选。 -->
<checkbox done="false">阶段一：___（负责人 / 时间）</checkbox>
<checkbox done="false">阶段二：___</checkbox>
<checkbox done="false">灰度与回滚预案：___</checkbox>

<h1>开放问题</h1>
<!-- 留给评审讨论的未决项。 -->
<ol><li seq="auto">___</li></ol>
