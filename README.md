# Session → CPA 一键保存

把当前登录的 ChatGPT 账号 session 一键变成 CPA（CLIProxyAPI）凭证，并直接保存进你自己的 CPA。纯静态网页，打开就能用，不需要安装扩展或油猴脚本。

在线使用：**https://session.shrimpfd.me**

## 功能

1. **一键获取 Session**：把页面上的蓝色按钮拖到浏览器书签栏（只需一次）。以后在 chatgpt.com 登录状态下点这个书签，它会同源取到当前账号的 session，自动跳回工具页并填好。备用方式：剪贴板导入 / 粘贴 / 拖入 .json 文件。
2. **填访问令牌**：只需再填工作空间访问令牌（ChatGPT 工作空间设置 → 凭据里创建的 `at-` 开头令牌），转换时自动注入为凭证顶层 `headers.authorization` —— 这是 Team 账号绕过 Codex 二次验证的关键一步。令牌可按账号记住（只存本地浏览器）。
3. **一键保存到 CPA**：调用 CPA 管理接口 `POST /v8/management/credentials` 直接上传凭证（CLIProxyAPI v8 的管理接口开放跨域，网页可直调），CPAMC 认证文件页里立刻生成凭证卡片。文件名 `{邮箱}.json`，同名会更新原凭证，正好用来给过期凭证续命。也可以只下载 JSON 手动上传。

## 为什么捕捉要用书签按钮

浏览器的同源策略决定了普通网页读不到 chatgpt.com 的登录态，任何声称「网页直接读取」的工具都做不到。书签脚本（bookmarklet）运行在你点击时所在的 chatgpt.com 页面里，是同源的，所以能取到 session；数据通过网址的 `#` 片段带回工具页（不经过任何服务器），工具页读取后立即清除地址栏中的片段。

## 部署你自己的副本

任意静态托管都可以（Cloudflare Pages / GitHub Pages / Nginx 等），仓库里只有一个 `index.html`：

- Cloudflare Pages：Workers & Pages → Create application → Drag and drop your files，上传 `index.html`，再在 Custom domains 里绑定你的域名即可。
- **注意**：页面里「CPA 地址」的默认值是作者自用的 `https://cpa.shrimpfd.me`，使用前请改成你自己的 CPA 地址；管理密钥也在页面里填你自己的。

## 安全说明

- 页面不加载任何第三方资源，唯一的网络请求是把生成的凭证发往你在页面里填的 CPA 地址。
- session JSON、访问令牌、管理密钥都等同于密码：只在自己的设备上使用，不要把导出的 JSON 发给别人。
- 管理密钥与记住的令牌只保存在你本地浏览器的 localStorage 中。
- 管理接口连续 5 次鉴权失败会封 IP 约 30 分钟，密钥填错不要连续重试。

## 已知限制

- session 转出的凭证没有 refresh_token，access_token 过期后 CPAMC 会报 401；重跑一遍本工具的一键保存即可续上，访问令牌那一半只要不被撤销一直有效。
- 书签方式需要把工具页部署在 https 域名下；本地双击打开 index.html 时请用粘贴方式导入。

## 致谢

session → CPA 的字段映射参考了开源项目 [GPTSession2CPAandSub2API](https://github.com/gtxx3600/GPTSession2CPAandSub2API)（MIT），上传接口与 CPAMC 前端自身使用的管理接口契约一致。

## License

MIT
