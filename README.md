# Legado Book-Source Skill / 阅读 Legado 书源技能

[中文](#中文说明) · [English](#english)

## 中文说明

这是一个面向 AI Agent 的 **Legado（阅读 3.0）书源制作、分析和调试技能**。它帮助 AI 根据合法可访问的网页、公开 API、公共领域内容或用户自有内容，制作可导入 Legado 的 JSON/TXT 书源。

### 能做什么

- 分析搜索页、发现页、详情页、目录页和正文页；
- 编写和调试 Default/JSoup、CSS、XPath、JSONPath、JavaScript 和正则规则；
- 处理搜索关键词、分页、相对链接、请求头、编码和 POST 请求；
- 设计目录翻页、正文翻页、章节顺序和内容净化规则；
- 在合法授权范围内处理登录 UI、CookieJar、登录头和会话状态；
- 检查已有书源的字段、规则和常见错误；
- 输出可导入的 JSON/TXT 书源及字段说明。

### 文件结构

```text
legado-book-source-skill/
├── SKILL.md                         # AI Agent 的主技能指令
├── references/
│   └── legado-rules.md              # 详细规则语法和 API 参考
└── README.md                        # 本说明文件
```

### 如何让其他 AI 使用

把仓库链接发给支持读取 GitHub 或 Markdown 技能文件的 AI，并附上下面的提示词：

```text
请读取并使用这个 Legado 书源技能：
https://github.com/ZYJ882/legado-book-source-skill

请先读取 SKILL.md；如果支持加载参考文件，再读取
references/legado-rules.md。

根据这个技能，帮我为以下合法授权的网站/内容制作 Legado 书源：
目标地址：
我拥有的使用权限：
需要支持：搜索 / 发现 / 详情 / 目录 / 正文 / 分页 / 登录（请删除不需要的项目）
测试书名或页面：
输出格式：JSON / TXT / 规则分析
```

如果目标 AI 支持 GitHub 仓库作为 Skill，通常可以直接导入仓库。如果它只支持上传单个文件，请同时上传 `SKILL.md` 和 `references/legado-rules.md`。如果它既不能读取 GitHub，也不能读取附件，仅发送链接不会自动产生效果。

### 最好同时提供什么

仅有仓库链接通常不足以制作可测试的书源。建议同时提供：

1. 目标网站或 API 地址；
2. 你对内容的授权或合法使用范围；
3. 搜索页、详情页、目录页和正文页的公开测试链接或脱敏样本；
4. 需要的功能，例如搜索、发现、分页、登录或下载；
5. 一个已知的书名或测试条目。

### 合规边界

本技能只用于合法授权、公共领域或用户自有内容。不要使用它绕过付费墙、VIP 限制、验证码、登录控制、反爬措施、DRM 或版权限制；不要把 Cookie、密码、Token 或个人数据写入公开书源。

### 参考来源

- [Legado 书源规则说明](https://github.com/missdeer/getnovel/blob/master/Legado%E4%B9%A6%E6%BA%90%E8%A7%84%E5%88%99%E8%AF%B4%E6%98%8E.txt)
- [The tutorial of Legado / Rule/source](https://mgz0227.github.io/The-tutorial-of-Legado/Rule/source.html)
- [Legado 官方入门文档](https://gedoor.github.io/docs/GettingStarted)

## English

This repository contains an **AI-agent skill for creating, inspecting, debugging, and reviewing Legado (Reading 3.0) book sources**. It helps an AI turn lawfully accessible websites, public APIs, public-domain content, or user-owned content into importable Legado JSON/TXT sources.

### Capabilities

- Analyze search, discovery, book-info, table-of-contents, and chapter pages;
- Write and debug Default/JSoup, CSS, XPath, JSONPath, JavaScript, and regex rules;
- Handle keywords, pagination, relative URLs, headers, encodings, and POST requests;
- Build table-of-contents and chapter pagination and content-cleaning rules;
- Handle login UI, CookieJar, login headers, and session state only within authorized access;
- Inspect existing sources and diagnose common rule failures;
- Produce an importable JSON/TXT source with field notes.

### How to give it to another AI

Send the repository URL to an AI that can read GitHub repositories or Markdown skills, together with this prompt:

```text
Please read and use this Legado book-source skill:
https://github.com/ZYJ882/legado-book-source-skill

Read SKILL.md first. If your environment supports bundled references,
also read references/legado-rules.md.

Using this skill, help me create a Legado source for the following
legally authorized website/content:
Target URL:
My authorization:
Required features: search / explore / book info / TOC / content / pagination / login
Test title or page:
Output: JSON / TXT / rule analysis
```

If the target AI supports GitHub repositories as skills, it may import the repository directly. If it only accepts individual files, upload both `SKILL.md` and `references/legado-rules.md`. If it can read neither GitHub links nor attachments, sending only the URL will not automatically activate the skill.

### Recommended task context

Provide the target website or API, authorization scope, sanitized samples or public test URLs for the relevant pages, required features, and a known test title. The skill is intended only for authorized, public-domain, or user-owned content. It must not be used to bypass paywalls, VIP restrictions, CAPTCHAs, login controls, anti-bot measures, DRM, or copyright restrictions, and public sources must not contain cookies, passwords, tokens, or personal data.

## License and attribution

The skill documentation is provided as practical guidance based on the linked Legado rule references and official getting-started documentation. Check the upstream documents and the target Legado build for version-specific behavior.
