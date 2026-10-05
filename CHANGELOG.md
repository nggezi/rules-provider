# 更新日志

本项目规则集随使用中的问题逐步补充，遵循 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.1.0/) 风格。

## [Unreleased]

### 修复 — 媒体域名国内/国际版拆分（关键）

一个品牌同时存在国内版与国际版时，把国际版域名从「国内直连」规则集中移出，改由走代理的规则集承接，避免海外 CDN 被误分到直连导致卡顿 / 无法访问。

- **爱奇艺**：
  - `Domestic_Media`：仅保留国内域名（`iqiyi.com` / `iqiyipic.com` / `qiyi.com` / `qiyipic.com` / `qy.net`），移除国际版域名。
  - `Proxy_Selection_2`：新增国际版域名走代理 —— `cache.video.iqiyi.com`、`inter.iqiyi.com`、`intl-rcd.iqiyi.com`、`intl-subscription.iqiyi.com`、`intl.iqiyi.com`、`iq.com`。
  - 对齐 blackmatrix7 `iQIYI`（国内）/ `iQIYIIntl`（国际）的拆分思路。
- **哔哩哔哩**：
  - `Bilibili`：恢复 Akamai **中国边缘** CDN（`p-bstarstatic.akamaized.net`、`p.bstarstatic.com`、`upos-bstar-mirrorakam.akamaized.net`、`upos-bstar1-mirrorakam.akamaized.net`、`upos-hz-mirrorakam.akamaized.net`），国内解析到 `23.60.96.x` / `23.204.80.x`，保持直连。
  - `Foreign_Media`：补 `biliintl.com`（国际版），与既有 `bilibili.tv` / `biliintl.co` 一起走代理。
  - `Global_Direct_3`：移除 `biliintl.co`，消除与 `Foreign_Media` 的方向冲突。
- **外部一致性**：改动与 blackmatrix7（`BiliBili` / `BiliBiliIntl`、`iQIYI` / `iQIYIIntl`）及 MetaCubeX `geosite`（`iqiyi` / `bilibili`）对照确认；CDN 归属以国内 DNS 解析结果为准。

### 新增 — AI / 流媒体 / 平台域名补全

- `AI_Service`：补充 Google Gemini / AI Studio、OpenAI（含登录风控依赖与语音 WebRTC）、Claude、POE、Grok (xAI)、OpenRouter、Cloudflare AI Gateway、Dify、Jasper / OpenArt / ClipDrop、GitHub Copilot 等域名，按服务分组注释。
- `Telegram_Message`：补充 Telegram 主域与生态域名（`telegram.dog`、`telegram.space`、`telegram-cdn.org`、`tg.dev`、`telegra.ph` 同族、`fragment.com`、`telega.one` 等）。
- `Youtube_Video`：补充 `ggpht.com(.cn)`、`gvt1.com`、`youtubego.com(.co.id)`、`youtubeembeddedplayer.googleapis.com`、`youtube-ui.l.google.com` 等。
- `Netfilx_Video`：补充 `netflix.ca`、`netflixinvestor.com`、`netflixstudios.com`、`netflixtechblog.com`、`nflxsearch.net`。
- `MSN_Service`：补充 Microsoft 主域 / 账号、开发者工具、Xbox / 游戏域名；`vscode.*` / `vsassets.io` / `gamepass.com` / `tenor.com` 移出（改由代理兜底）。
- `Game_Platform`：补充 `steam-api.com`、`steam.tv`、`steamdeck.com`、`steamcdn-a.akamaihd.net`、`steamvideo-a.akamaihd.net`、`steammobile.akamaized.net`。
- `Foreign_Media`：补充 Amazon / Prime Video / IMDb / Audible 及 Disney 族（`disney.com`、`espn*`、`marvel.com`、`starwars.com`、`nationalgeographic.com`、`hotstar.com` 等）。
- `Bilibili`：补充 `biliimg.com`、`bilicomic(s).com`、`maoer.com`、`missevan.com`。

## [Initial] — 基线

规则集随配置整合而建立，覆盖代理兜底、广告拦截、应用净化、国内直连、国外媒体、AI、游戏平台、Apple / Microsoft 服务等。
