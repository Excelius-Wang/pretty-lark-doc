<!--
分类模型方案 模板（DocxXML，默认格式）— 指标聚焦 P / R / F1
用法：读取后按实际内容改写，不要原样粘贴。HTML 注释是撰写提示，写入飞书前删除。
标题由 docs +create 从 <title> 自动提取（无 --title flag）；正文不要再重复标题。
适用：核心评价指标就是 精确率 / 召回率 / F1 的二分类 / 检测 / 风控等任务。
多分类（需 macro/micro/weighted 与 N×N 混淆矩阵）、召回/排序/回归/生成等用 ml-design.md；纯工程方案用 tech-design.md。
关键纪律：P / R / F1 三项必须成套出现；任何 P/R/F1 都绑定一个阈值（工作点），不写阈值无法对比。

撰写总则（逻辑清晰·易读·美观）：
· 标题用 talking headline：填好内容后把功能性标题改成传达结论的标题（「方案设计」→「方案：XGB + 新特征，F1 0.78→0.85」），扫标题即可读出全文逻辑。
· 视觉有节奏：图 / 表 / 文交替，连续两个表格之间补一句「所以呢」，链路优先用白板图，别整篇全是表。
· 首屏即全貌：TL;DR 高亮块决定第一观感，信息要完整、干净。
-->

<title>___ 分类模型方案</title>

<!-- BLUF：先给结论。指标必须同时给 P / R / F1 三项（各「基线 → 目标」），并注明评测集与阈值。
     TL;DR 的指标取正文「离线评测」表的核心行，两处数值、口径必须一致——这里和正文是对应关系。 -->
<callout emoji="✅" background-color="light-green" border-color="green">
  <p><b>方法</b>：用 ___（模型 / 特征，一句话）做 ___ 分类。</p>
  <p><b>指标</b>：P ___ → ___ / R ___ → ___ / F1 ___ → ___（评测集 ___、阈值 ___），详见「离线评测」。</p>
  <p><b>结论</b>：工作点 ___（如 P≥0.9 下最大化 R）下建议 ___；需评审拍板 ___ 决策点。</p>
  <p><b>产物</b>：模型 ___ · 代码 ___ · 实验记录 ___（详见文末）。</p>
</callout>

<h1>背景与问题</h1>
<!-- SCQA 钩子：现状(如人工规则的 P/R，已知) → 规则为何不够(冲突) → 要分什么(问题) → 引出模型方案(答案)。2-4 句。 -->
<p>___</p>

<h1>目标与非目标</h1>
<!-- 目标必须落到 P/R/F1 的具体值或工作点约束；非目标划清边界。
     工作点很关键：先想清楚业务更怕误报（重 Precision）还是漏报（重 Recall）。 -->
<table>
  <thead><tr><th background-color="light-gray">类型</th><th background-color="light-gray">内容</th></tr></thead>
  <tbody>
    <tr><td>目标</td><td>___（如 F1 ≥ ___，或 P ≥ ___ 下 R 最大）</td></tr>
    <tr><td>工作点取向</td><td>___（更怕误报 → 重 P / 更怕漏报 → 重 R）</td></tr>
    <tr><td>非目标</td><td>___</td></tr>
  </tbody>
</table>

<h1>数据与标签</h1>
<!-- P/R/F1 的可信度全靠这里。类别不均衡会让准确率失真，必须写清正负比。 -->
<ul>
  <li><b>正样本定义</b>：___（什么算正例，边界 case 如何处理）</li>
  <li><b>数据源 / 规模</b>：___（时间范围 / 样本量）</li>
  <li><b>类别分布</b>：___（正：负 = ___，是否不均衡）</li>
  <li><b>训练 / 评测切分</b>：___（时序任务按时间切防穿越；独立样本分层随机切；评测集需反映线上分布）</li>
  <li><b>泄漏检查</b>：___（特征不含标签 / 未来信息）</li>
</ul>

<h1>方案设计</h1>
<!-- 「方法」展开：特征、模型、训练、在线打分链路。务必画图（白板，不用 ```mermaid 围栏），别只用表 / 文。 -->
<whiteboard type="mermaid">
flowchart LR
  A[样本 / 特征] --> B[模型训练]
  B --> C[离线评测 @阈值]
  C --> D[在线打分]
  D --> E{超过阈值?}
  E -->|是| F[判正 / 拦截]
  E -->|否| G[判负 / 放行]
</whiteboard>
<h2>特征 / 模型</h2>
<p>___</p>

<h1>离线评测</h1>
<!-- 核心。P / R / F1 成套给「基线 → 目标」，并写清评测集、阈值、规模、置信区间（单点值不可信）。
     辅助：用 PR-AUC（不均衡优先）/ ROC-AUC 看阈值无关的整体质量；不均衡场景慎用准确率（会虚高）。
     TL;DR 的指标与本表一致。方向用 ↑↓（色觉友好）。 -->
<table>
  <thead><tr><th background-color="light-gray">指标</th><th background-color="light-gray">基线（现状）</th><th background-color="light-gray">目标</th><th background-color="light-gray">评测集 / 阈值 / 规模 / CI</th></tr></thead>
  <tbody>
    <tr><td>精确率 Precision</td><td>___</td><td><span text-color="green">___ ↑</span></td><td>评测集 ___ / 阈值 ___ / N=___ / 95% CI ___</td></tr>
    <tr><td>召回率 Recall</td><td>___</td><td><span text-color="green">___ ↑</span></td><td>同上口径</td></tr>
    <tr><td><b>F1</b></td><td>___</td><td><span text-color="green">___ ↑</span></td><td>P 与 R 的调和平均</td></tr>
    <tr><td>PR-AUC（阈值无关，主）</td><td>___</td><td><span text-color="green">___ ↑</span></td><td>不均衡优先，比 ROC-AUC 更敏感</td></tr>
    <tr><td>ROC-AUC（辅助）</td><td>___</td><td><span text-color="green">___ ↑</span></td><td>___</td></tr>
  </tbody>
</table>

<h2>混淆矩阵（@选定阈值）</h2>
<!-- P/R 来自这里。给出选定阈值下的 TP/FP/FN/TN，让评审能复核 P/R 算得对不对。
     本表仅适用二分类；多分类请用 N×N 混淆矩阵并改用 ml-design.md。 -->
<table>
  <thead><tr><th background-color="light-gray"></th><th background-color="light-gray">预测正</th><th background-color="light-gray">预测负</th></tr></thead>
  <tbody>
    <tr><td>实际正</td><td>TP ___</td><td>FN ___</td></tr>
    <tr><td>实际负</td><td>FP ___</td><td>TN ___</td></tr>
  </tbody>
</table>

<h2>工作点 / 阈值选择</h2>
<!-- 说明阈值怎么定：按业务约束（P≥x 最大化 R，或 F1 最大）在 PR 曲线上取点，给出该点的 P/R/F1。 -->
<p>___（选定阈值 ___，依据 ___）</p>

<h1>误差分析</h1>
<!-- 典型 FP / FN 案例、分群（细分人群 / 类目）表现，找系统性失败。比单一 F1 更能暴露问题。 -->
<table>
  <thead><tr><th background-color="light-gray">错误类型 / 分群</th><th background-color="light-gray">典型案例</th><th background-color="light-gray">疑似原因</th></tr></thead>
  <tbody>
    <tr><td>假正例 FP</td><td>___</td><td>___</td></tr>
    <tr><td>假负例 FN</td><td>___</td><td>___</td></tr>
  </tbody>
</table>

<hr/>

<h1>线上实验设计</h1>
<!-- 离线≠线上。A/B 主指标 + 护栏。严格度按风险调整。 -->
<ul>
  <li><b>分桶 / 流量 / 周期</b>：___</li>
  <li><b>主指标 / 护栏指标</b>：___ / ___（不可劣化）</li>
  <li><b>显著性</b>：___（样本量、置信水平）</li>
</ul>

<h1>备选方案与取舍</h1>
<table>
  <thead><tr><th background-color="light-gray">方案</th><th background-color="light-gray">优点</th><th background-color="light-gray">缺点</th><th background-color="light-gray">是否采纳</th></tr></thead>
  <tbody>
    <tr><td>方案 A</td><td>___</td><td>___</td><td>✅</td></tr>
    <tr><td>方案 B</td><td>___</td><td>___</td><td>❌</td></tr>
  </tbody>
</table>

<h1>风险与应对</h1>
<callout emoji="🚨" background-color="light-red" border-color="red">
  <p><b>最高风险</b>：___。应对：___。</p>
</callout>
<table>
  <thead><tr><th background-color="light-gray">风险</th><th background-color="light-gray">影响</th><th background-color="light-gray">应对</th></tr></thead>
  <tbody>
    <tr><td>类别不均衡 / 阈值漂移</td><td>___</td><td>___</td></tr>
    <tr><td>训练-服务特征不一致、数据漂移</td><td>___</td><td>___</td></tr>
    <tr><td>标签泄漏 / 评测集不代表线上</td><td>___</td><td>___</td></tr>
  </tbody>
</table>

<h1>上线计划</h1>
<checkbox done="false">影子评估 / 小流量 A/B：___（负责人 / 时间）</checkbox>
<checkbox done="false">上线阈值确定：___</checkbox>
<checkbox done="false">放量与回滚预案：___（劣化触发条件 ___）</checkbox>

<h1>结论与决策点</h1>
<p><b>结论</b>：___（建议方案 + 一句话理由）。</p>
<ol>
  <li seq="auto">决策点 ___：上线阈值 / 工作点，建议 ___。</li>
  <li seq="auto">决策点 ___：是否放量，建议 ___。</li>
</ol>

<h1>开放问题</h1>
<ol><li seq="auto">___</li></ol>

<hr/>

<h1>产物与位置</h1>
<table>
  <thead><tr><th background-color="light-gray">产物</th><th background-color="light-gray">位置 / 链接</th><th background-color="light-gray">状态</th></tr></thead>
  <tbody>
    <tr><td>模型 / 版本</td><td>___</td><td>___</td></tr>
    <tr><td>代码仓库 / PR</td><td>___</td><td>___</td></tr>
    <tr><td>实验记录 / 评测报告</td><td>___</td><td>___</td></tr>
    <tr><td>监控看板 / 报警</td><td>___</td><td>___</td></tr>
  </tbody>
</table>
