<!--
评审纪要 / 会议纪要 模板（DocxXML，默认格式）
用法：读取后按实际内容改写。HTML 注释是撰写提示，写入飞书前删除。
标题由 docs +create 从 <title> 自动提取（无 --title flag），如「XX 评审纪要 · YYYY-MM-DD」，正文不要重复标题。
纪要的第一价值是“决策”和“行动项”，把它们放最前面，背景放后面。
-->

<title>___ 评审纪要 · YYYY-MM-DD</title>

<!-- BLUF：开头先给结论——今天定了什么。用高亮块。 -->
<callout emoji="✅" background-color="light-green" border-color="green">
  <p><b>会议结论</b>：通过 / 有条件通过 / 未通过 ___；核心决策 ___；下一步 ___。</p>
</callout>

<h1>决策</h1>
<!-- 每条决策独立成行，明确“定了什么 + 依据”。 -->
<ol><li seq="auto">___</li><li seq="auto">___</li></ol>

<h1>行动项</h1>
<!-- 必须有 负责人 + 截止时间，否则纪要等于没开会。用表格或任务清单。 -->
<table>
  <thead><tr><th background-color="light-gray">行动项</th><th background-color="light-gray">负责人</th><th background-color="light-gray">截止</th><th background-color="light-gray">状态</th></tr></thead>
  <tbody>
    <tr><td>___</td><td>@___</td><td>MM-DD</td><td>待开始</td></tr>
    <tr><td>___</td><td>@___</td><td>MM-DD</td><td>待开始</td></tr>
  </tbody>
</table>

<h1>开放问题 / 待跟进</h1>
<!-- 没当场拍板的，记下来并指定跟进人，避免悬空。 -->
<ul><li>___（跟进人：@___）</li></ul>

<h1>讨论与背景</h1>
<!-- 决策理由、争议点、被否决的方案放这里。需要对比时用表格。 -->
<table>
  <thead><tr><th background-color="light-gray">议题</th><th background-color="light-gray">观点 A</th><th background-color="light-gray">观点 B</th><th background-color="light-gray">结论</th></tr></thead>
  <tbody><tr><td>___</td><td>___</td><td>___</td><td>___</td></tr></tbody>
</table>

<hr/>
<!-- 参会人 / 缺席 / 相关材料链接放末尾。 -->
<!-- 参会：___ | 相关文档：___ -->
