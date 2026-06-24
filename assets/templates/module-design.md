<!--
模块设计 / Module Design 模板（DocxXML，默认格式）— 单模块 / 组件的详细设计（LLD）
用法：读取后按实际内容改写，不要原样粘贴。HTML 注释是给你（撰写者）的提示，写入飞书前请删除。
标题由 docs +create 从 <title> 自动提取（无 --title flag）；正文不要再重复标题。
本骨架用 DocxXML，可承载 callout / 表格 / 白板等 Markdown 无法表达的富 block。
适用：已经决定要做、聚焦「这个模块怎么实现、怎么用、怎么维护」的详细设计，读者是实现者与维护者。
全文围绕：职责（做什么 / 边界）· 接口（怎么用）· 实现（怎么造）· 韧性（怎么不出事、可观测、可演进）。
路由区分：要评审拍板「做不做 / 选哪个方案」的工程方案 / 选型 / RFC 用 tech-design.md（决策导向）；
算法 / ML 用 ml-design.md，P/R/F1 分类用 classification-design.md。模块设计是「方案定了之后」的落地细节。

撰写总则（逻辑清晰·易读·美观）：
· 标题用 talking headline：填好内容后把功能性标题改成传达结论的标题（「内部设计」→「内部设计：乐观锁 + 幂等键，并发写不覆盖」），扫标题即可读出全文逻辑。
· 视觉有节奏：图 / 表 / 文交替，连续两个表格之间补一句「所以呢」，链路 / 时序 / 状态机优先用白板图，别整篇全是表。
· 首屏即全貌：TL;DR 高亮块决定第一观感，信息要完整、干净。
· 克制：删掉不适用的章节（无持久化删「数据模型」、全新模块「兼容性与迁移」标 N/A），别为凑齐结构硬填。
-->

<title>___ 模块设计</title>

<!-- BLUF：开头先给全貌——这个模块是什么、边界在哪、怎么用、依赖什么。每行一句，可一眼扫完。
     「不负责」一行别省：模块设计最容易踩的坑是边界含糊，把不属于自己的事悄悄揽进来。 -->
<callout emoji="✅" background-color="light-green" border-color="green">
  <p><b>定位</b>：本模块负责 ___（一句话）；在链路中位于 上游 ___ → 本模块 → 下游 ___。</p>
  <p><b>边界</b>：负责 ___；<b>不负责</b> ___（明确划界，详见「职责边界」）。</p>
  <p><b>接口与依赖</b>：对外提供 ___（核心能力 / API）；强依赖 ___（失败则 ___）、弱依赖 ___（失败则降级 ___）。</p>
  <p><b>产物</b>：代码 ___ · 接口文档 ___ · 监控看板 ___（详见文末「产物与位置」）。</p>
</callout>

<h1>模块定位与职责边界</h1>
<!-- 先一句话说清「这个模块存在的理由」，再用表格把「负责 / 不负责」摆开。
     非职责 = 方案里的「非目标」：划清边界、避免职责蔓延，也帮读者快速判断「这事该不该找它」。 -->
<p><b>一句话定位</b>：___。</p>
<table>
  <thead><tr><th background-color="light-gray">类型</th><th background-color="light-gray">内容</th></tr></thead>
  <tbody>
    <tr><td>核心职责</td><td>___</td></tr>
    <tr><td>核心职责</td><td>___</td></tr>
    <tr><td>非职责（明确不做）</td><td>___（由 ___ 模块负责）</td></tr>
  </tbody>
</table>

<h1>系统上下文与依赖</h1>
<!-- 先画上下文图：上游谁调它、它调下游谁，让读者一眼看清模块在系统里的位置（白板，不用 ```mermaid 围栏）。
     再用表列依赖，重点标「强 / 弱」与「失败影响 + 降级」——这是评估模块韧性的基础。 -->
<whiteboard type="mermaid">
flowchart LR
  U1[上游 ___] --> M[本模块]
  U2[上游 ___] --> M
  M --> D1[下游 ___]
  M --> DB[(存储 ___)]
</whiteboard>
<table>
  <thead><tr><th background-color="light-gray">依赖</th><th background-color="light-gray">类型</th><th background-color="light-gray">用途</th><th background-color="light-gray">失败影响与降级</th></tr></thead>
  <tbody>
    <tr><td>___（如 鉴权服务）</td><td><span text-color="red">强</span></td><td>___</td><td>___（不可用则 ___）</td></tr>
    <tr><td>___（如 缓存）</td><td>弱</td><td>___</td><td>___（不可用则降级到 ___）</td></tr>
  </tbody>
</table>

<h1>对外接口（API 契约）</h1>
<!-- 模块设计的核心：把「怎么用」讲清，让调用方不看代码也能接入。
     每个接口写清 入参 / 出参 / 错误码 / 幂等性——幂等性和错误码最容易漏，却最容易酿成线上事故。 -->
<table>
  <thead><tr><th background-color="light-gray">接口</th><th background-color="light-gray">入参（关键）</th><th background-color="light-gray">出参</th><th background-color="light-gray">错误码</th><th background-color="light-gray">幂等性</th></tr></thead>
  <tbody>
    <tr><td><code>___</code></td><td>___</td><td>___</td><td>___</td><td>___（幂等键 ___ / 非幂等）</td></tr>
  </tbody>
</table>
<!-- 给一个有代表性的请求 / 响应示例，标好语言。 -->
<pre lang="json" caption="___ 请求 / 响应示例"><code>{
  "request":  { "___": "___" },
  "response": { "code": 0, "data": { "___": "___" } }
}</code></pre>

<h1>数据模型与存储</h1>
<!-- 核心实体 / 表结构 + 存储选型与一致性。纯计算、无持久化的模块，本节标「N/A（无状态）」即可。
     一致性别含糊：写清强一致 / 最终一致，以及并发写如何不互相覆盖（详见「内部设计」）。 -->
<pre lang="sql" caption="核心表 / 实体（示例）"><code>CREATE TABLE ___ (
  id          BIGINT PRIMARY KEY,
  ___         VARCHAR(64) NOT NULL,
  version     INT NOT NULL DEFAULT 0,
  updated_at  TIMESTAMP
);</code></pre>
<p><b>存储选型</b>：___（为什么用它）；<b>一致性</b>：___（强 / 最终一致，依据 ___）。</p>

<h1>内部设计</h1>
<!-- 「怎么造」的展开：先用图讲核心流程或状态机，再用表列关键设计点与取舍。
     时序交互用 sequenceDiagram，生命周期用 stateDiagram-v2；mermaid 里别用字面量小于号（会被当成 XML 标签起始）。 -->
<whiteboard type="mermaid">
sequenceDiagram
  participant C as 调用方
  participant M as 本模块
  participant D as 下游 / 存储
  C->>M: 请求(幂等键)
  M->>D: 读取 / 校验
  D-->>M: 结果
  M-->>C: 响应
</whiteboard>

<h2>关键设计点与取舍</h2>
<!-- 每个设计点给「方案 / 取舍 / 原因」，被否的也写。并发 / 一致性 / 边界条件是模块设计最容易出 bug 的地方，重点写。 -->
<table>
  <thead><tr><th background-color="light-gray">设计点</th><th background-color="light-gray">采用方案</th><th background-color="light-gray">取舍 / 原因</th></tr></thead>
  <tbody>
    <tr><td>并发写冲突</td><td>___（如 乐观锁 version）</td><td>___（vs 悲观锁：___）</td></tr>
    <tr><td>幂等 / 去重</td><td>___（如 幂等键 + 唯一索引）</td><td>___</td></tr>
    <tr><td>边界条件 / 异常输入</td><td>___</td><td>___</td></tr>
  </tbody>
</table>

<h1>异常处理与降级</h1>
<!-- 把「出事时怎么办」讲清。最高风险用红色高亮块单列，其余用表。
     务必覆盖：依赖失败、超时、重试是否安全（幂等）、降级路径。 -->
<callout emoji="🚨" background-color="light-red" border-color="red">
  <p><b>最高风险</b>：___。应对：___。</p>
</callout>
<table>
  <thead><tr><th background-color="light-gray">失败场景</th><th background-color="light-gray">影响</th><th background-color="light-gray">处理 / 重试</th><th background-color="light-gray">降级</th></tr></thead>
  <tbody>
    <tr><td>下游超时 / 不可用</td><td>___</td><td>___（重试 N 次 + 退避；重试需幂等）</td><td>___</td></tr>
    <tr><td>非法输入 / 越界</td><td>___</td><td>___（快速失败 + 明确错误码）</td><td>N/A</td></tr>
  </tbody>
</table>

<h1>性能与容量</h1>
<!-- 关键路径的量化目标，别写「性能良好」。给数字、同口径。 -->
<ul>
  <li><b>关键路径时延</b>：___（如 P99 ≤ ___ ms，口径 ___）</li>
  <li><b>吞吐 / 容量</b>：___（如 ___ QPS，峰值 ___）</li>
  <li><b>资源</b>：___（CPU / 内存 / 连接数 / 存储增长）</li>
</ul>

<h1>可观测性与配置</h1>
<!-- 上线后靠什么发现问题、靠什么调。指标 / 日志 / trace / 告警，以及关键配置项与默认值、开关。 -->
<ul>
  <li><b>关键指标</b>：___（QPS / 错误率 / 时延；告警阈值 ___）</li>
  <li><b>日志 / trace</b>：___（关键埋点、traceId 透传）</li>
  <li><b>配置 / 开关</b>：___（配置项 ___ 默认 ___；降级开关 ___）</li>
</ul>

<h1>测试策略</h1>
<!-- 可勾选。契约测试与边界 / 并发用例最容易漏。 -->
<checkbox done="false">单元测试：核心逻辑 / 边界条件，覆盖率 ___</checkbox>
<checkbox done="false">集成 / 契约测试：与上下游接口对齐 ___</checkbox>
<checkbox done="false">并发 / 幂等用例：重复提交、竞态 ___</checkbox>

<h1>兼容性与迁移</h1>
<!-- 改造已有模块才需要；全新模块标 N/A。重点：向后兼容、数据迁移、可灰度可回滚。 -->
<checkbox done="false">向后兼容：接口 / 数据结构变更不破坏现有调用方 ___</checkbox>
<checkbox done="false">数据迁移：___（迁移脚本 / 双写 / 回填）</checkbox>
<checkbox done="false">灰度与回滚：___（开关 / 回滚预案）</checkbox>

<h1>开放问题</h1>
<!-- 尚无定论、需要进一步信息或他人输入的点。诚实留白好过隐藏缺口。 -->
<ol><li seq="auto">___</li></ol>

<hr/>

<h1>产物与位置</h1>
<!-- 交付物去哪找，方便接手与追溯。尚未产出写「待建」，无对应写「N/A」。 -->
<table>
  <thead><tr><th background-color="light-gray">产物</th><th background-color="light-gray">位置 / 链接</th><th background-color="light-gray">状态</th></tr></thead>
  <tbody>
    <tr><td>代码仓库 / 模块路径</td><td>___</td><td>___</td></tr>
    <tr><td>接口文档 / API 契约</td><td>___</td><td>___</td></tr>
    <tr><td>监控看板 / 告警</td><td>___</td><td>___</td></tr>
    <tr><td>测试报告 / 用例</td><td>___</td><td>___</td></tr>
  </tbody>
</table>
