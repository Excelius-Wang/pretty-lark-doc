<!--
算法 / 机器学习方案 模板（DocxXML，默认格式）— 通用 ML 设计文档
用法：读取后按实际内容改写，不要原样粘贴。HTML 注释是撰写提示，写入飞书前删除。
标题由 docs +create 从 <title> 自动提取（无 --title flag）；正文不要再重复标题。
适用：模型 / 特征 / 召回 / 排序 / 生成等算法方案。纯工程方案用 tech-design.md；
若任务核心就是 P/R/F1 的分类，用更聚焦的 classification-design.md。
全文围绕四要素组织：方法 · 指标 · 结论 · 产物。ML 比普通工程方案多三块必答：数据、离线↔线上、可复现性。

撰写总则（逻辑清晰·易读·美观）：
· 标题用 talking headline：填好内容后把功能性标题改成传达结论的标题（「方案设计」→「方案：双塔召回，Recall@50 +8pt」），扫标题即可读出全文逻辑。
· 视觉有节奏：图 / 表 / 文交替，连续两个表格之间补一句「所以呢」，链路 / 架构优先用白板图，别整篇全是表。
· 首屏即全貌：TL;DR 高亮块决定第一观感，信息要完整、干净。
-->

<title>___ 算法方案</title>

<!-- BLUF：先给结论，控制在 4 行内（方法 / 指标 / 结论 / 产物）。指标按任务族选（见下「离线评测」注释），给「基线 → 目标」并写清评测集。
     离线指标只是代理；指标行同时点明线上预期主指标与护栏（guardrail），未实测的标「待 A/B 验证」。 -->
<callout emoji="✅" background-color="light-green" border-color="green">
  <p><b>方法</b>：用 ___（模型 / 特征 / 策略，一句话）解决 ___ 问题。</p>
  <p><b>指标</b>：离线 ___ 从 ___ → ___（评测集 ___）；线上预期主指标 ___（待 A/B 验证、护栏不劣化），详见「离线评测」。</p>
  <p><b>结论</b>：建议 ___（采纳 / 先小流量实验）；需评审拍板 ___ 决策点。</p>
  <p><b>产物</b>：模型 ___ · 代码 ___ · 实验记录 ___（详见文末）。</p>
</callout>

<h1>背景与问题</h1>
<!-- SCQA 钩子：现状 / 基线(已知) → 规则 / 启发式为何不够(冲突) → 为什么用 ML(问题) → 引出方案(答案)。2-4 句。 -->
<p>___</p>

<h1>目标与非目标</h1>
<!-- 目标尽量可量化并区分「离线目标」与「线上业务目标」；非目标划清边界（如本期不做冷启动）。 -->
<table>
  <thead><tr><th background-color="light-gray">类型</th><th background-color="light-gray">内容</th></tr></thead>
  <tbody>
    <tr><td>线上业务目标</td><td>___（如 CTR / CVR / 时长 / 留存）</td></tr>
    <tr><td>离线目标</td><td>___（对应离线指标的目标值）</td></tr>
    <tr><td>非目标</td><td>___</td></tr>
  </tbody>
</table>

<h1>数据与标签</h1>
<!-- ML 文档的可信度从这里开始。务必写清，否则后面的指标都站不住。 -->
<ul>
  <li><b>数据源 / 规模</b>：___（表 / 时间范围 / 样本量）</li>
  <li><b>标签定义</b>：___（正样本如何定义、弱标签 / 人工标注）</li>
  <li><b>训练 / 评测切分</b>：___（时序任务按时间切防穿越；独立样本分层随机切）</li>
  <li><b>分布 / 不均衡</b>：___（正负比、长尾、与线上分布是否一致）</li>
  <li><b>泄漏检查</b>：___（特征是否含标签信息 / 穿越特征已排查）</li>
</ul>

<h1>方案设计</h1>
<!-- 「方法」展开：特征、模型、训练、在线服务链路。务必画图（白板，不用 ```mermaid 围栏）。 -->
<whiteboard type="mermaid">
flowchart LR
  A[样本/特征] --> B[训练]
  B --> C[离线评测]
  C --> D{达标?}
  D -->|是| E[在线服务]
  D -->|否| A
</whiteboard>

<h2>特征 / 模型</h2>
<!-- 特征来源与处理、模型结构与关键超参、训练资源。 -->
<p>___</p>

<h1>离线评测</h1>
<!-- 「指标」展开。先按任务族选对指标，再填表：
     · 二分类 / 检测 → Precision / Recall / F1 / PR-AUC（不均衡优先 PR-AUC，慎用准确率）
     · 排序 / 推荐 → Recall@K / NDCG@K / HitRate / MAP / GAUC
     · 回归 → MAE / RMSE / MAPE
     · 生成 → 人工胜率 / ROUGE / BLEU / 一致性
     指标给「基线 → 目标」，并标注评测集、评测集规模与置信区间（单点值不可信）。同口径对比。方向用 ↑↓。 -->
<table>
  <thead><tr><th background-color="light-gray">指标</th><th background-color="light-gray">基线</th><th background-color="light-gray">目标</th><th background-color="light-gray">评测集 / 规模 / 口径</th></tr></thead>
  <tbody>
    <tr><td>___（主指标）</td><td>___</td><td><span text-color="green">___ ↑</span></td><td>___（含 95% CI ___）</td></tr>
    <tr><td>___（辅助）</td><td>___</td><td><span text-color="green">___ ↑</span></td><td>___</td></tr>
  </tbody>
</table>

<h1>线上实验设计</h1>
<!-- 离线≠线上。说明怎么用 A/B 验证。严格度按风险调整：1% 实验可以简单，全量上线必须严谨。 -->
<ul>
  <li><b>假设</b>：___（预期主指标变化）</li>
  <li><b>分桶 / 流量 / 周期</b>：___（如 5% × 2 周）</li>
  <li><b>主指标</b>：___</li>
  <li><b>护栏指标</b>：___（不可劣化：如时延 / 留存 / 投诉）</li>
  <li><b>显著性</b>：___（样本量是否足够、置信水平）</li>
</ul>

<hr/>

<h1>备选方案与取舍</h1>
<table>
  <thead><tr><th background-color="light-gray">方案</th><th background-color="light-gray">优点</th><th background-color="light-gray">缺点</th><th background-color="light-gray">是否采纳</th></tr></thead>
  <tbody>
    <tr><td>方案 A</td><td>___</td><td>___</td><td>✅</td></tr>
    <tr><td>方案 B</td><td>___</td><td>___</td><td>❌</td></tr>
  </tbody>
</table>

<h1>风险与应对</h1>
<!-- ML 特有失效模式，别只写通用风险。最高风险用红色高亮块单列。 -->
<callout emoji="🚨" background-color="light-red" border-color="red">
  <p><b>最高风险</b>：___。应对：___。</p>
</callout>
<table>
  <thead><tr><th background-color="light-gray">风险</th><th background-color="light-gray">影响</th><th background-color="light-gray">应对</th></tr></thead>
  <tbody>
    <tr><td>训练/服务特征不一致（train-serve skew）</td><td>___</td><td>___</td></tr>
    <tr><td>数据/特征漂移、反馈回路</td><td>___</td><td>___</td></tr>
    <tr><td>冷启动 / 长尾 / 公平性偏置</td><td>___</td><td>___</td></tr>
  </tbody>
</table>

<h1>资源与成本</h1>
<!-- 上线决策依赖项：训练成本、推理时延 / 吞吐、存储、是否满足在线 SLA。 -->
<ul>
  <li><b>训练成本 / 周期</b>：___</li>
  <li><b>推理时延 P99 / 吞吐</b>：___</li>
  <li><b>存储 / 在线资源</b>：___</li>
</ul>

<h1>上线计划</h1>
<!-- 分阶段，含影子流量 / 灰度 / 回滚 / 监控告警。 -->
<checkbox done="false">影子（shadow）评估：___</checkbox>
<checkbox done="false">小流量 A/B：___（负责人 / 时间）</checkbox>
<checkbox done="false">放量与回滚预案：___（劣化触发条件 ___）</checkbox>

<h1>可复现性</h1>
<!-- 让指标可追溯、可复跑。缺这块，再漂亮的数字也不可信。 -->
<ul>
  <li><b>数据版本 / 快照</b>：___</li>
  <li><b>代码 commit / 配置</b>：___</li>
  <li><b>实验记录</b>：___（MLflow / wandb 等）</li>
  <li><b>随机种子 / 环境</b>：___</li>
</ul>

<h1>结论与决策点</h1>
<!-- 重申建议，列需拍板的决策点（每条可「同意 / 否决」）。与「开放问题」区分。 -->
<p><b>结论</b>：___（建议方案 + 一句话理由）。</p>
<ol>
  <li seq="auto">决策点 ___：方案 A / 方案 B，建议 ___。</li>
  <li seq="auto">决策点 ___：实验流量 / 上线门槛，建议 ___。</li>
</ol>

<h1>开放问题</h1>
<ol><li seq="auto">___</li></ol>

<hr/>

<h1>产物与位置</h1>
<!-- 交付物去哪找。尚未产出写「待建」，无对应写「N/A」。 -->
<table>
  <thead><tr><th background-color="light-gray">产物</th><th background-color="light-gray">位置 / 链接</th><th background-color="light-gray">状态</th></tr></thead>
  <tbody>
    <tr><td>模型 / 版本</td><td>___</td><td>___</td></tr>
    <tr><td>代码仓库 / PR</td><td>___</td><td>___</td></tr>
    <tr><td>数据集 / 特征</td><td>___</td><td>___</td></tr>
    <tr><td>实验记录 / 评测报告</td><td>___</td><td>___</td></tr>
    <tr><td>监控看板 / 报警</td><td>___</td><td>___</td></tr>
  </tbody>
</table>
