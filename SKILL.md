---
name: legado-book-source
description: 制作、分析、调试和审查 Legado（阅读 3.0）书源与订阅规则。用于为合法授权、公共领域或用户自有内容创建 JSON/TXT 书源，解析搜索、发现、详情、目录和正文页面，编写 CSS/JSoup、CSS、XPath、JSONPath、JavaScript 和正则规则，处理登录、Cookie、分页、请求参数、文件/音频源及排错；不要用于绕过付费墙、验证码、登录限制或抓取未获授权的受版权内容。
license: Complete terms in LICENSE.txt
---

# Legado 书源技能

## 目标与边界

将一个**合法可访问且有权使用**的网页、API、文件或音频来源转换为可导入 Legado 的书源 JSON/TXT；或检查、修复已有书源。先确认内容来源和授权边界，不制作绕过付费、VIP、验证码、登录保护、反爬或 DRM 的规则，不传播侵权书源集合。

本技能的资料依据：

- 用户提供的 `guiz.md`；
- [Legado 书源规则说明](https://github.com/missdeer/getnovel/blob/master/Legado%E4%B9%A6%E6%BA%90%E8%A7%84%E5%88%99%E8%AF%B4%E6%98%8E.txt)；
- [The tutorial of Legado / Rule/source](https://mgz0227.github.io/The-tutorial-of-Legado/Rule/source.html)；
- [官方入门文档](https://gedoor.github.io/docs/GettingStarted)。

详细语法和扩展 API 见 `references/legado-rules.md`，需要写具体规则时再读取，避免把大段语法常驻上下文。

## 工作流程

### 1. 先确认输入和合法范围

收集目标站点或 API URL、使用权限、内容类型、要支持的能力（搜索、发现、详情、目录、正文、分页、登录或下载），以及脱敏的页面样本。若站点需要绕过验证码、付费墙、VIP、反爬或未授权访问，停止该部分，只提供合规的公开/API/自有内容方案。

### 2. 识别数据形态并选解析器

按优先顺序选择：JSON API 用 JSONPath（`@Json:` 或 `$.`）；静态 HTML 用默认 JSoup 或 `@css:`；复杂 HTML 用 `@XPath:` 或 `//`；需要计算、拼接、发请求或保存状态时用 JavaScript；列表一次性正则提取才使用以 `:` 开头的 AllInOne 正则。

规则标志：`@@` 默认规则（通常可省略）、`@XPath:` XPath、`@Json:` JSONPath、`@css:` CSS、`:` AllInOne 正则。`{{...}}` 内没有标志时按 JavaScript 执行；使用其他规则时必须保留标志。

### 3. 按页面链路逐层构建

1. 基本信息：`bookSourceUrl`（唯一）、`bookSourceName`、分组、请求头、并发率；
2. 搜索：`searchUrl` 使用 `{{key}}` 和 `{{page}}`，解析 `bookList`、书名、作者、详情链接；
3. 发现：可选的 `exploreUrl` 与发现列表；
4. 详情：简介、分类、字数、最新章节、封面和 `tocUrl`；
5. 目录：章节列表、章节名、章节 URL、VIP 标志和目录分页；
6. 正文：正文容器、净化规则、正文分页和下一章链接；
7. 登录/状态：仅对用户有权访问的内容使用登录 UI、CookieJar 或登录头；
8. 导入导出：导出 JSON/TXT，用独立测试数据验证后交付。

### 4. 从最小规则开始

对每个字段先使用最短、最稳定的选择器，再添加备用规则：`||` 取第一个非空结果，`&&` 合并所有结果，`%%` 交错合并列表；目录反序在列表规则前加 `-`；优先稳定的 id、data 属性或 API 字段；去广告和换行放在字段末尾用 `##` 净化。

### 5. 测试与交付

至少测试搜索、详情、目录首尾章节、正文、下一章、空结果和站点错误。记录测试时间、规则假设、是否需要登录、外部依赖和已知限制。交付可导入 JSON/TXT、关键字段说明、合法使用边界和未测试项；不要把 Cookie、账号密码、Token 或真实个人数据写入书源。

## 常见排错顺序

1. URL、重定向、编码；2. `key`、`page` 是否替换；3. HTML 还是 JSON、是否动态渲染；4. 先输出整个响应/节点再缩小选择器；5. 相对 URL、编码、请求头大小写、Referer；6. 空列表、倒序、分页；7. 最后才加入 JavaScript、WebView、Cookie 或代理；8. 登录失败时检查 CookieJar、登录头、登录检查 JS 和授权范围。

## 参考资料导航

- 基础解析器、连接符、字段模型：`references/legado-rules.md` 的“语法与字段”。
- 请求、变量、URL 参数、分页：同文件的“请求与状态”。
- 登录 UI、Cookie、脚本和高级能力：同文件的“登录与高级能力”。
