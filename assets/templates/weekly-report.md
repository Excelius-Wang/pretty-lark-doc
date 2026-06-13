<!--
周报 / Weekly Report 模板（DocxXML，默认格式）
用法：读取后按实际内容改写。HTML 注释是撰写提示，写入飞书前删除。
标题由 docs +create 从 <title> 自动提取（无 --title flag），如「XX 团队周报 · 第 N 周」，正文不要重复标题。
-->

<title>___ 团队周报 · 第 N 周</title>

<!-- BLUF：开头一段「本周要点」，用高亮块。 -->
<callout emoji="✅" background-color="light-green" border-color="green">
  <p><b>本周要点</b>：完成 ___，推进 ___，最大风险是 ___，需要 ___ 支持。</p>
</callout>

<h1>进展 vs. 计划</h1>
<!-- 表格对照“计划 / 实际 / 状态”，一眼看清是否 on track。 -->
<table>
  <thead><tr><th background-color="light-gray">事项</th><th background-color="light-gray">计划</th><th background-color="light-gray">实际</th><th background-color="light-gray">状态</th></tr></thead>
  <tbody>
    <tr><td>___</td><td>___</td><td>___</td><td>✅ 完成</td></tr>
    <tr><td>___</td><td>___</td><td>___</td><td>🟡 进行中</td></tr>
    <tr><td>___</td><td>___</td><td>___</td><td>🔴 阻塞</td></tr>
  </tbody>
</table>

<h1>关键指标</h1>
<!-- 有数据就放表格；并在表格上下用一句话说明“所以呢”。指标方向用 ↑↓ 标注（色觉友好）。 -->
<table>
  <thead><tr><th background-color="light-gray">指标</th><th background-color="light-gray">上周</th><th background-color="light-gray">本周</th><th background-color="light-gray">环比</th></tr></thead>
  <tbody><tr><td>___</td><td>___</td><td>___</td><td><span text-color="green">___ ↑</span></td></tr></tbody>
</table>

<h1>阻塞与需要的支持</h1>
<!-- 明确“卡在哪、需要谁、何时要”。阻塞项用高亮块强调（风险语义 → 红）。 -->
<callout emoji="🚨" background-color="light-red" border-color="red">
  <p>___（需要：___ / 期望时间：___）</p>
</callout>

<h1>下周计划</h1>
<checkbox done="false">___</checkbox>
<checkbox done="false">___</checkbox>

<hr/>
<!-- 可选：附录放链接（相关文档、看板、PR），不要把背景长文塞进正文。 -->
<!-- 相关链接：设计文档 ___ | 看板 ___ -->
