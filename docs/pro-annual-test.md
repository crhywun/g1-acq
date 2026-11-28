# Google One SKU 管理、Pro 12个月优惠测试与采集

**v7.13.0 主模块为 G1 ACQ SKU 管理，响应运行版本为 `g1-acq-v7.13-mappings`。** [本地报告](https://one.google.com/g1-sku-report)保留五套预制映射模板：1 元 Pro · 月付源核对、土区 20TB、土区 30TB、Ultra 75p、2TB / Pro 12个月 90p。**1 元 Pro 模板改用两条月付 `exact`：旧 Pro 月付 `g1.ios.2tb.ai.m` 与已采集的 AI 月付候选 `g1.ios.ai.2tb.m.ip_3mo_75p.acq`，均指向裸 `com.google.gemini.2tb.ai.m.3month_ip_99p`。** 新候选尚缺官方 iOS 同字段绑定和实机身份，套餐归属待核对；模板名称不是价格保证，金额、周期、可售状态看本次购买面板。安装升级保留已有配置，还需在报告点击 **应用模板**。同次保存仅停用仍启用的旧 2TB 前缀 → 同一 Gemini 目标和旧 exact 20TB → 同一 Gemini 目标，保留其他目标、ID、顺序与历史，再合并启用两条月付 exact；土区 20TB / 30TB 原模板保持独立。重新进入实际提供的 **Pro / AI 月付方案页** 后，下一次 `/ai?` 响应使用新规则；五张卡片按当前配置与执行版本显示本模板及每源命中数，零命中、未应用、等待分开。HTTP 改写不证明购买调用或面板已出现。手动编辑、启停、恢复默认、采集源预填和 SKU 候选输入保持。

默认 `discount75` 保留原三条映射：20TB / 30TB 三个月 75p 与 2TB 目标 `g1.2tb.ai.12months_ip_90p`（无 `.acq`）；应用模板或手动保存后为 `custom`，即使模板与默认目标相同。**75p = 75% off、实付 25%、2.5折**；本版命中行 `fallback=false`。历史试用映射来自 [GitHub PR #3](https://github.com/crhywun/g1-acq/pull/3)，贡献者 [@cehomecambodia-lang](https://github.com/cehomecambodia-lang) 提供了 20TB / 30TB Ultra 1个月免费试用 SKU 与三个月 90p 备用方案，其历史含义保留。

采集只读取手机收到的原始匹配响应，**不会主动查询全量 SKU**。最多保留 300 个 ID、最近 10 条脱敏响应事件，时间使用 UTC。旧 ID 再次出现会累加次数；当前报告版本与实际记录版本分别显示，升级不重标历史。

默认 2TB 候选来自用户截图：`$1.99 × 12 months = $23.88/year` 表达连续 12 个月的月度优惠，`90p` 表示 **90% off、约一折**。截图标题 **Pro 5TB** 未改变 `g1.ios.2tb.` 默认源，也未新增 5TB 前缀；截图不证明 iOS 商品、一次年付扣款或实际 5TB 权益。商品名称、周期、初次与续费金额按 App Store 返回记录，权益按 Google One 账户实际显示记录。

## 1. 安装与升级主模块

**G1 ACQ SKU 管理 — 五套预制模板与本地手动映射，保留默认 75p 与 2TB 目标**

```text
https://cdn.jsdelivr.net/gh/crhywun/g1-acq@v7.13.0/g1-acq-ultra-discount.sgmodule
```

| 项目 | 当前主入口 |
|---|---|
| 当前模式 | 默认 `discount75`；应用模板或手动保存后 `custom` |
| 响应脚本 | [g1-acq-discount.js](../g1-acq-discount.js)，与 [g1-acq.js](../g1-acq.js) 实现相同 |
| 响应运行版本 | `g1-acq-v7.13-mappings` |
| 报告脚本 | [g1-sku-report-discount.js](../g1-sku-report-discount.js)，与 [g1-sku-report.js](../g1-sku-report.js) 实现相同 |
| 报告版本 | `v7.13.0` |
| 本地存储 | 沿用 v7.9 的三个 schema 1 键，模板结果写入既有 `g1.sku.mappings.v1`，无数据迁移 |

1. 关闭旧 **G1 ACQ Ultra 75p**、**G1 ACQ**、**G1 ACQ Ultra 一折**、**G1 ACQ Ultra 试用**、独立 **G1 SKU Scan**、**G1 Pro Annual Test** 与其它改写副本。
2. 在小火箭模块管理中添加上方 `v7.13.0` 地址，只启用 **G1 ACQ SKU 管理**。模块包含响应与报告脚本，无需单独添加。
3. 重新连接小火箭，打开[本机报告](https://one.google.com/g1-sku-report)，核对 **本机脚本已接管** 与 **v7.13.0**，在所需模板卡片点击 **应用模板**。安装升级保留已有配置，不自动选择模板；Pro 卡片点击后启用两条月付 exact，停用旧 2TB 前缀与旧 exact 20TB → 同一 Gemini 目标，其他目标保留。
4. 退出已打开或缓存的方案，再进入 Google App 对应方案页；「1 元 Pro · 月付源核对」进入实际提供的 **Pro / AI 月付方案页**，不借用 20TB / 30TB 入口。
5. 刷新报告，核对 **`test.version=g1-acq-v7.13-mappings`**、`test.mappingRevision` / `test.mappingUpdatedAt` / `test.mappingStatus` 与当前配置一致、`test.updatedAt` 为本次时间，再查看卡片本模板 / 每源次数和 **`test.matchedFrom`**。Pro 模板只计 `g1.ios.2tb.ai.m`、候选 `g1.ios.ai.2tb.m.ip_3mo_75p.acq` → `com.google.gemini.2tb.ai.m.3month_ip_99p` 的实际命中。报告徽标只表明报告版本，旧执行记录保持到新记录成功写入；未应用、等待、当前 0 次按第 4.1 节分别判断。

安装入口与四个模块的 **8 个内部 `script-path`** 固定 `@v7.13.0`，旧固定链接需换新地址。历史 `v7.12.0` / `v7.11.0` / `v7.10.0` / `v7.9.0` / `v7.8.0` / `v7.7.0` / `v7.6.0` / `v7.5.0` / `v7.4.0` / `v7.3.0` / `v7.2.0` / `v7.1.0` / `v7.0.0` 和 `pro-annual-test-v1` / `pro-annual-test-v2` 标签保持原样；试用模块仍缺席，旧固定试用链接仍引用原版本。升级沿用三个 schema 1 键，保留已有映射、累计次数、最近 10 次响应和执行记录，不自动迁移配置或改默认，不要求清空记录。

[g1-acq-v7.sgmodule](../g1-acq-v7.sgmodule) 保留名称 **G1 ACQ**；[g1-pro-annual-test.sgmodule](../g1-pro-annual-test.sgmodule) 保留名称 **G1 Pro Annual Test** 和 `/ai?` 响应范围，均引用当前共享响应脚本与同一映射配置。[g1-sku-scan.sgmodule](../g1-sku-scan.sgmodule) 更新 pin 并保留报告请求体配置，[g1-sku-scan.js](../g1-sku-scan.js) 仍为 `pro-annual-test-v2`，只识别原有 `g1.*` / `com.google.one.*`，保持响应原文，没有模式或响应事件字段。[g1-pro-annual-test.js](../g1-pro-annual-test.js) 同样保留历史独立实现；主响应脚本的 Gemini 支持不改变这两个 legacy 文件。使用主入口时兼容入口保持关闭。

四个模块的报告 `http-request` 均保留 `requires-body=1,max-size=0`。原响应匹配、`[Rule]`、`[MITM]` 保留：hostname 仅追加 `one.google.com`，全局拦截 UDP 443，也影响其它 App 的 HTTP/3、速度或连通性。

## 2. 配置 HTTPS 解密

模块要处理 HTTPS 响应，需在当前使用的配置中启用解密并信任证书。

1. 只启用第 1 节的 `v7.13.0` **G1 ACQ SKU 管理**，旧版用户先关闭旧模块并安装新地址，保持小火箭 VPN 连接。
2. 进入 **配置页 → 当前使用配置右侧的 (i) 图标 → HTTPS 解密**，打开解密开关。
3. 已有可用且已受信任的 Shadowrocket 证书时直接沿用；尚未配置证书时，在该页生成并安装证书，再前往 iOS **设置 → 通用 → VPN 与设备管理** 完成描述文件安装。
4. 首次配置证书时，前往 iOS **设置 → 通用 → 关于本机 → 证书信任设置**，开启对应 Shadowrocket 根证书的信任。
5. 在当前配置的 **HTTPS 解密 hostname 列表**中核对 `one.google.com`。模块使用 `%APPEND%` 追加该域名；此处是 HTTPS 解密列表，不是 DNS Hosts。
6. 更改模块开关、升级模块或更改解密配置后重新连接小火箭，再重新进入 Google App 的方案页面。

[配置入口截图](../docs-config-entry-example.png)与 [README 安装步骤](../README.md#安装shadowrocket--小火箭)展示当前配置右侧的 `(i)` 入口；[Ultra 方案截图](../docs-ultra-plans-example.png)展示 20TB 入口示例。两图保留脱敏后的文件名与内容。MITM hostname 覆盖 `one.google.com` 整个域名，脚本读取与改写范围由下文的 URL 匹配限定。

## 3. 浏览方案，被动采集原始 SKU

主入口处理 `one.google.com/` 响应时先采集原始正文，再检查 `/ai?` 改写范围；候选保留改写前原值，非文本只计入非文本诊断，本地报告页、末尾 `/` 和 query 均跳过。

1. 在 Safari 打开[本地报告](https://one.google.com/g1-sku-report)，记录 `scan.version/mode/updatedAt`、`scannedResponses`、`matchedResponses`、`nonTextResponses` 与最新事件时间。
2. 打开 **Google App（彩色 G 图标）→ Google One 管理**，浏览实际提供的 **2TB / Pro、20TB / 30TB、月付、年付与优惠入口**，等待页面加载，无需完成购买。
3. 返回报告刷新比较。`known-skus` 表示收到已保存 ID，次数与发现时间更新；`no-skus` 表示没有识别到候选。计数与时间均未变时，按第 7 节核对缓存、匹配范围与保存日志。
4. 查看候选的完整 ID、次数与发现时间。点采集行的源预填入口可开始添加映射，或点 **导出 JSON** 保存当前结果。

当前主入口的采集识别 `g1.*`、`com.google.one.*` 与 `com.google.gemini.*`，在识别副本中还原 `\u002e`、`\x2e` 与 HTML 点号实体，该还原步骤不修改响应。同一 ID 在单响应或后续响应重复出现均累加次数，次数不是购买次数。

最多保留 **300 个不同 ID**。满额后继续更新已有 ID，超出的新 ID 计入 `omittedCount`；`addedCount>0` 优先为 `new-skus`，只有 `addedCount=0` 且 `omittedCount>0` 才为 `limit-reached`。响应脚本不读取请求头或请求体，不保存完整正文，不查询 App Store，不向外发送采集数据。

## 4. 应用预制模板、编辑规则与核对执行

### 4.1 五套模板，点击直接保存

打开[本地报告](https://one.google.com/g1-sku-report)，在手动规则前的 **预制映射模板** 区域查看以下五张卡片；每张显示标题、说明、匹配方式和完整源 / 目标。

| 顺序与模板 ID | 标题 | 匹配与完整映射 |
|---|---|---|
| 1 · `pro-1yuan` | **1 元 Pro · 月付源核对** | 精确 `exact`：旧 Pro 月付 `g1.ios.2tb.ai.m` → `com.google.gemini.2tb.ai.m.3month_ip_99p`<br>精确 `exact`：AI 月付候选 `g1.ios.ai.2tb.m.ip_3mo_75p.acq` → `com.google.gemini.2tb.ai.m.3month_ip_99p` |
| 2 · `tr-ultra-20tb` | **土区 20TB · 1989.99 里拉记录** | 精确 `exact`：`g1.ios.20tb.m.v2` → `g1.ios.20tb.m` |
| 3 · `tr-ultra-30tb` | **土区 30TB · 2949.99 里拉记录** | 精确 `exact`：`g1.ios.30tb.ai.pro.m` → `g1.ios.30tb.m` |
| 4 · `ultra-75p` | **Ultra 20TB / 30TB · 三个月 75p** | 精确 `exact`：`g1.ios.20tb.m.v2` → `g1.20tb.3months_ip_75p.acq`<br>精确 `exact`：`g1.ios.30tb.ai.pro.m` → `g1.ai.ultra.3months_ip_75p.acq` |
| 5 · `pro-12months-90p` | **2TB / Pro · 12个月 90p** | 前缀 `prefix`：`g1.ios.2tb.` → `g1.2tb.ai.12months_ip_90p` |

「1 元 Pro · 月付源核对」的卡片说明为：**匹配旧 Pro 月付编号与已采集的 AI 月付候选；新候选的套餐归属待实机核对，金额与周期查看购买面板。** 两条源的证据分别记录：

| 月付源 | 已有证据 | 仍需核对 |
|---|---|---|
| `g1.ios.2tb.ai.m` | 早于 PR #8 的 [cuongpv88 固定脚本](https://github.com/cuongpv88/g1-ultra-free/blob/3a4c8864a064a04402a4cddfa79150fa1e7e3869/g1-ultra-free.js#L13-L25)明确标注 Pro 5TB；[Dgkncn 固定脚本](https://github.com/Dgkncn/g1-acq-plus/blob/e4280c20c69fe9dc2d8458de1b652b2a92d7d741/g1-acq.js#L37-L51)列为 Pro 月付。[Google 官方套餐页](https://one.google.com/about/plans?hl=en&gl=US)的 web `g1.2tb.ai` 对应 Pro 5TB，`g1.2tb` 对应 Plus 2TB，为旧命名族提供佐证 | 官方页面未直接给出同一 iOS 字段绑定；本次商品返回、周期、金额和权益仍需实机记录 |
| `g1.ios.ai.2tb.m.ip_3mo_75p.acq` | 已在设备原始采集中出现，也见于[固定公开清单](https://github.com/deveappcool-sys/g1-acq/blob/45091b639646dadb9470138743dcab00fc5258f3/docs/sku-atlas.md#L18-L23) | 仍是 **AI 月付候选**；尚无官方 iOS 同字段绑定或同一原始商品 / 实机身份记录，不能称为已证实 Pro，也不能以命名字序推断套餐归属 |

1 元模板只加入这两条完整月付 exact；年度 `g1.ios.2tb.ai.a`、`g1.ios.ai.2tb.a.ip_1yr_10p.acq`，`helium` 月付 / 年度，历史普通 2TB 优惠与 20TB / 30TB 均未加入。它们仍按原有已启用规则处理，默认 2TB 前缀 90p 和土区两个模板目标保留。

目标保持裸 `com.google.gemini.2tb.ai.m.3month_ip_99p`，不添加 `https://`。[公开 Google App 订阅截图](https://pbs.twimg.com/media/HYC1ZlybcAEMNPQ.jpg?name=orig)显示 `Google - More ways to search`、Pro 5TB、前三个月 `£0.29/月` 和后续 `£18.99/月`；[原帖](https://x.com/radishzz_/status/2125898543567442351)提供讨论上下文。[印度计价截图](https://pbs.twimg.com/media/HYEQZrSbcAAGc8Z.jpg?name=orig)显示 `₹10/月` 与三个月后 `₹1950/月` 的确认前面板，[原帖](https://x.com/leo114119/status/2125999131026210991)给出目标文字。这些图片支持 Google App 低价 Pro 面板或订阅状态存在，缺完整 productId、宿主购买上下文与 StoreKit 交易链，不证明本账号可售，也不证明新候选已确认为 Pro。[Apple QA1329](https://developer.apple.com/library/archive/qa/qa1329/_index.html)区分商品 ID 与 App ID，单凭 `com.google.gemini` 命名不足以断言目标在 Google App 中不可用。

[@duwqijlk](https://github.com/duwqijlk) 在 [PR #8](https://github.com/crhywun/g1-acq/pull/8) 提供 Gemini 目标与旧 `g1.ios.20tb.m.v2` → 该目标的购买记录；旧 Ultra 来源仅保留为历史贡献引用，本次不借用该源。v7.12.0 曾使用宽 2TB 前缀，v7.13.0 改为两条月付 exact；PR #8 不计作两条新规则的实机购买记录。配置、HTTP 改写、原生购买调用、面板、实际商品与账户权益按第 8 节分别填写。

土区 20TB 的卡片说明为：**旧 iOS 20TB 编号；公开记录报价为 1989.99 里拉，实际金额与周期查看 App Store。** [Dgkncn 固定提交](https://github.com/Dgkncn/g1-acq-plus/blob/f0f86c4dc955fcb35aad9c882e60deaf08db4d1f/g1-acq.js#L48-L54) 同时列出完整映射与 **₺1,989.99/月**；[RealTong 固定提交](https://github.com/RealTong/surge-rules/blob/4268e75ddb72411582d5491e3e1b587f3021c49d/g1.js#L13-L18) 也记录同一完整映射。[土耳其 App Store 公开价格列表](https://apps.apple.com/tr/app/google/id284815942)列出 **Google AI Ultra (20 TB) ₺1.989,99**。Apple 列表只给商品名称与金额，未给 productId 或周期；月付表述来自公开脚本，不构成本项目的实际购买、周期或权益验证。

土区 30TB 的[公开历史记录](https://github.com/kawhaiyoung/rule/blob/b9ce20bc11a77762216fb9f1ab0f04371f943290/G1_ACQ_Egern_README.md#L75)记载 `g1.ios.30tb.m` 曾调起 **2949.99 TRY** 报价；同一 Apple 列表列出 **Google AI Ultra (30 TB) ₺2.949,99**。土区 20TB 的 **1989.99 TRY** 与土区 30TB 的 **2949.99 TRY** 均为公开报价记录，本次金额、周期、续费与权益按第 8 节填写。

**1 元 Pro · 月付源核对与土区 20TB / 30TB 可同时使用。** Ultra 75p 同时切换 20TB / 30TB 精确源，两个土区模板各自只切换对应精确源，保留两条月付 exact。「2TB / Pro · 12个月 90p」继续使用宽 `prefix` `g1.ios.2tb.` → `g1.2tb.ai.12months_ip_90p`，不覆盖月付 exact：旧 `g1.ios.2tb.ai.m` 按精确规则优先，新 `g1.ios.ai.2tb.m.ip_3mo_75p.acq` 也不在该前缀内。**切回旧前缀行为时，先在管理页停用两条月付 exact，再应用 Pro 12个月模板**；旧月付源随前缀处理，新 AI 月付候选仅按其他已启用规则处理。75p 为减免 75%、实付 25%；默认 2TB 12个月目标无 `.acq`，商品周期看 App Store 面板。模板目录没有过期 `trial` 或 Ultra 三个月 90p 方案，不重新开启旧试用或 Ultra 一折模式，默认三条保持原样。

1. 点击所需卡片的 **应用模板**，页面立即提交原生 POST 以保存配置，无需再编辑 SKU 或另点保存；成功 **303** 返回报告 GET，提示模板已保存到本机、下一次匹配响应读取新配置，刷新报告不重复提交。安装升级与普通 GET 均不自动应用模板。
2. 保存前重新读取当前配置，按 `(match, from)` 合并：找到同匹配方式和源时更新 `to` 并设 `enabled=true`，保留原 `id` 与列表顺序；缺少时按模板规则顺序追加唯一 ID 的新规则。应用 `pro-1yuan` 先在同次保存中仅将仍启用的旧 `prefix` `g1.ios.2tb.` → `com.google.gemini.2tb.ai.m.3month_ip_99p`、旧 `exact` `g1.ios.20tb.m.v2` → 同一 Gemini 目标设为 `enabled=false`，保留原 ID、顺序与目标，已禁用项保持原样，再合并启用两条月付 exact。停用只针对上述匹配方式 / 源 / 同一 Gemini 目标组合；旧来源采用其他目标时保留其值与启用状态，包括前缀 90p、土区 20TB / 75p / 手动 20TB 目标，未参与月付 exact 合并的其他手动规则与禁用项保持原样。
3. 重复应用同一模板不产生重复规则，成功保存仍生成新 `revision`；模板结果为 `customized=true` / `custom`，即使目标等于内建默认。卡片 **当前规则一致** 比较当前启用规则；Pro 卡片要求两条月付 exact 均以模板目标启用，并且旧 2TB 前缀 → Gemini、旧 exact 20TB → Gemini 均未启用。仅一条 exact 一致或任一旧路由仍启用时不显示该标签。初始默认只对应 Ultra 75p 与 Pro 12个月两张一致卡片；一致标签不表示本次已点击保存或已实际改写响应。
4. 模板合并与旧 Pro 规则停用先在副本中执行并统一校验，禁用规则也计入 **30 条**：满 30 条时更新已有项仍可成功；新增后超过 30 条时整次 400 失败、零写入，原配置保持完整，不留下部分模板或单独停用的旧规则。
5. 旧页面修订或合并读取后、写入前发生冲突时返回 **409**，整次不保存，刷新核对最新规则后重试；读取、写入、回读失败返回 **503**，不显示保存成功，刷新核对实际配置后再提交。`invalid` 时先按第 7 节排查新旧脚本混装，确认当前版本下存储配置损坏后才显式恢复默认重建；`unavailable` 或缺少写入接口时也停用模板控件。
6. 退出已打开或缓存的方案，再进入 Google App 对应方案；Pro 模板进入实际提供的 **Pro / AI 月付方案页**。核对 **`test.version=g1-acq-v7.13-mappings`**、`test.mappingRevision` / `test.mappingUpdatedAt` / `test.mappingStatus` 与当前配置一致、`test.updatedAt` 为本次时间，查看卡片本模板 / 每源次数和 **`test.matchedFrom` 的 `id` / `to`**。只有 `g1.ios.2tb.ai.m` 或候选 `g1.ios.ai.2tb.m.ip_3mo_75p.acq` → Gemini 目标才计入 Pro 模板。**报告版本、当前规则一致、模板已保存、下一响应实际应用、原生购买调用、App Store 面板与账户权益**分别核对，配置保存本身不重写采集与执行历史。

五张卡片均按本模板原始来源和执行记录中明确保存的有效 `to` 统计；缺失或非法 `to` 不计入，JSON与历史表格的旧目标推断保持原样。exact 要求完整源相同，prefix 要求实际源符合该前缀规则；每条规则显示次数，总数按实际命中行只累加一次。其他模板的命中、相同源但不同目标、旧宽前缀产生的普通 2TB → Gemini 均不混入 Pro 计数，不使用全局 `test.hitCount` 替代。

| 卡片状态 | 显示与含义 |
|---|---|
| 当前规则未符合模板 | **当前规则尚未应用此模板。**；不把历史命中套入当前模板 |
| 当前规则一致，但尚无当前配置的新版执行 | **等待当前配置的新响应记录。**；包括无记录、旧版本、旧修订或配置更新时间 / 状态不一致 |
| 配置有效、模板一致，执行版本 / 修订 / 配置更新时间 / 状态均对应当前配置 | **最近响应：本模板命中 N 次。**，并逐条显示源次数；当前零命中显示 **0 次**，各源也显示实际 0 值 |

命中计数只记录 HTTP 字符串替换，不含 StoreKit 商品查询、购买调用、错误码或面板结果。模板一致也不证明对应源已在最新响应中出现；保存成功后等待新响应，与已有当前执行但 0 次是两种状态。

例如按 **Pro → 土区 20TB → Ultra 75p → Pro** 应用：前两步启用两条月付 exact → Gemini 与 20TB 土区目标；Ultra 75p 切换 20TB / 30TB 目标而保留两条月付 exact；最后再应用 Pro 时，20TB 保留 75p。反向先应用土区 20TB 再应用 Pro，也保留两者。应用土区 30TB 只切换 30TB。存在旧 exact 20TB → Gemini 时，应用 Pro 先停用它，随后应用土区 20TB 可原位启用并切换该条目标。应用 Pro 12个月只更新旧 2TB 前缀 90p，不删除或停用两条月付 exact，旧月付 exact 仍优先；要切回旧前缀行为，需在管理页分别停用 `g1.ios.2tb.ai.m`、`g1.ios.ai.2tb.m.ip_3mo_75p.acq` 并保存，再应用 Pro 12个月。新 AI 月付候选不属于旧前缀，不会因此映射为 90p。

**应用模板**合并指定规则并保留其他规则，保持 `custom`；**恢复默认**替换整个规则配置，清除手动差异，回到内建三条 `discount75`。两种操作均不清采集 / 执行记录；模板不引入新存储键、schema 或 JSON 字段。

### 4.2 手动编辑、默认规则与执行记录

1. 在 Safari 打开[本地报告](https://one.google.com/g1-sku-report)，查看当前配置卡片。从采集列表点源预填入口，或直接使用新增规则表单；源与目标输入框会提示已采集 SKU 与模板目标候选，也支持手动输入完整 ID。
2. 选择 **精确 `exact`** 或 **前缀 `prefix`**，填写源和目标 ID，勾选 **启用**，点击该条的 **保存**。已有规则在各自卡片编辑；取消启用后也需保存。
3. **删除**移除该条规则；**恢复默认**恢复下表三条并回到 `discount75`，不清采集 / 执行记录。删除全部规则或全部禁用并保存后为 `custom`，响应原样通过。
4. 保存成功后页面返回固定成功提示；非法输入保留填写值并给出错误。旧页面冲突时先刷新核对新配置，再重新填写提交；保存失败时按反馈核对，未见成功提示不按新规则判断执行。
5. 退出已打开或缓存的方案，重新进入 Google App，使下一次 `/ai?` 响应使用新配置。保存只改配置，最近执行保留到新响应成功写入；比较 `mappingConfig.revision` 与 `test.mappingRevision`，同时核对版本、模式和时间。
6. 从实际方案进入订阅流程，记录 App Store 商品名称、周期、币种、初次与续费金额或实际错误提示，再导出 JSON。默认规则分别核对 2TB / Pro、20TB / 30TB；手动规则核对完整源与实际 `to`。

| 规则字段 | 填写与匹配规则 |
|---|---|
| `match` | `exact` 匹配完整源；`prefix` 匹配带非空后缀的完整 SKU |
| `from` | 精确源为完整 `g1.*` / `com.google.one.*` / `com.google.gemini.*` ID；前缀源以点结尾，命名空间之后仍需有内容 |
| `to` | 完整目标 ID，支持 `g1.*` / `com.google.one.*` / `com.google.gemini.*`，不以点结尾，不与源相同 |
| ID 字符 | 命名空间之后 1–160 个 `[A-Za-z0-9._-]` 字符；前缀也受同一长度限制；源 / 目标表单 `maxlength=178`，容纳 Gemini 的 18 字符命名空间加 160 字符后缀，后缀上限不变；表单会去掉两端空白 |
| `enabled` | 勾选为启用；禁用规则保留在列表，但不参与匹配 |
| `id` | 本机生成的稳定规则标识，编辑或模板合并已有项时保留；不是待改写商品 ID |
| 上限与重复 | 最多 30 条，禁用规则也计数；相同 `match+from` 重复或规则 ID 重复均拒绝 |

**启用的精确规则优先于前缀；多个启用前缀匹配时取最长前缀。** 每个原始 ID 只替换一次，不链式替换目标；例如精确规则 `g1.example.source → g1.example.middle` 与 `g1.example.middle → g1.example.final` 同时存在时，原始 `g1.example.source` 只变成 `g1.example.middle`。累计清单与正文中是否已有目标均不参与目标选择。

主入口匹配 `^https://one\.google\.com/` 响应，先采集，再仅在 `^https://one\.google\.com/ai\?` 中替换完整明文 ID。首次使用或恢复默认时为 `discount75`，三条规则均启用：

| `id` | 匹配方式与源 | 目标 ID |
|---|---|---|
| `default-2tb` | 前缀 `g1.ios.2tb.` | `g1.2tb.ai.12months_ip_90p` |
| `default-20tb` | 精确 `g1.ios.20tb.m.v2` | `g1.20tb.3months_ip_75p.acq` |
| `default-30tb` | 精确 `g1.ios.30tb.ai.pro.m` | `g1.ai.ultra.3months_ip_75p.acq` |

默认 2TB 目标保留 90p、无 `.acq`；20TB / 30TB 保留三个月 75p。默认与手动映射均无备用，`fallback=false`；试用 / 90p / 75p 目标是否出现在原始响应中不改变规则。所有规则未命中、全部禁用或列表为空时原样返回响应；配置损坏或存储读取出错也暂停改写，不假定默认配置。

| 执行字段 | 含义 |
|---|---|
| `test.version` / `test.mode` | 执行时版本与模式；本版 `g1-acq-v7.13-mappings`，正常配置下为 `discount75` 或 `custom`，配置出错时模式为空 |
| `test.mappingRevision` / `test.mappingUpdatedAt` / `test.mappingStatus` | 执行使用的配置修订、配置保存 UTC 时间、配置状态；旧记录缺失时不填入当前配置 |
| `test.matchedFrom` | 每行保留 `{id, to, count, fallback}`，`to` 为实际目标，`count` 为该源次数；本版 `fallback=false` |
| `test.target` / `test.targetOrigin` | 执行时启用且源恰为 `g1.ios.2tb.` 的前缀目标；默认来源 `user-specified`，手动配置为 `user-configured`，缺少该前缀则均为空；精确覆盖目标看各行 `to` |
| `test.hitCount` | 全部规则的总改写数；日志 `hits=N` |
| `test.annualHitCount` | 兼容统计仅计 `g1.ios.2tb.` 开头源：旧月付 `g1.ios.2tb.ai.m` 计入，新候选 `g1.ios.ai.2tb.m.ip_3mo_75p.acq` 不计入；日志 `annualHits=N`，HTML 显示“其中 2TB”；不代表全部 Pro 命中，也不证明年付扣款 |
| `test.updatedAt` | 最近一次执行时间，UTC，与配置保存时间独立 |
| `test.status=rewritten` | 至少一条规则命中 |
| `test.status=no-match` | 没有启用规则命中，响应原样通过 |
| `test.status=non-text` | 正文非文本，响应原样通过 |
| `test.status=config-error` | 配置 `invalid` 或 `unavailable`，暂停改写并保留响应原文 |

`configuredTarget` / `mappingConfig.target` / `test.target` 仍沿用旧 `g1.ios.2tb.` 前缀摘要语义，不汇总两条月付 exact。应用 Pro 停用旧宽前缀 → Gemini 后，当前目标摘要可为空而两条 exact 正常工作；保留默认前缀 90p 时摘要仍为 90p，精确规则的 Gemini 目标看卡片与 `test.matchedFrom[].to`。新卡片只从既有执行数据派生计数，不新增 JSON 字段，也不改变 `annualHitCount` 的兼容语义。

日志前缀 `[g1-acq]` 与运行版本 `g1-acq-v7.13-mappings` 用于定位本次执行，包含模式、映射状态 / 修订、`hits`、`annualHits`；逐行显示 `源 SKU → 实际目标 xN`。采集摘要保留 `status`、`addedCount`、`candidates`、`omittedCount`、`occurrences`、`saved=yes/no`，`saved=yes` 才表示该次记录成功写入。本版没有备用日志，旧 `fallback=实际完整目标编号` 与更早 `fallback=75p` 仍按历史读取。

执行键保存最近一次 `/ai?` 检查，后续零命中或非文本也会更新；应用模板、编辑、删除、恢复规则和升级本身均不改写历史。旧目标推断使用固定默认 2TB 常量，不用新的 `configuredTarget` 回填。历史版本、来源与模式按下表保留：

| 历史版本 | 保留的 2TB 目标 | 目标来源 | Ultra 与模式记录 |
|---|---|---|---|
| `g1-acq-v7.12-mappings` / `g1-acq-v7.11-mappings` / `g1-acq-v7.10-mappings` / `g1-acq-v7.9-mappings` | 保留当时保存的目标，手动配置可与默认不同或为空 | 保留当时值 | 保留 `mode=discount75` / `custom`、原配置修订、实际 `to` 与 `fallback=false`；不按新模板补写；其中 v7.12 的旧 2TB 前缀 → Gemini、v7.11 的旧 20TB → Gemini 命中保留旧入口含义 |
| `g1-acq-v7.8-75p` | `g1.2tb.ai.12months_ip_90p` | `user-specified` | `mode=discount75`，固定三个月 75p，`fallback=false` |
| `g1-acq-v7.7-trial` | `g1.2tb.ai.12months_ip_90p` | `user-specified` | 已保存的 `mode=trial` 保留试用历史含义；默认 1个月试用、备用 90p 的实际行保留 |
| `g1-acq-v7.7-discount` | `g1.2tb.ai.12months_ip_90p` | `user-specified` | 已保存的 `mode=discount` 保留一折历史含义；默认 90p、备用 75p 的实际行保留 |
| `g1-acq-v7.6` | `g1.2tb.ai.12months_ip_90p` | `user-specified` | 保留 1个月试用默认与 90p 备用的实际行；缺失 `mode` 保持空值 |
| `g1-acq-v7.5` | `g1.2tb.ai.12months_ip_90p` | `user-specified` | 保留当时实际行与备用状态；缺失模式保持空值 |
| v7.3 / v7.4 | `g1.2tb.ai.annual_ip_90p.acq` | `user-specified` | 保留当时实际行；缺失模式保持空值 |
| v7.2 | `g1.2tb.ai.annual.3months_ip_90p.acq` | `pattern-derived` | 保留当时实际行；缺失模式保持空值 |
| v1 / v7.1 | `g1.2tb.ai.annual` | `legacy` | 保留普通年付记录；缺失模式保持空值 |

历史 Ultra 完整目标对应如下，仅用于读取旧记录：

| 历史模式 | 20TB 默认 / 备用 | 30TB 默认 / 备用 |
|---|---|---|
| v7.7 `trial` | `g1.20tb.1month_eft.acq` / `g1.20tb.3months_ip_90p.acq` | `g1.ai.ultra.1month_eft.acq` / `g1.ai.ultra.3months_ip_90p.acq` |
| v7.7 `discount` | `g1.20tb.3months_ip_90p.acq` / `g1.20tb.3months_ip_75p.acq` | `g1.ai.ultra.3months_ip_90p.acq` / `g1.ai.ultra.3months_ip_75p.acq` |

相同目标不代表新版本已执行：v7.5 / v7.6 / v7.7 / v7.8 的 2TB 目标与本版默认相同，v7.9 的默认规则也相同，但其手动目标按当时记录保留；旧 v7.7 `discount` 的 75p 备用也与当前默认 Ultra 目标相同。历史 `fallback=true` 表示当时使用备用，HTML 按实际 `to` 显示 75p、90p 或“是”；目标不足时只显示“是”。本版、旧 v7.9 映射规则与 v7.8 固定规则的 `fallback=false` 显示“否”。

历史 `mode` 保留实际值，缺失或未知时为空，旧 `discount` 仍表示一折。旧 v1 仅 2TB 行缺少 `to` 时补为 `g1.2tb.ai.annual`，缺少 `annualHitCount` 时从旧行次数计算，旧行按未使用备用显示。判断实际执行需一起看版本、模式、时间、`to` 与 `fallback`。

`hitCount>0` 且 `annualHitCount=0` 表示有源不以 `g1.ios.2tb.` 开头的规则命中，默认配置下对应 20TB / 30TB；应用 Pro 后也可来自新 AI 月付候选，需看卡片每源次数与 `test.matchedFrom`，不按兼容计数推断套餐身份。采集可识别转义点号，改写只处理 `/ai?` 原文中的完整明文 ID，采集到某个候选不等同于另一响应已命中。

## 5. 本地管理页、JSON、存储与清理

[本地报告](https://one.google.com/g1-sku-report)显示 **Google One SKU 管理**、**本机脚本已接管** 与 **v7.13.0**。页面由手机 Shadowrocket `http-request` 脚本生成，支持末尾 `/` 和 query，直接返回 HTML / JSON，不把请求交给 Google。HTML 仅用原生表单，无页面 JavaScript 或外部资源，也无网络上报。

| 操作 | 结果 |
|---|---|
| 普通报告 GET | 读取当前配置、累计采集与最近执行，不写配置 |
| [导出 JSON](https://one.google.com/g1-sku-report?format=json) | 只读导出当前 `mappingConfig` 与原有 `scan` / `test` 字段 |
| 应用模板 | 点击即 POST 合并保存，只写映射键；成功 303 返回 `/g1-sku-report?notice=preset`，刷新不重复应用 |
| 页面保存 / 删除 / 恢复默认 | POST 到既有报告地址，只写映射键；成功 303 返回报告 GET，刷新不重复提交 |
| [清空采集与测试记录](https://one.google.com/g1-sku-report?clear=1) | 保留旧 GET 清理行为，只清原两键，不清映射 |

表单只接受 `Origin: https://one.google.com`，提供 `Sec-Fetch-Site` 时须为 `same-origin`；正文为 `application/x-www-form-urlencoded`，可带 `charset=UTF-8`，按 UTF-8 字节计算最大 20KB。只读取这三个头与表单正文，不读 Cookie / 认证头。重复、未知参数与非法百分号编码会被拒绝；GET 的 `action` 返回 405 且不改配置，普通链接、query 与 JSON 导出不应用模板，CSP 和同源限制保持原样。成功提示区分模板已保存、手动已保存、已删除、已恢复默认。

模板表单仅接受 `action=preset`、`revision`、`preset` 三个字段，`preset` 只接受第 4.1 节的五个内建 ID；缺字段、未知 ID、重复字段或附加字段返回 400。模板规则来自本机目录，表单不提交自选 SKU 字符串；其他操作附加 `preset` 字段也会被拒绝。卡片 **当前规则一致** 比较当前启用规则；Pro 卡片另要求两条月付 exact 均符合模板且旧 2TB 前缀 → Gemini、旧 exact 20TB → Gemini 均未启用，与 POST 成功提示和实际运行证据分开。

| 提交反馈 | 处理 |
|---|---|
| 400 输入不合要求 / 模板超限 | 手动表单保留转义后的填写值供修正；模板 ID / 字段错误或合并后超过 30 条时整次失败、零写入，原配置不变 |
| 403 来源不匹配 | 从同一手机 Safari 打开完整本地报告，用页面表单提交 |
| 413 正文超过 20KB | 按条填写并保存，源与目标使用完整商品编号 |
| 415 正文类型不匹配 | 使用页面原生表单提交 |
| 409 旧页面冲突 / 保存前修订变化 | 刷新报告核对最新配置，再提交；模板缺少 `revision` 字段为 400，过期修订或保存前冲突为 409 |
| 503 存储读取 / 写入 / 回读失败 | 页面显示失败而非保存成功；刷新核对实际配置，待本地存储恢复再提交 |
| `mappingConfig.status=invalid` | 暂停改写；先按第 7 节统一报告与响应脚本版本，确认当前版下存储 JSON / schema / 规则损坏后，导出并显式恢复默认重建配置 |
| `mappingConfig.status=unavailable` | 读取接口暂未就绪或读取出错，暂停改写；存储恢复后再核对配置 |

数据保存在手机 `$persistentStore`，所有时间使用 UTC；三个键均沿用 v7.9 的 schema 1，应用模板不新增字段，不自动迁移旧配置：

| 键 | 内容 |
|---|---|
| `g1.sku.scan.v1` | 累计候选、次数、时间、实际采集版本与模式、文字 / 命中 / 非文本计数、容量状态与最多 10 条事件 |
| `g1.pro.annual.test.v1` | 最近执行版本与模式、源前缀、命中行、目标与来源、次数、时间和状态，沿用 v7.9 的 `mappingRevision` / `mappingUpdatedAt` / `mappingStatus` |
| `g1.sku.mappings.v1` | `{schemaVersion:1, revision, updatedAt, customized, rules}`，最多 30 条 |

映射键缺失或为空时使用内建默认规则；默认记录 `customized=false`，恢复默认保存空 `rules` 并重新采用内建三条。应用模板或手动保存为 `customized=true`，显式空列表表示关闭全部改写，仍为 `custom`。配置 JSON、schema 或规则损坏属于 `invalid`；读取出错属于 `unavailable`，两者均暂停改写。

| 当前报告 JSON 字段 | 含义 |
|---|---|
| `reportVersion` | 当前报告版本 `v7.13.0`，独立于实际采集与执行版本 |
| `mappingConfig.status` | `default` / `custom` / `invalid` / `unavailable` |
| `mappingConfig.revision` | 当前变更标识；未保存默认值为 `defaults-75p-v1`，每次成功保存生成新的 48 位小写十六进制修订；用于并发检查，不是认证令牌 |
| `mappingConfig.updatedAt` | 当前配置保存的 UTC ISO 时间；未保存默认配置时为空 |
| `mappingConfig.mode` / `customized` | 默认 `discount75` / `false`，模板或手动保存为 `custom` / `true`；配置出错时模式为空 |
| `mappingConfig.rules` / `limit` | 当前规则数组 `{id, match, from, to, enabled}` 与上限 `30` |
| `mappingConfig.target` / `targetOrigin` | 当前启用且源恰为 `g1.ios.2tb.` 的前缀目标；默认 `user-specified`、手动 `user-configured`，缺少此规则时均为空 |
| `mappingConfig.error` | 固定错误提示，不回显损坏存储原文 |
| `configuredMode` / `configuredTarget` | 从当前 `mappingConfig` 派生，默认分别为 `discount75` / `g1.2tb.ai.12months_ip_90p` |

`mappingConfig` 完整字段为 `status, revision, updatedAt, mode, customized, rules, target, targetOrigin, error, limit`。保存只改变配置；`scan.mode` / `test.mode` 取实际记录，缺失或未知时 JSON 为空字符串、HTML 显示未记录，不按当前配置、旧版本或目标推断。v7.9 已有的三项执行配置字段见第 4 节，旧记录缺失时不补入当前值。

| 保存的模式值 | 报告名称 |
|---|---|
| `discount75` | Ultra 三个月 75p（2.5折） |
| `custom` | 手动映射 |
| `trial` | Ultra 1个月免费试用优先（历史模式） |
| `discount` | Ultra 三个月一折优先（历史模式） |
| 空字符串、缺失或未知值 | 未记录 |

采集与执行独立写入两键：`one.google.com/` 响应可更新采集，只有 `/ai?` 检查才更新执行；保存失败保留旧记录。因此当前配置、采集、执行的模式与时间分别核对。只有 `g1-acq-v7.13-mappings` 标为本版采集记录；`g1-acq-v7.12-mappings`、`g1-acq-v7.11-mappings`、`g1-acq-v7.10-mappings`、`g1-acq-v7.9-mappings`、`g1-acq-v7.8-75p`、v7.7 两入口、`g1-acq-v7.6` 和 `pro-annual-test-v2` 保留真实旧版本。未知版本或无记录显示“尚未保存本版采集响应记录”，旧记录缺少响应历史时不补造。

`scan.skus` 每项保留 `{id, count, lastSeen}`。升级继续保留累计记录与最近 10 次响应；事件本身没有模式字段，不套用当前 `scan.mode`。

| 采集字段 | 含义 |
|---|---|
| `scan.version` | 实际采集版本；本版为 `g1-acq-v7.13-mappings`，旧记录保留原值 |
| `scan.mode` | 执行当时的配置模式 `discount75` / `custom`；配置出错时为空；历史模式与缺失值保留，不按当前入口推断 |
| `scan.updatedAt` | 最近一次成功写入采集记录的 UTC 时间 |
| `scan.scannedResponses` | 累计文字响应数；非文本不计入，原有含义不变 |
| `scan.matchedResponses` | 至少识别出一个候选 ID 的累计文字响应数，重复 ID 也算命中 |
| `scan.nonTextResponses` | 累计非文本响应数，与文字响应分开 |
| `scan.recentResponses` | 最多 10 条事件；JSON 旧 → 新保存，超过 10 条移出最旧事件；HTML 新 → 旧展示 |

每条响应事件包含以下字段：

| 事件字段 | 含义 |
|---|---|
| `at` | 本次响应处理时间，UTC |
| `path` | 固定类别路径，仅取 `/`、`/ai`、`/plans`、`/_/…/data/batchexecute` 或 `[其他 one.google.com 路径]` |
| `bodyType` | 响应体类型，不包含正文内容 |
| `status` | 本次响应的采集诊断状态，见下表 |
| `candidateCount` | 本响应内识别出的不同候选 ID 数；同一 ID 多次出现只计 1 个候选 |
| `addedCount` | 本次新加入并实际保留的不同 ID 数 |
| `omittedCount` | 本次因 300 个 ID 容量限制而丢弃的不同 ID 数；同一未保留 ID 多次出现只计 1 次丢弃 |

| `status` | 含义 |
|---|---|
| `new-skus` | `addedCount>0`，本次保留新增 ID；即使 `omittedCount>0` 仍优先使用此状态 |
| `known-skus` | 本次识别出的 ID 均已保存，只更新累计次数与发现时间 |
| `no-skus` | 本次文字响应未识别到候选 ID |
| `limit-reached` | 仅在 `addedCount=0` 且 `omittedCount>0` 时使用：没有新保留 ID，存在容量丢弃 |
| `non-text` | 本次响应不是可采集文字，增加 `nonTextResponses` |

`candidateCount`、`addedCount`、`omittedCount` 按每个响应中的不同 ID 计数；`scan.skus[].count` 累计字符串出现次数，两者含义不同。已有记录缺少 `recentResponses` 时，不根据旧时间或累计计数补造历史；实际处理的新响应才产生事件。最近 10 次响应不代表全部历史。

响应路径固定脱敏：`/_/…/data/batchexecute` 中的 `…` 是固定展示文本，其它路径归入 `[其他 one.google.com 路径]`。事件与导出不保存 query、完整 URL、请求头、响应头、请求体或响应正文。用最后一次响应时间与最近历史对照本次打开方案页的时间。

**清空采集 / 测试记录不清映射，恢复默认不清历史。** `?clear=1` 只清 `g1.sku.scan.v1` 和 `g1.pro.annual.test.v1`，保留 `g1.sku.mappings.v1`；普通报告与导出不清理。需要重置历史时先导出，清理后返回不带 `clear=1` 的[本地报告](https://one.google.com/g1-sku-report)，避免刷新清理地址再次清除新记录。

## 6. 在 iOS 小火箭中采集与验证隐藏 SKU

当前项目运行在 **iPhone + Shadowrocket + Google App** 中。某个方案未显示在界面上，但其 ID 已在匹配原始正文里时，采集仍会提取；服务器未下发的 ID 不会进入清单。

1. 在 Google App 依次浏览实际月付、年付和优惠入口，等待新响应。
2. 在 Safari 查看[本地报告](https://one.google.com/g1-sku-report)中的原始候选，点采集行预填源，按第 4 节填写规则并保存；候选输入只帮助填写，不自动启用规则。
3. 重新进入对应源方案，按当前版本、配置修订 / 更新时间 / 状态与本次 `test.updatedAt` 核对下一响应，再看卡片每源次数和 `test.matchedFrom` 各行的 `id` / `to`。「1 元 Pro · 月付源核对」进入实际提供的 Pro / AI 月付方案，只计旧 `g1.ios.2tb.ai.m` 或候选 `g1.ios.ai.2tb.m.ip_3mo_75p.acq` → Gemini；土区 20TB 则核对 `g1.ios.20tb.m.v2` → `g1.ios.20tb.m`，两者可同时保留。使用默认配置时，2TB 保留 90p，20TB / 30TB 保留 75p，`fallback=false`。
4. 进入原生订阅流程，记录 App Store 实际商品、周期、初次与续费金额或错误提示，再导出 JSON 对照。

原始采集与配置目标分开保存，替换生成的目标不会自动成为已采集 SKU。脚本只处理网络响应，没有直接调用 Google App 内 StoreKit；[`Product.products(for:)`](https://developer.apple.com/documentation/storekit/product/products(for:)) / `SKProductsRequest` 由对应 App 查询已知 ID 的商品，不是全量商品目录。报告没有 StoreKit 请求、购买调用、返回产品或错误字段，命中只描述 HTTP 文本改写；App Store 是否打开、实际商品和购买结果分别记录，未取得设备证据时保持“未记录”。

## 7. 常见结果的判断

**报告显示 v7.13.0，但应用 Gemini 目标后出现 `invalid` / `config-error`，先核对报告与响应是否混装旧版本。** 报告徽标只标识报告脚本，旧 v7.10 响应脚本会把 Gemini 命名空间判为无效；已保存的旧执行记录也会继续显示，需用本次时间与实际执行区分。

1. 打开[JSON 导出](https://one.google.com/g1-sku-report?format=json)，保存当前规则与诊断，记录 `reportVersion`、`mappingConfig.status/revision`、`test.version/mappingStatus/mappingRevision/updatedAt`。报告提示最近执行版本旧时，先重新进入一次对应方案，刷新比较；仅保留旧时间的记录不证明旧 runtime 仍在执行。
2. 只启用第 1 节 `@v7.13.0` 的 **G1 ACQ SKU 管理**，关闭旧模块、独立扫描器和手工添加的重复脚本。打开模块配置，确认响应与报告分别使用以下两个固定地址，再重新连接小火箭：

   ```text
   https://cdn.jsdelivr.net/gh/crhywun/g1-acq@v7.13.0/g1-acq-discount.js
   https://cdn.jsdelivr.net/gh/crhywun/g1-acq@v7.13.0/g1-sku-report-discount.js
   ```

   旧 `@v7.10.0` / `@v7.11.0` / `@v7.12.0` 固定地址需换新后更新模块；只更新报告脚本会留下旧响应 runtime。由旧教程或旧固定地址装入旧版的路径需要逐项检查，报告徽标不替代这一步。
3. 回到本地报告点击 **1 元 Pro · 月付源核对 → 应用模板**。核对 `exact` `g1.ios.2tb.ai.m` 与候选 `exact` `g1.ios.ai.2tb.m.ip_3mo_75p.acq` → `com.google.gemini.2tb.ai.m.3month_ip_99p` 均启用；旧 `prefix` `g1.ios.2tb.` → 同一 Gemini 目标、旧 `exact` `g1.ios.20tb.m.v2` → 同一 Gemini 目标均禁用，ID / 顺序 / 目标保留。其他目标包括前缀 90p、土区 20TB、75p 和手动 20TB 目标保持原值与启用状态。安装、普通 GET、重新连接均不自动完成这次配置保存，无需自动迁移或清空记录。
4. 退出已打开或缓存的方案，再进入实际提供的 **Pro / AI 月付方案页**。刷新报告，确认 `test.version=g1-acq-v7.13-mappings`、执行修订 / 配置更新时间 / 状态与当前配置一致、`test.updatedAt` 为本次时间；在卡片逐条核对两个月付源次数，再在 `test.matchedFrom` 核对完整 `id` 与 Gemini `to`。旧普通 2TB → Gemini 命中不算作这张卡片命中。卡片等待表示尚无当前配置的新记录，当前 0 次表示本次响应未命中这两条规则；其他模板的命中不代替该结果。
5. 本次时间已更新但执行版本仍旧时，继续核对模块下载地址、重复响应脚本与实际开启的入口。时间未更新时，核对页面缓存、`/ai?` 响应、VPN、HTTPS 解密与日志 `saved=no` / `SKU collection unavailable`。原有 `scan.version` / `test.version` 历史值保留，不重标为当前版。
6. 响应与报告已统一当前版、当前 `mappingConfig.status` 仍为 `invalid` 时，保存错误状态与已有规则备份。报告只返回固定错误，不回显损坏的存储原文；确认当前版仍判定 JSON / schema / 规则损坏后显式 **恢复默认**重建映射，再应用模板并从先前有效导出重建手动规则。该操作替换自定义规则，保留采集与执行历史；`unavailable` / 503 按实际存储读取、写入或回读反馈处理。Gemini 原生表单上限由 175 调为 178 是长 ID 边界修正，不作为短 Pro 目标失效的原因。

| 观察结果 | 下一步 |
|---|---|
| Google 普通页、404，缺少本机标识 / `v7.13.0` / 编辑控件 | 核对新模块、VPN、当前配置 `(i) → HTTPS 解密`、`one.google.com` hostname 与证书信任，再粘贴完整报告 URL |
| 当前规则一致 / 模板已保存，但方案仍旧 | 保存仅作用于下一响应；Pro 重新进入实际提供的 Pro / AI 月付方案页，核对执行版本 / 修订 / 配置更新时间 / 状态、本次时间、卡片逐源数和 `test.matchedFrom` 的 `id` / `to` |
| Pro 卡片无当前规则一致 | 核对两条月付 exact 是否都以 Gemini 目标启用，再核对旧 2TB 前缀 → Gemini、旧 exact 20TB → Gemini 是否仍启用；点击应用统一保存，其他目标保留 |
| 卡片显示当前规则尚未应用此模板 | 当前规则与该模板不一致；历史命中不作为当前结果 |
| 卡片显示等待当前配置的新响应记录 | 核对新版执行、修订、配置更新时间与状态，重新进入对应方案取得当前配置的新响应 |
| 卡片显示本模板命中 0 次 | 已有符合当前配置的执行，但该响应未按本模板来源与目标命中；逐源 0 与等待区分 |
| 应用 Pro 12个月后月付仍指向 Gemini | 精确规则优先，Pro 12个月未停用两条月付 exact；切回旧前缀行为需在管理页停用两条 exact 并保存 |
| Pro 两条 exact 启用，但 configuredTarget 为空或仍显示 90p | 该字段仍是旧 2TB 前缀摘要；核对卡片逐源数与 matchedFrom 的实际目标 |
| 模板合并超过 30 条 | 整次 400、零写入；删除不再使用的规则后重试；满 30 条时只更新已有项仍可成功 |
| 当前 `custom`，执行仍为 `discount75` / `trial` / `discount` | 历史未改写；按版本、模式、时间核对新响应是否成功写入 |
| 报告 `v7.13.0`，采集 / 执行仍为 `g1-acq-v7.12-mappings`、`g1-acq-v7.11-mappings`、`g1-acq-v7.10-mappings` 或更早 | 先按旧记录保留理解，重开方案再比时间；新时间仍旧版本时排查混装与重复入口 |
| 采集模式已更新、执行仍旧 | 两键独立写入且范围不同，查看 `/ai?` 是否产生新执行记录 |
| `invalid` / `config-error`，规则含 Gemini 目标 | 先按本节步骤统一报告与响应版本；确认当前版下配置损坏后，导出并显式恢复默认重建 |
| `unavailable` 或 503 保存失败 | 核对本地存储状态；页面未声称保存成功，刷新查看实际配置 |
| 409 冲突 | 旧页面修订过期，刷新核对最新规则后再提交 |
| 规则有误、重复或超过 30 条 | 按保留的输入修正；禁用规则也计入数量与重复检查 |
| 全部禁用或全部删除后 `no-match` | 空规则 / 禁用规则不改写；恢复默认会重建三条启用规则 |
| 只有 `known-skus`、没有新 ID | 本次是重复数据，比较次数、发现时间和最新事件 |
| 三个计数与最新时间均未变 | 先看 `saved=no` / `SKU collection unavailable`，再核对缓存、响应范围、唯一入口与解密配置 |
| 已采集 ID，但没有改写 | 核对规则启用、精确 / 前缀、完整明文 ID 与 `/ai?` 范围，采集与改写范围不同 |
| `hitCount>0`、`annualHitCount=0` | 兼容统计未计入 `g1.ios.2tb.` 开头源；新 AI 月付候选命中也属此情况，查看卡片逐源数和实际行 `id` / `to` |
| 旧模式、旧目标或 `fallback=true` 仍显示 | 按第 4 节历史表读取，不把当前配置套入历史；目标相同不证明新版本运行 |
| `addedCount=0`、`omittedCount>0` | 达到 300 ID 上限；需要清理时先导出，仅清采集 / 执行两键，映射保留 |
| 改写命中但收银台报错、未出现或商品不同 | 分别记录实际 `to`、原生商品返回 / 错误、是否调用购买、面板商品 / 周期 / 金额；HTTP 命中不证明购买调用或面板出现，也不证明本账号可售 |

按“先记录 → 重开实际方案 → 刷新比较”核对活动。重复安装或反复清空不会使服务器下发新 ID；没有模式或响应历史的旧记录保留空值，不补造。

## 8. 实机记录模板

```text
采集 / 测试时间（含时区）：[填写]
iOS / Google App / Shadowrocket 版本：[填写]
唯一开启的主模块：G1 ACQ SKU 管理
主安装地址：https://cdn.jsdelivr.net/gh/crhywun/g1-acq@v7.13.0/g1-acq-ultra-discount.sgmodule
实际模块地址 / 发布版本：[填写；当前 v7.13.0]
预期响应版本：g1-acq-v7.13-mappings
旧模块与独立扫描器关闭情况：[填写实际状态]
重新连接 / 重开方案时间：[填写]
报告本机标识 / reportVersion：[填写实际显示；当前 v7.13.0]
当前 configuredMode / configuredTarget：[填写实际值；默认 discount75 / g1.2tb.ai.12months_ip_90p]
当前 mappingConfig.status / customized / error：[填写]
当前 mappingConfig.revision / updatedAt（UTC）：[填写]
当前 mappingConfig.rules：[填写 {id, match, from, to, enabled}，最多30条]
本次应用模板 / 新增 / 编辑 / 禁用 / 删除 / 恢复操作：[填写实际操作]
本次模板 ID / 标题：[填写 pro-1yuan / tr-ultra-20tb / tr-ultra-30tb / ultra-75p / pro-12months-90p；未用则填未用]
卡片当前规则一致状态：[填写实际显示；Pro要求两条月付exact一致且两种旧Gemini路由均停用；不代表已执行]
本模板诊断状态：[填写尚未应用 / 等待当前配置新响应 / 最近响应本模板命中N次]
每个模板源实际命中数：[逐条填写；当前0次与未应用 / 等待分开]
旧月付 g1.ios.2tb.ai.m→com.google.gemini.2tb.ai.m.3month_ip_99p：[填写启用状态与本次次数]
AI月付候选 g1.ios.ai.2tb.m.ip_3mo_75p.acq→com.google.gemini.2tb.ai.m.3month_ip_99p：[填写启用状态与本次次数；套餐身份未核对则保持候选]
模板合并前后规则 / ID / 顺序 / 启用状态：[填写实际差异与保留项]
旧 prefix g1.ios.2tb.→com.google.gemini.2tb.ai.m.3month_ip_99p：[填写原状态与应用Pro后状态；不存在则记不存在]
旧 exact g1.ios.20tb.m.v2→com.google.gemini.2tb.ai.m.3month_ip_99p：[填写原状态与应用Pro后状态；不存在则记不存在]
其他目标 / 启用状态：[填写前缀90p、土区20TB、75p或手动目标的保留结果]
切回旧前缀行为时两条月付exact停用结果：[填写各条状态；未切回则填未执行]
保存结果 / 错误提示：[填写实际显示；未执行则填未执行]
POST 成功提示 / 303 后页面地址：[填写；失败则记录实际状态码与配置]
保存后重新进入方案时间：[填写]
实际 scan.version / mode / updatedAt（UTC）：[填写实际记录，不重标历史或缺失模式]
打开方案前 / 后 scannedResponses / matchedResponses / nonTextResponses：[填写]
打开方案前 / 后最新响应时间：[填写；缺失则记未记录]
scan.recentResponses：[按 JSON 旧→新填写最多10条；HTML 新→旧；不补写事件模式]
最新事件 at / path / bodyType / status：[填写 UTC 时间、脱敏路径、类型、采集状态]
最新事件 candidateCount / addedCount / omittedCount：[填写]
Google App 实际方案 / 原始完整源 SKU：[填写实际显示与采集值]
默认 2TB 目标：g1.2tb.ai.12months_ip_90p（无 .acq）
默认 20TB / 30TB 目标：g1.20tb.3months_ip_75p.acq / g1.ai.ultra.3months_ip_75p.acq
模板 / 手动规则目标：[填写实际配置；未使用则填默认]
1元Pro预期入口：实际提供的Pro / AI月付方案；不借用Ultra20/30TB源
1元Pro预期映射1：exact g1.ios.2tb.ai.m→com.google.gemini.2tb.ai.m.3month_ip_99p
1元Pro预期映射2：exact g1.ios.ai.2tb.m.ip_3mo_75p.acq→com.google.gemini.2tb.ai.m.3month_ip_99p（AI月付候选，归属待实机核对）
1元Pro排除来源：年度、helium、历史普通2TB优惠、20TB/30TB不加入该模板，仍按其他已有规则处理
土区20TB预期映射：exact g1.ios.20tb.m.v2→g1.ios.20tb.m
土区公开报价参考：20TB 1989.99 TRY；30TB 2949.99 TRY；不作为本次实际金额 / 周期 / 购买 / 权益记录
PR#8历史来源：duwqijlk的旧20TB→Pro入口购买记录，仅作历史引用；不证明两条月付exact或本账号的购买结果
实际 test.version / mode / updatedAt（UTC）：[填写实际记录，不按当前配置补写]
实际 test.mappingRevision / mappingUpdatedAt / mappingStatus：[填写；旧记录缺失则记未记录]
当前配置与实际执行修订是否一致：[填写对照结果]
test.matchedFrom：[逐行填写 {id, to, count, fallback}；本版 false，历史按原值]
test.target / targetOrigin：[填写旧g1.ios.2tb.前缀目标摘要与来源；空值不表示两条exact停用；来源为 user-specified / user-configured / pattern-derived / legacy / 空值]
test.hitCount / annualHitCount / status：[填写总数 / g1.ios.2tb.源改写数 / 状态；annualHitCount计旧2tb.ai.m、不计新ai.2tb.m候选；模板命中看卡片逐源数]
保留的历史版本 / 模式 / 时间 / to / fallback：[填写实际值；缺失保持空值]
日志模式 / 配置状态 / 修订 / saved / hits / annualHits：[填写实际值]
日志 fallback：[本版无备用日志；历史按实际原值]
入口App / 实际负责购买的App：[填写实际观察；未取得记录则填未记录]
StoreKit请求productId / 返回商品 / 错误domain与code：[仅填实际设备记录；HTTP报告没有这些字段；未取得则填未记录]
是否发生原生购买调用：[填写实际设备记录；HTTP命中不证明调用；未取得则填未记录]
App Store 是否拉起 / 商品 / 周期：[填写实际显示]
币种 / 初次金额 / 续费金额 / 错误提示：[填写实际显示；模板名称与公开报价不保证本次价格]
是否完成购买 / 购买后账户权益：[按实际填写；未购买则记未购买]
本轮 JSON 导出文件：[填写]
```

## 9. 离线验证与生成副本

使用 Node.js 22 或更新版本（原生表单 pattern 按 UnicodeSets v 语义验证），在仓库根目录执行：

```sh
node tools/build-variants.cjs --check
node --test tests/*.test.cjs
```

[lib/mapping-config.js](../lib/mapping-config.js) 提供共享配置引擎；[tools/build-variants.cjs](../tools/build-variants.cjs) 把生成区块嵌入 [g1-acq.js](../g1-acq.js) 与 [g1-sku-report.js](../g1-sku-report.js)，再生成 [g1-acq-discount.js](../g1-acq-discount.js) 与 [g1-sku-report-discount.js](../g1-sku-report-discount.js)。修改源后执行 `node tools/build-variants.cjs`，`--check` 只检查两个源内区块和两个副本，不写文件。

验证项目包括五套模板目录与真实卡片表单、点击 POST / 303、两个月付 exact 与年度 / helium / 历史普通 2TB 的区分、Pro 与土区 20TB 共存及各源独立切换、应用 Pro 原子停用旧 2TB 前缀与旧 exact 20TB → 同一 Gemini 目标并保留其他目标、同源合并启用并保留 ID / 顺序、其他手动或禁用规则保留、重复应用与修订变化、满 30 条更新已有项与新增超限原子失败、未知模板 / 篡改字段拒绝、旧页面与保存前冲突、存储读写 / 回读失败。卡片检查两条 exact 和两种旧路由的一致条件、当前版本 / 修订 / 配置更新时间 / 状态门槛、未应用 / 等待 / 当前零命中的区分、本模板单源 / 双源次数、不同来源 / 目标与普通 2TB 历史行不计入 Pro；也覆盖 Pro 12个月前缀不覆盖两条月付 exact、默认 75p / 2TB 规则、手动增删改 / 禁用 / 恢复、空列表、精确优先 / 最长前缀 / 单次替换、三个 SKU 命名空间、Gemini 后缀 160 / 完整 178 字符的原生表单与 POST / runtime / JSON / HTML 路径、后缀 161 拒绝、历史独立、300 / 10 上限、JSON 与模块 pin / 请求体标记。测试结果按实际命令输出记录，实机结果按第 8 节填写。
