# 规则集

Clash Meta (mihomo) `RULE-SET` 规则集集合，供主配置通过 `rule-providers` 远程拉取。

所有文件均为 mihomo `behavior: classical` 格式：

```yaml
payload:
  - DOMAIN-SUFFIX,example.com
  - DOMAIN-KEYWORD,example
  - IP-CIDR,1.2.3.0/24,no-resolve
  - PROCESS-NAME,example
```

## 文件说明

| 文件 | 说明 |
| --- | --- |
| `Ad_Blocking` | 广告域名拦截 |
| `App_Purification` | 应用内广告 / 追踪净化 |
| `AI_Service` | AI 服务：Google Gemini / AI Studio、OpenAI / ChatGPT、Claude、POE、Grok (xAI)、OpenRouter、Copilot、Dify 等，按服务分组注释 |
| `Apple_Service` | Apple 服务域名 |
| `Bahamut` | 巴哈姆特（动画疯） |
| `Bilibili` | 哔哩哔哩（仅国内，含 Akamai 中国边缘 CDN） |
| `Domestic_Media` | 国内流媒体：爱奇艺、芒果TV、腾讯视频、优酷 |
| `Foreign_Media` | 国外流媒体：Amazon / Prime Video / Disney 族 / ESPN / Marvel / StarWars / NatGeo / Hotstar、哔哩哔哩国际版（`bilibili.tv` / `biliintl.*`）等 |
| `Game_Platform` | 游戏平台：Steam / Epic 等 |
| `Global_Direct_1/2/3` | 全球直连兜底（1/2 精选，3 为超大兜底集） |
| `Google_FCM` | Google FCM 推送 |
| `MSN_Cloud` | 微软云盘 (OneDrive 等) |
| `MSN_Service` | 微软服务：Microsoft 账号、开发者工具、Xbox、Bethesda 等 |
| `Netease_Music` | 网易云音乐 |
| `Netfilx_Video` | Netflix（含各区域主域） |
| `Proxy_Selection_1/2` | 需代理域名兜底（2 为超大集） |
| `Telegram_Message` | Telegram 主域 + 生态域名 |
| `Youtube_Video` | YouTube / Google Video 相关 |

## 命名 / 分流约定

- **国内服务与国际版拆分**：一个品牌若同时有国内版与国际版（如爱奇艺 `iQIYI` / `iQIYIIntl`、哔哩哔哩 `BiliBili` / `BiliBiliIntl`），国内域名留在国内直连规则集，国际版域名放进走代理的规则集，避免海外 CDN 被误分到直连。
- **CDN 判定以国内解析为准**：Akamai 等 anycast / 边缘域名在国内外解析结果不同，判定是否直连时应以国内 DNS 视角为准。
- **多服务规则集加注释**：`AI_Service` 等包含多个服务的规则集，按服务分组并加注释，便于后续增删。

## 使用

主配置示例：

```yaml
rule-providers:
  Bilibili:
    behavior: "classical"
    type: http
    url: "https://raw.githubusercontent.com/<owner>/<repo>/<branch>/Bilibili"
    interval: 3600
    path: ./Bilibili.yaml

rules:
  - RULE-SET,Bilibili,<代理策略组>
```

`interval` 决定自动刷新周期；`path` 为本地缓存路径（相对 mihomo 工作目录）。

## 更新日志

见 [CHANGELOG.md](./CHANGELOG.md)。
