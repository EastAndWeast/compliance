# Aurellix Limited 网站测试报告

测试日期：2026-07-18  
测试对象：`index.html`、`style.css`、`script.js` 及本地图片资源  
测试范围：功能、UI/响应式实现、可访问性、内容与基础合规信息

## 1. 测试结论

网站结构简单，核心监管状态和“尚未获批、尚未提供服务”的信息表达醒目，页面内的两个导航锚点和本地资源路径均存在。当前不建议直接作为正式公司网站发布，至少应先处理公司法定披露、Logo 点击异常、移动端导航和隐私透明度问题。

问题统计：高 1 项，中 4 项，低 4 项。未发现阻断页面内容读取的致命问题。

## 2. 测试限制

当前会话没有可用的内置浏览器实例，因此未能执行真实点击、控制台/网络面板检查、键盘遍历、320/768/1440 px 实机截图和跨浏览器测试。本报告中的交互结论来自代码级可复现检查；UI 结论来自当前 CSS 与仓库现有截图。仓库截图与当前 HTML/CSS 存在版本差异，不能视为当前版本的完整视觉验收结果。

## 3. 问题清单

### H-01 高：缺少爱尔兰公司网站法定身份信息

- 类型：内容 / 合规
- 位置：`index.html:193-199` 页脚及全站
- 现状：只显示公司名称和版权年份，没有注册地、公司注册号、注册地址。
- 影响：无法让访客核验公司主体，也可能不满足爱尔兰公司网站披露要求。
- 建议：在页脚显著加入 `Registered in Ireland`、CRO/company registration number、registered office address；内容须由公司/法律顾问确认。
- 依据：S.I. No. 306/2014 要求公司网站在显著且易于访问的位置展示相应公司资料。

### M-01 中：点击 Logo 会触发 JavaScript 异常

- 类型：功能
- 位置：`index.html:18`，`script.js:20-24`
- 复现：点击页眉 Logo；其链接为 `href="#"`，事件代码随后执行 `document.querySelector('#')`。
- 预期：平滑返回页首或跳转首页。
- 实际：`#` 不是有效 CSS 选择器，浏览器会抛出 `DOMException/SyntaxError`，点击行为被 `preventDefault()` 阻止。
- 建议：将 Logo 指向明确的 `#top`，并为页面顶部添加对应 id；或在脚本中单独处理空 hash。

### M-02 中：移动端完全隐藏导航

- 类型：功能 / UI
- 位置：`style.css:123-128`
- 现状：视口宽度不超过 768 px 时 `.nav { display: none; }`，没有替代菜单。
- 影响：手机用户无法直接跳转到 Important Notice 和 Proposed Services，只能长距离滚动。
- 建议：保留紧凑的横向导航，或实现可键盘操作、带正确 ARIA 状态的菜单按钮。

### M-03 中：加载第三方字体但没有隐私说明

- 类型：内容 / 隐私
- 位置：`index.html:8-10`，全站页脚
- 现状：浏览器会连接 `fonts.googleapis.com` 和 `fonts.gstatic.com`，页面没有 Privacy Notice，也没有说明第三方请求和相关数据处理。
- 影响：存在 GDPR 透明度风险，访客也无法获知数据控制者联系方式、处理目的、接收方等信息。
- 建议：优先自托管 Inter 字体；同时基于实际数据处理活动发布 Privacy Notice。是否需要同意机制应由隐私/法律顾问结合最终部署和分析工具确认。

### M-04 中：Legal Notice 不在导航中

- 类型：内容发现 / UI
- 位置：`index.html:21-24` 与 `index.html:158`
- 现状：页面有 `#legal` 区块，但桌面导航只有 Compliance 和 Proposed Services。
- 影响：重要免责和范围排除内容不易被发现，移动端又没有任何导航。
- 建议：在页眉或页脚加入 Legal Notice 链接，并保留固定页眉的滚动偏移。

### L-01 低：页脚版权文字对比度不足

- 类型：UI / 可访问性
- 位置：`style.css:636-659`
- 数据：`#475569` 位于 `#0f172a` 背景上的对比度约为 2.36:1；0.8rem 正文通常需要至少 4.5:1。
- 建议：改用更浅的文字色，例如现有的 `#94a3b8`（约 6.96:1）。

### L-02 低：缺少主内容地标和跳过链接

- 类型：可访问性
- 位置：`index.html:13-201`
- 现状：Header 后的所有内容未包裹在 `<main>` 中，也没有“Skip to main content”。
- 影响：屏幕阅读器和键盘用户定位主要内容的效率较低。
- 建议：用 `<main id="main-content">` 包裹 Hero 至 Legal 区域，并在页面顶部加入可聚焦的跳过链接。

### L-03 低：监管状态缺少更新时间与可核验入口

- 类型：内容 / 信任
- 位置：`index.html:46-68`、`index.html:89-117`
- 现状：多处声明申请正在进行，但没有“Last updated”日期，也没有指向 Central Bank/ESMA 官方信息的链接。
- 影响：状态变化后页面容易过期，访客无法方便地区分企业自述和监管机构公开记录。
- 建议：加入明确更新时间、内容负责人复核流程和官方核验链接；“申请中”事实应由公司留存证据并定期复核。

### L-04 低：基础品牌与分享元数据不完整

- 类型：内容 / SEO
- 位置：`index.html:3-11`
- 现状：已有 title 和 description，但没有 favicon、canonical、Open Graph/Twitter 分享信息。
- 影响：浏览器标签和社交分享展示不完整。
- 建议：正式域名确定后补齐 favicon、canonical、`og:title`、`og:description`、`og:image`。

## 4. 已通过或表现良好的项目

- `#compliance`、`#services` 两个导航目标均存在，且 CSS 设置了固定页眉滚动偏移。
- `style.css`、`script.js`、`logo-light.png` 的本地路径均存在。
- 页面设置了 `lang="en"`、UTF-8、viewport、独立 title 和 meta description。
- 只有一个 H1，H1/H2/H3 结构总体清晰。
- 监管状态卡明确写出申请中、服务尚不可用、未接收或持有客户资产。
- “proposed services only”“only if and when authorised”等限制语多次出现，核心风险提示较醒目。
- 服务名称与 Central Bank of Ireland 公布的 MiCAR CASP 服务类别在表述上基本一致。
- 主要正文颜色的静态对比度总体可接受；警示框文字对比度约为 6.84:1。
- 未发现表单、登录、支付、开户或外部业务入口，因此不存在这些流程的半成品交互。

## 5. 建议修复顺序与回归标准

1. 补齐公司法定资料，并由爱尔兰法律/合规负责人确认。验证：桌面和手机页脚均显著可见且信息准确。
2. 修复 Logo 空 hash。验证：点击和键盘激活均返回页首，控制台无异常。
3. 恢复移动端导航。验证：320、375、768 px 下所有主要区块可达，菜单支持 Tab/Enter/Escape。
4. 处理 Google Fonts 与 Privacy Notice。验证：网络请求与隐私说明一致；如自托管，不再请求 Google 字体域名。
5. 修复对比度与语义结构。验证：页脚文字达到 WCAG AA，存在 `<main>` 和可用的跳过链接。
6. 在真实浏览器中做最终回归。验证：Chrome/Edge/Safari、键盘、200% 缩放、控制台、网络 404、移动端无横向滚动。

## 6. 参考依据

- Irish Statute Book, S.I. No. 306/2014, European Communities (Companies) Regulations 2014
- Central Bank of Ireland, Markets in Crypto-Assets Regulation (MiCAR)
- Data Protection Commission, “How do I make a privacy policy?”
- WCAG 2.2, Success Criterion 1.4.3 Contrast (Minimum)
