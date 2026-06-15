<!--
技术方案 / Design Doc 模板（DocxXML，默认格式）— 通用工程设计文档
用法：读取后按实际内容改写，不要原样粘贴。HTML 注释是给你（撰写者）的提示，写入飞书前请删除。
标题由 docs +create 从 <title> 自动提取（无 --title flag）；正文不要再重复标题。
本骨架用 DocxXML，可承载 callout / 表格 / 白板等 Markdown 无法表达的富 block。
全文围绕四要素组织：方法（怎么做）· 指标（如何判定成功）· 结论（建议与决策点）· 产物（交付物在哪）。
算法 / 机器学习类方案请改用 ml-design.md（通用 ML，按任务族选指标）或 classification-design.md（P/R/F1 分类任务）。

撰写总则（逻辑清晰·易读·美观）：
· 标题用 talking headline：填好内容后把功能性标题改成传达结论的标题（「方案设计」→「方案：网关拦截，P99 80ms→10ms」），扫标题即可读出全文逻辑。
· 视觉有节奏：图 / 表 / 文交替，连续两个表格之间补一句「所以呢」，关系 / 流程优先用白板图，别整篇全是表。
· 首屏即全貌：TL;DR 高亮块决定第一观感，信息要完整、干净。
-->

<title>___ 技术方案</title>

<!-- BLUF：开头先给结论。按「方法 → 指标 → 结论 → 产物」四要素组织，每行一句，可一眼扫完。
     指标要给数字、同口径并注明来源；别写「性能显著提升」这类无数字、无口径的话。 -->
<callout emoji="✅" background-color="light-green" border-color="green">
  <p><b>方法</b>：用 ___（核心做法，一句话）解决 ___ 问题。</p>
  <p><b>指标</b>：核心指标 ___ 从 ___ → ___（同口径，来源 ___），详见正文「成功指标」。</p>
  <p><b>结论</b>：建议 ___（采纳 / 有条件采纳）；需评审拍板 ___ 决策点。</p>
  <p><b>产物</b>：代码 ___ · 文档 ___ · 看板 ___（详见文末「产物与位置」）。</p>
</callout>

<h1>背景与问题</h1>
<!-- 用 SCQA 钩子先共识问题再给答案：现状(读者已知) → 出现的痛点(冲突) → 为什么现在要做(问题) → 引出方案(答案)。2-4 句，避免大段铺垫。 -->
<p>___</p>

<h1>目标与非目标</h1>
<!-- 目标尽量可量化（量化部分在「成功指标」展开）；非目标用于划清边界，避免评审跑题。
     注意：克制是砍废话，不是砍范围——别把硬骨头塞进「暂不 / 无状态」之类限定词里。 -->
<table>
  <thead><tr><th background-color="light-gray">类型</th><th background-color="light-gray">内容</th></tr></thead>
  <tbody>
    <tr><td>目标</td><td>___</td></tr>
    <tr><td>目标</td><td>___</td></tr>
    <tr><td>非目标</td><td>___</td></tr>
  </tbody>
</table>

<h1>方案设计</h1>
<!-- 「方法」的展开。架构 / 流程务必画图：<whiteboard type="mermaid"> 自动转飞书白板（可编辑矢量图），不要用 ```mermaid 代码围栏。 -->
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

<h1>成功指标</h1>
<!-- 「指标」的展开：怎么判定方案成功。每个指标给「基线 → 目标」并写清度量口径与来源——
     同一对比必须同口径（如端到端 vs 仅执行不能混比）。TL;DR 的指标取本表核心项，两处须一致。方向用 ↑↓ 标注（色觉友好）。
     工程类常见指标：时延 P99 / 吞吐 / 错误率 / 成本 / 可用性。算法类指标请用 ml-design 或 classification-design 模板。 -->
<table>
  <thead><tr><th background-color="light-gray">指标</th><th background-color="light-gray">基线（现状）</th><th background-color="light-gray">目标</th><th background-color="light-gray">度量口径与来源</th></tr></thead>
  <tbody>
    <tr><td>___（如 P99 时延）</td><td>___</td><td><span text-color="green">___ ↓</span></td><td>___</td></tr>
    <tr><td>___（如错误率 / 成本）</td><td>___</td><td><span text-color="green">___ ↓</span></td><td>___</td></tr>
  </tbody>
</table>

<hr/>

<h1>备选方案与取舍</h1>
<!-- 用表格对比，比文字更清晰。被否决的方案也要列，并给出否决理由。 -->
<table>
  <thead><tr><th background-color="light-gray">方案</th><th background-color="light-gray">优点</th><th background-color="light-gray">缺点</th><th background-color="light-gray">是否采纳</th></tr></thead>
  <tbody>
    <tr><td>方案 A</td><td>___</td><td>___</td><td>✅</td></tr>
    <tr><td>方案 B</td><td>___</td><td>___</td><td>❌</td></tr>
  </tbody>
</table>

<h1>风险与应对</h1>
<!-- 高风险项用高亮块单独强调（风险语义 → 红）。务必覆盖失败与回滚安全性。 -->
<callout emoji="🚨" background-color="light-red" border-color="red">
  <p><b>最高风险</b>：___。应对：___。</p>
</callout>
<table>
  <thead><tr><th background-color="light-gray">风险</th><th background-color="light-gray">影响</th><th background-color="light-gray">应对</th></tr></thead>
  <tbody><tr><td>___</td><td>___</td><td>___</td></tr></tbody>
</table>

<h1>上线计划</h1>
<!-- 分阶段、可勾选，含灰度与回滚预案。 -->
<checkbox done="false">阶段一：___（负责人 / 时间）</checkbox>
<checkbox done="false">阶段二：___</checkbox>
<checkbox done="false">灰度与回滚预案：___</checkbox>

<h1>结论与决策点</h1>
<!-- 「结论」的展开：重申建议，并把需要评审拍板的决策点列清楚——每条要能被「同意 / 否决」。
     与「开放问题」区分：决策点是想现在定的，开放问题是暂无定论的。 -->
<p><b>结论</b>：___（建议采纳的方案 + 一句话理由）。</p>
<ol>
  <li seq="auto">决策点 ___：方案 A / 方案 B，建议 ___。</li>
  <li seq="auto">决策点 ___：是否 ___，建议 ___。</li>
</ol>

<h1>开放问题</h1>
<!-- 留给评审讨论的未决项——暂无明确建议、需要进一步信息的。诚实留白好过隐藏缺口。 -->
<ol><li seq="auto">___</li></ol>

<hr/>

<h1>产物与位置</h1>
<!-- 「产物」的展开：方案相关交付物在哪，方便评审与后续追溯。尚未产出的写「待建」，无对应项写「N/A」。 -->
<table>
  <thead><tr><th background-color="light-gray">产物</th><th background-color="light-gray">位置 / 链接</th><th background-color="light-gray">状态</th></tr></thead>
  <tbody>
    <tr><td>代码仓库 / PR</td><td>___</td><td>___</td></tr>
    <tr><td>设计 / 接口文档</td><td>___</td><td>___</td></tr>
    <tr><td>监控看板 / 报警</td><td>___</td><td>___</td></tr>
    <tr><td>测试 / 验收报告</td><td>___</td><td>___</td></tr>
  </tbody>
</table>
