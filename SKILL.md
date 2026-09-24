---
name: legado-book-source
description: "中文：制作、分析、调试和审查 Legado（阅读 3.0）书源与订阅规则；用于合法授权、公共领域或用户自有内容的 JSON/TXT 书源，以及 CSS/JSoup、CSS、XPath、JSONPath、JavaScript、正则、登录、Cookie、分页和请求参数。English: Create, inspect, debug, and review Legado (Reading 3.0) book-source and subscription rules for authorized, public-domain, or user-owned content, including JSON/TXT sources, CSS/JSoup, CSS, XPath, JSONPath, JavaScript, regex, login, cookies, pagination, and request options. Do not use it to bypass paywalls, CAPTCHAs, login controls, anti-bot protections, DRM, or copyright restrictions."
---

# Legado 书源技能 / Legado Book-Source Skill

## 双语简介 / Bilingual introduction

**中文：**本技能帮助 AI Agent 为 Legado（阅读 3.0）制作、分析、调试和审查书源或订阅规则。它覆盖搜索、发现、详情、目录、正文、分页、请求、登录、Cookie、文件和音频等常见场景，并提供 Default/JSoup、CSS、XPath、JSONPath、JavaScript 和正则规则的工作方法。

**English:** This skill helps an AI agent create, inspect, debug, and review Legado (Reading 3.0) book-source or subscription rules. It covers search, discovery, book information, tables of contents, chapters, pagination, requests, login, cookies, files, and audio, with practical guidance for Default/JSoup, CSS, XPath, JSONPath, JavaScript, and regular-expression rules.

**适用范围 / Scope:** The skill is written in standard Markdown with relative references. It does not require Manus, a specific model, a specific IDE, or a proprietary API. Any agent that can read a `SKILL.md` file and optionally load files under `references/` can use it. If an agent does not support bundled references, provide both `SKILL.md` and `references/legado-rules.md` in the same context.

## 目标与合规边界 / Goal and boundaries

将一个**合法可访问且有权使用**的网页、API、文件或音频来源转换为可导入 Legado 的 JSON/TXT 书源；或检查、修复已有书源。先确认内容来源和授权边界，不制作绕过付费、VIP、验证码、登录保护、反爬或 DRM 的规则，不传播侵权书源集合。

Convert a **lawfully accessible and authorized** website, API, file, or audio source into an importable Legado JSON/TXT source, or inspect and repair an existing source. Do not create rules that bypass paywalls, VIP restrictions, CAPTCHAs, login protections, anti-bot controls, or DRM, and do not distribute infringing source collections.

## 使用本技能的前置输入 / Required input

收集目标站点或 API URL、使用权限、内容类型、要支持的能力（搜索、发现、详情、目录、正文、分页、登录或下载），以及脱敏的页面样本。若站点需要绕过验证码、付费墙、VIP、反爬或未授权访问，停止该部分，只提供合规的公开/API/自有内容方案。

Collect the target URL or API, authorization status, content type, required capabilities, and sanitized samples of relevant pages. If the task requires bypassing a CAPTCHA, paywall, VIP restriction, anti-bot measure, or unauthorized access, stop that part and offer only a compliant public/API/user-owned alternative.

## 工作流程 / Workflow

### 1. 识别数据形态并选解析器 / Identify the data shape and parser

按优先顺序选择：JSON API 用 JSONPath（`@Json:` 或 `$.`）；静态 HTML 用默认 JSoup 或 `@css:`；复杂 HTML 用 `@XPath:` 或 `//`；需要计算、拼接、发请求或保存状态时用 JavaScript；列表一次性正则提取才使用以 `:` 开头的 AllInOne 正则。

Prefer JSONPath (`@Json:` or `$.`) for JSON APIs, Default JSoup or `@css:` for static HTML, `@XPath:` or `//` for complex HTML, JavaScript for computation/state/requests, and the `:` AllInOne regex only for appropriate list-processing cases.

规则标志 / Rule markers：`@@` 默认规则（通常可省略） / Default rule (often optional); `@XPath:` XPath; `@Json:` JSONPath; `@css:` CSS; `:` AllInOne 正则 / AllInOne regex. `{{...}}` 内没有标志时按 JavaScript 执行；使用其他规则时必须保留标志 / An unmarked expression inside `{{...}}` is evaluated as JavaScript, so keep the marker when using another rule type.

### 2. 按页面链路逐层构建 / Build the page chain incrementally

1. 基本信息 / Basics：`bookSourceUrl`（唯一 / unique）、`bookSourceName`、分组 / group、请求头 / headers、并发率 / rate limit；
2. 搜索 / Search：`searchUrl` 使用 `{{key}}` 和 `{{page}}`，解析 `bookList`、书名、作者、详情链接；
3. 发现 / Explore：可选的 `exploreUrl` 与发现列表 / optional explore URLs and lists；
4. 详情 / Book info：简介、分类、字数、最新章节、封面和 `tocUrl`；
5. 目录 / Table of contents：章节列表、章节名、章节 URL、VIP 标志和目录分页；
6. 正文 / Content：正文容器、净化规则、正文分页和下一章链接；
7. 登录/状态 / Login and state：仅对用户有权访问的内容使用登录 UI、CookieJar 或登录头；
8. 导入导出 / Import and export：导出 JSON/TXT，用独立测试数据验证后交付。

### 3. 从最小规则开始 / Start with minimal rules

对每个字段先使用最短、最稳定的选择器，再添加备用规则：`||` 取第一个非空结果，`&&` 合并所有结果，`%%` 交错合并列表；目录反序在列表规则前加 `-`；优先稳定的 id、data 属性或 API 字段；去广告和换行放在字段末尾用 `##` 净化。

Start each field with the shortest stable selector, then add fallbacks. Use `||` for the first non-empty result, `&&` to merge results, `%%` to interleave lists, `-` to reverse a list, stable IDs/data attributes/API fields where possible, and trailing `##` rules for cleanup.

### 4. 测试与交付 / Test and deliver

至少测试搜索、详情、目录首尾章节、正文、下一章、空结果和站点错误。记录测试时间、规则假设、是否需要登录、外部依赖和已知限制。交付可导入 JSON/TXT、关键字段说明、合法使用边界和未测试项；不要把 Cookie、账号密码、Token 或真实个人数据写入书源。

Test search, details, the beginning and end of the table of contents, chapter content, next-chapter navigation, empty results, and site errors. Record test time, assumptions, login requirements, dependencies, and limitations. Deliver an importable JSON/TXT source with field notes and usage boundaries; never embed cookies, passwords, tokens, or personal data.

## 常见排错顺序 / Troubleshooting order

1. URL、重定向、编码 / URL, redirects, encoding；
2. `key`、`page` 是否替换 / whether `key` and `page` are substituted；
3. HTML 还是 JSON、是否动态渲染 / HTML vs JSON and dynamic rendering；
4. 先输出整个响应/节点再缩小选择器 / inspect the full response or node before narrowing selectors；
5. 相对 URL、编码、请求头大小写、Referer / relative URLs, encoding, header case, Referer；
6. 空列表、倒序、分页 / empty lists, order, pagination；
7. 最后才加入 JavaScript、WebView、Cookie 或代理 / add JavaScript, WebView, cookies, or proxies only last；
8. 登录失败时检查 CookieJar、登录头、登录检查 JS 和授权范围 / on login failure, check CookieJar, login headers, login-check JS, and authorization。

## 参考资料 / Reference material

本技能的详细规则位于技能根目录下的相对路径 `references/legado-rules.md`。支持引用文件的 Agent 应在需要编写具体规则、请求、登录或高级功能时读取它；不支持引用文件的 Agent 应将该文件与本文件一起加载。

The detailed rules are at the relative path `references/legado-rules.md` under the skill root. Agents that support bundled references should load it when writing concrete rules, request options, login flows, or advanced features. Agents without reference-file support should load both files together.

资料来源 / Sources：

- [Legado 书源规则说明](https://github.com/missdeer/getnovel/blob/master/Legado%E4%B9%A6%E6%BA%90%E8%A7%84%E5%88%99%E8%AF%B4%E6%98%8E.txt)
- [The tutorial of Legado / Rule/source](https://mgz0227.github.io/The-tutorial-of-Legado/Rule/source.html)
- [官方入门文档 / Official getting-started guide](https://gedoor.github.io/docs/GettingStarted)
