# Legado 书源规则参考

本文件是面向制作书源的精简参考，综合用户提供的 `guiz.md`、missdeer/getnovel 的规则说明和 mgz0227 的教程。具体版本若有差异，以目标 Legado 构建的实际帮助和测试结果为准。

## 1. 规则类型

| 写法 | 含义 | 主要使用场景 |
|---|---|---|
| `@@...` | 默认 JSoup/Default 规则；通常可省略 `@@` | HTML |
| `@css:...` | CSS 选择器 | HTML |
| `@XPath:...` 或 `//...` | XPath | HTML |
| `@Json:...` 或 `$.` 开头 | JSONPath | JSON 响应 |
| `:...` | AllInOne 正则 | 搜索/发现/详情预处理/目录列表 |
| `@js:...` 或 `<js>...</js>` | JavaScript | 计算、状态、复杂请求和转换 |

在 `{{...}}` 中，没有明显标志会按 JavaScript 执行；Default 要以 `@@` 开头，XPath 用 `@XPath:` 或 `//`，JSONPath 用 `@Json:` 或 `$.`，CSS 用 `@css:`。旧式 `{}` 尽量避免，主要保留给旧版 JSONPath 规则。

## 2. Default/JSoup 规则

Default 规则用 `@` 串联路径。每段通常是 `类型.名称.索引`，末段是输出类型：

```text
class.item.0@tag.a.0@text
id.content@html
class.cover.0@tag.img.0@src
```

常见类型/输出：`class`、`id`、`tag`、`text`、`children`、`text`、`textNodes`、`ownText`、`html`、`all`、`href`、`src`。索引从 0 开始，负数从末尾计数，`-1` 是最后一个；前缀 `-` 可反转列表。可用 `!` 排除索引；新版也支持 `[index]`、`[start:end:step]` 和 `[-1:0]` 反转。

CSS 规则必须以 `@css:` 开头，例如：

```text
@css:.book-list > li
@css:.title@text
@css:a@href
```

## 3. 连接与净化

同类型规则可用：

- `||`：按顺序取第一个非空结果；
- `&&`：合并所有结果；
- `%%`：交错取多个列表的第 1、2、3 项；
- `##`：正则循环替换/净化，通常追加在字段末尾；
- `#####`：OnlyOne 正则，只取第一个匹配并替换；
- 列表规则前的 `-`：反转列表。

不要把 `&&`、`||`、`%%` 混用于 JavaScript 或正则规则。净化例子：

```text
@css:.content@html##广告.*?##
```

## 4. 书源字段模型

### 基本

- `bookSourceUrl`：必填、唯一标识；
- `bookSourceName`：必填；
- `bookSourceGroup`：分组；
- `loginUrl`：可选登录页面或登录脚本；
- `bookUrlPattern`：识别某站书籍 URL 的正则；
- `header`：全局请求头；
- `bookSourceType`：普通网页、文件、音频等类型；
- 并发率：如 `1000` 表示访问间隔 1 秒，`20/60000` 表示 60 秒最多 20 次。

### 搜索

- `searchUrl`：使用 `{{key}}`、`{{page}}`，支持相对 URL 和 POST 配置；
- `ruleSearch.bookList`：书籍列表；
- `name`、`author`、`kind`、`wordCount`、`lastChapter`、`intro`、`coverUrl`、`bookUrl`：列表字段。

分页例子：

```text
https://example.com/search?q={{key}}&page={{page}}
```

第一页没有页码时可以使用条件表达式，例如 `{{page - 1 == 0 ? "" : page}}`。

### 发现

- `exploreUrl`：可用多行或 `&&` 分隔多个发现入口；
- 发现列表字段与搜索列表基本相同；
- 可用 `infoMap` 保存自定义按钮、筛选值和排序值。

### 详情

- `ruleBookInfo`：详情字段；
- `bookInfoInit`：预处理，只能使用 AllInOne 正则或 JavaScript；
- `name`、`author`、`kind`、`wordCount`、`lastChapter`、`intro`、`coverUrl`、`tocUrl`。

JavaScript 预处理可以返回对象，然后让字段规则引用对象键：

```js
(function () {
  return { a: "书名", b: "作者", h: "https://example.com/toc" };
})()
```

### 目录

- `chapterList`：章节列表；前置 `-` 可倒序；
- `chapterName`/`ruleChapterName`：章节名；
- `chapterUrl`：章节 URL；
- `isVip`：结果为 `null`、`false`、`0` 或空字符串时视为非 VIP；
- `chapterInfo`：章节信息；
- `nextTocUrl`：目录下一页，返回空列表、`null` 或空字符串表示结束。

### 正文

- `content`：正文规则；
- 可以追加 `##` 净化广告、脚注或多余空白；
- 通过 `webView`、正文分页或 `nextContentUrl` 处理动态正文，但只对合法可访问内容使用。

## 5. URL、请求和变量

URL 后可接 JSON 选项：

```text
https://example.com/page,{"charset":"gbk","headers":{"User-Agent":"Mozilla/5.0"},"webView":true}
```

常用选项：`charset`、`method`、`body`、`headers`、`webView`、`timeout`、`followRedirects`、`proxy`、`dnsIp`、`js`、`bodyJs`。请求头名称注意大小写；`dnsIp` 不能与 `proxy` 同时使用。

代理示例：

```json
{"proxy":"socks5://127.0.0.1:1080"}
{"proxy":"http://user:password@127.0.0.1:8080"}
```

JavaScript 中常用变量/方法：

- `result`：当前响应体；
- `baseUrl`：当前 URL；
- `java.ajax(url)`：请求文本；
- `java.getString(rule, isUrl)`、`java.getStringList(rule, isUrl)`、`java.getElements(rule)`：二次解析；
- `java.setContent(content, baseUrl)`：更换解析内容；
- `java.base64Encode/Decode`、`java.md5Encode/Encode16`；
- `java.timeFormat(timestamp)`；
- `source.put/get` 或 `@put/@get`：保存/读取状态。

只在确有必要时使用脚本；不要在脚本中硬编码个人 Cookie、密码、Token 或未知第三方上传地址。

## 6. 登录与 Cookie

对用户有权访问的账户内容，优先使用内置登录 UI，不要模拟或绕过第三方安全验证。

登录 UI 可使用 `text`、`password`、`button`、`toggle`、`select` 等控件。登录 URL 可以是 URL，也可以是实现 `login()` 的 JavaScript。常见接口包括：

```js
source.getLoginInfo()
source.getLoginInfoMap().get("username")
source.getLoginHeader()
source.getLoginHeaderMap()
source.putLoginHeader(header)
source.removeLoginHeader()
```

需要验证码/多步骤流程时，`登录UI` 可使用 `{"version":2}`，实现 `loginUi(state)` 和 `loginAction(action, state, form)`，让脚本返回 `rows`、`state`、`error`、`login`、`close` 等状态。只保存用户主动提交的登录信息；不要把密码写入规则文件。

`CookieJar` 可自动保存响应中的 `Set-Cookie`，适合合法的 session 流程。登录检查脚本可在登录后调用 `initUrl()`、更新登录头，再用 `getStrResponse()` 或 `getResponse()` 重试。

## 7. 文件、音频和图片

文件型书源可从详情页的下载 URL 获取授权文件；应用可根据响应头和 URL 选项识别文件信息。音频型书源把正文结果作为音频链接，序列化的多个链接可拼接播放。

图片若需要合法的二次解密，解密规则应返回 `ByteArray`；涉及密钥时不要把私钥写入公开书源。图片链接可以附带 `headers` 修改 User-Agent、Referer 或 Cookie，但应避免泄露用户数据。

## 8. 调试清单

- 先确认原始响应是否包含目标内容；动态页面可能需要合法的 WebView 或公开 API；
- 搜索规则先固定关键词测试，再加入 `{{key}}` 和 `{{page}}`；
- 先提取列表节点，再提取节点内部字段；
- 用 `href`/`src` 后检查相对 URL；
- JSONPath 先确认响应是 JSON，不要把 HTML 当 JSON；
- XPath 以 `@XPath:` 或 `//` 开头；
- Default 规则中的索引、列表方向和 `##` 替换要分别测试；
- 页面改版后优先调整选择器，不要直接增加复杂脚本；
- 用限速和合理并发，尊重站点服务条款；
- 对未授权、付费、VIP、验证码和反爬场景停止自动化抓取。
