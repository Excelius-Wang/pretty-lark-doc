<!-- 完整范文 · module-design 实例（支付回调幂等模块）：无占位符、talking headline、三张白板（flowchart / stateDiagram-v2 / sequenceDiagram）。已实跑 create + fetch --detail full + 导白板图，确认渲染正常。用于校准目标水准——写你自己的文档时参照它，不要照抄。 -->

<title>支付回调幂等处理模块 设计</title>

<callout emoji="✅" background-color="light-green" border-color="green">
  <p><b>定位</b>：接收支付网关异步回调，做幂等去重并推进订单支付状态；在链路中位于 上游 支付网关（微信 / 支付宝） → 本模块 → 下游 订单服务。</p>
  <p><b>边界</b>：负责 验签 / 幂等落地 / 状态机推进 / 通知下游；<b>不负责</b> 发起支付、退款撤销、对账差错（由支付发起、对账模块负责）。</p>
  <p><b>接口与依赖</b>：对外提供 <code>POST /pay/callback</code>；强依赖 订单库（失败则返回 fail、由网关重试），弱依赖 Redis 幂等缓存（失败则降级到 DB 唯一索引）。</p>
  <p><b>产物</b>：代码 <code>pay-callback-svc</code> · 接口文档 待建 · 监控看板 待建（详见文末「产物与位置」）。</p>
</callout>

<h1>模块定位：支付回调的幂等入口</h1>
<p><b>一句话定位</b>：把「网关说支付成功了」这件事，安全、幂等地变成订单系统里的一次状态推进——网关会重复投递，本模块保证只生效一次。</p>
<table>
  <thead><tr><th background-color="light-gray">类型</th><th background-color="light-gray">内容</th></tr></thead>
  <tbody>
    <tr><td>核心职责</td><td>回调验签、幂等去重、支付状态机推进、成功后通知下游</td></tr>
    <tr><td>核心职责</td><td>按网关约定返回 success / fail，驱动其重试机制</td></tr>
    <tr><td>非职责（明确不做）</td><td>发起支付（支付发起模块）、退款 / 撤销（退款模块）、对账与差错处理（对账模块）</td></tr>
  </tbody>
</table>

<h1>系统上下文：夹在支付网关与订单服务之间</h1>
<whiteboard type="mermaid">
flowchart LR
  G[支付网关 微信/支付宝] -->|异步回调| M[回调处理模块]
  M -->|快路径去重 SETNX| R[Redis 幂等缓存]
  M -->|同库事务| DB[订单库 MySQL]
  M -->|异步通知| MQ[消息队列]
  MQ --> O[订单服务]
</whiteboard>
<p>三个依赖里只有订单库是<b>强</b>依赖，其余都可降级——这决定了故障时的兜底顺序：</p>
<table>
  <thead><tr><th background-color="light-gray">依赖</th><th background-color="light-gray">类型</th><th background-color="light-gray">用途</th><th background-color="light-gray">失败影响与降级</th></tr></thead>
  <tbody>
    <tr><td>订单库 MySQL</td><td><span text-color="red">强</span></td><td>记录回调、推进订单状态（同库事务）</td><td>不可用则返回 fail，网关重试；因幂等，重试安全</td></tr>
    <tr><td>Redis 幂等缓存</td><td>弱</td><td>快路径去重（SETNX trade_no）</td><td>不可用则降级到 DB 唯一索引，功能不受损、时延略升</td></tr>
    <tr><td>消息队列</td><td>弱</td><td>异步通知下游订单服务</td><td>投递失败落消息表 + 异步重试，不阻塞回调响应</td></tr>
  </tbody>
</table>

<h1>对外接口：回调以 trade_no 为幂等键</h1>
<table>
  <thead><tr><th background-color="light-gray">接口</th><th background-color="light-gray">入参（关键）</th><th background-color="light-gray">出参</th><th background-color="light-gray">错误处理</th><th background-color="light-gray">幂等性</th></tr></thead>
  <tbody>
    <tr><td><code>POST /pay/callback</code></td><td>sign, trade_no, out_trade_no, amount, status</td><td>纯文本 success / fail（网关约定）</td><td>验签失败返回 fail + 告警；其余异常返回 fail 触发重试</td><td>幂等键 trade_no，重复回调返回首次结果</td></tr>
  </tbody>
</table>
<pre lang="json" caption="回调请求 / 响应示例"><code>{
  "request": {
    "trade_no": "2026062311002001",
    "out_trade_no": "ORD-88231",
    "amount": 19900,
    "status": "TRADE_SUCCESS",
    "sign": "a1b2c3..."
  },
  "response": "success"
}</code></pre>

<h1>数据模型：回调记录表 + 唯一索引兜底幂等</h1>
<pre lang="sql" caption="callback_record 核心表"><code>CREATE TABLE callback_record (
  id           BIGINT PRIMARY KEY AUTO_INCREMENT,
  trade_no     VARCHAR(64) NOT NULL,
  out_trade_no VARCHAR(64) NOT NULL,
  status       VARCHAR(16) NOT NULL,
  version      INT NOT NULL DEFAULT 0,
  raw_payload  JSON,
  created_at   TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  UNIQUE KEY uk_trade_no (trade_no)
);</code></pre>
<p><b>存储选型</b>：MySQL——需要事务把「写回调记录 + 推进订单状态」绑定为原子操作，并用唯一约束兜底幂等。<b>一致性</b>：与订单库同库事务，强一致；通知下游经消息队列，最终一致。</p>

<h1>内部设计：SETNX + 乐观锁，重复回调只生效一次</h1>
<p>幂等落在两条不变量上：订单状态<b>只进不退</b>，单次回调<b>恰好生效一次</b>。先看状态视角，再看单次回调的处理时序。</p>
<whiteboard type="mermaid">
stateDiagram-v2
  [*] --> 待支付
  待支付 --> 已支付: 成功回调（验签 + 金额一致）
  待支付 --> 支付失败: 失败回调
  支付失败 --> 已支付: 后续成功回调（补单）
  已支付 --> 已支付: 重复回调（幂等忽略，返回首次结果）
  已支付 --> [*]
</whiteboard>
<p>状态机决定「能不能推进」，下面的时序决定「单次回调怎么走」——首次落地、重复直接返回首次结果：</p>
<whiteboard type="mermaid">
sequenceDiagram
  participant G as 支付网关
  participant M as 本模块
  participant R as Redis
  participant D as 订单库
  participant Q as 消息队列
  G->>M: 回调（trade_no, sign）
  M->>M: 验签 + 金额校验
  M->>R: SETNX trade_no
  R-->>M: 是否首次
  alt 首次回调
    M->>D: 事务（插入 record + 乐观锁更新订单）
    D-->>M: 成功
    M->>Q: 投递下游通知
    M-->>G: success
  else 重复回调（已落地）
    M-->>G: success（返回首次结果）
  end
</whiteboard>

<h2>关键设计点与取舍</h2>
<table>
  <thead><tr><th background-color="light-gray">设计点</th><th background-color="light-gray">采用方案</th><th background-color="light-gray">取舍 / 原因</th></tr></thead>
  <tbody>
    <tr><td>重复回调去重</td><td>Redis SETNX 快路径 + DB 唯一索引兜底</td><td>纯 DB 也能幂等，加 Redis 减少无谓事务；Redis 挂了仍有 DB 兜底，不丢正确性</td></tr>
    <tr><td>状态机只进不退</td><td>乐观锁 version，已是终态直接忽略</td><td>对比悲观锁：回调并发量大，乐观锁冲突重试成本更低</td></tr>
    <tr><td>金额防篡改</td><td>验签 + 回调金额与订单应付金额比对</td><td>仅验签不够，需防渠道侧金额不一致</td></tr>
  </tbody>
</table>

<h1>异常与降级：对网关重试安全，缓存挂了走 DB 兜底</h1>
<callout emoji="🚨" background-color="light-red" border-color="red">
  <p><b>最高风险</b>：重复回调导致重复发货 / 重复加款。应对：trade_no 唯一索引 + 状态机幂等，保证 exactly-once 落地，重复回调返回首次结果。</p>
</callout>
<table>
  <thead><tr><th background-color="light-gray">失败场景</th><th background-color="light-gray">影响</th><th background-color="light-gray">处理 / 重试</th><th background-color="light-gray">降级</th></tr></thead>
  <tbody>
    <tr><td>Redis 不可用</td><td>快路径去重失效</td><td>跳过 SETNX，直接走 DB 事务</td><td>DB 唯一索引兜底，功能不受损</td></tr>
    <tr><td>订单库超时</td><td>本次回调未落地</td><td>返回 fail，网关按策略重试</td><td>因幂等，重试安全，不会重复推进</td></tr>
    <tr><td>验签失败 / 金额不符</td><td>疑似伪造或渠道异常</td><td>快速失败 + 告警，不推进状态</td><td>N/A（必须拦截，不可降级）</td></tr>
  </tbody>
</table>

<h1>性能与容量：P99 ≤ 50ms，峰值按 2k QPS 设计</h1>
<ul>
  <li><b>关键路径时延</b>：P99 ≤ 50ms（口径：服务端从收到回调到返回，不含网关网络往返）</li>
  <li><b>吞吐 / 容量</b>：日常约 200 QPS，大促峰值按 2k QPS 设计（单实例约 500 QPS，横向扩展）</li>
  <li><b>资源</b>：无状态服务，按 CPU 扩缩；Redis 幂等键 TTL 24h，内存可控</li>
</ul>

<h1>可观测性与配置</h1>
<ul>
  <li><b>关键指标</b>：回调 QPS、验签失败率、幂等命中率（重复回调占比）、状态推进失败率；告警：验签失败率超过 1% 或状态推进失败率超过 0.5%</li>
  <li><b>日志 / trace</b>：以 trade_no + out_trade_no 串联全链路，traceId 透传到下游</li>
  <li><b>配置 / 开关</b>：幂等缓存 TTL 默认 24h；降级开关 <code>callback.redis.enabled</code>（关闭则强制走 DB 兜底）</li>
</ul>

<h1>测试策略</h1>
<checkbox done="false">单元测试：状态机流转、验签、金额校验的边界条件</checkbox>
<checkbox done="false">集成 / 契约测试：与微信 / 支付宝回调报文格式对齐</checkbox>
<checkbox done="false">并发 / 幂等用例：同一 trade_no 并发 100 次，断言只生效一次</checkbox>

<h1>兼容性与迁移</h1>
<checkbox done="false">向后兼容：保留各渠道旧回调 path 路由到本模块，调用方无感</checkbox>
<checkbox done="false">数据迁移：历史回调记录回填并按 trade_no 去重后再建唯一索引</checkbox>
<checkbox done="false">灰度与回滚：按渠道灰度，异常时关闭 <code>callback.redis.enabled</code> 或路由切回旧逻辑</checkbox>

<h1>开放问题</h1>
<ol><li seq="auto">跨渠道 trade_no 是否全局唯一待网关确认；若不唯一，唯一索引需加 channel 维度（uk_channel_trade_no）。</li></ol>

<hr/>

<h1>产物与位置</h1>
<table>
  <thead><tr><th background-color="light-gray">产物</th><th background-color="light-gray">位置 / 链接</th><th background-color="light-gray">状态</th></tr></thead>
  <tbody>
    <tr><td>代码仓库 / 模块路径</td><td>pay-callback-svc</td><td>待建</td></tr>
    <tr><td>接口文档 / API 契约</td><td>—</td><td>待建</td></tr>
    <tr><td>监控看板 / 告警</td><td>—</td><td>待建</td></tr>
    <tr><td>测试报告 / 用例</td><td>—</td><td>待建</td></tr>
  </tbody>
</table>
