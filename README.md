# rules-provider

自用的 Clash Meta (mihomo) `RULE-SET` 规则集集合，供 `mix.yaml` / `mix_merlin_clash.yaml` 等主配置通过 `rule-providers` 远程拉取。

所有文件均为 mihomo `behavior: classical` 格式：

```yaml
payload:
  - DOMAIN-SUFFIX,example.com
  - DOMAIN-KEYWORD,example
  - IP-CIDR,1.2.3.0/24,no-resolve
  - PROCESS-NAME,example
```

## 与主配置的对应关系

主配置里每条 `RULE-SET` 的命中结果都会被送到一个固定的代理策略组。**顺序即优先级**，越靠前越先生效。

| 规则集 | 命中后走向 | 组默认出口 |
| --- | --- | --- |
| `Proxy_Selection_1` | 🚀 节点选择 | 代理 |
| `Global_Direct_1` | 🎯 全球直连 | 直连 |
| `Ad_Blocking` | 🛑 广告拦截 | REJECT |
| `App_Purification` | 🍃 应用净化 | REJECT |
| `Google_FCM` | 📢 谷歌FCM | 直连优先 |
| `Global_Direct_2` | 🎯 全球直连 | 直连 |
| `MSN_Cloud` | Ⓜ️ 微软云盘 | 直连 |
| `MSN_Service` | Ⓜ️ 微软服务 | 直连 |
| `Apple_Service` | 🍎 苹果服务 | 直连 |
| `Telegram_Message` | 📲 电报消息 | 代理 |
| `AI_Service` | 💬 OpenAi | 代理 |
| `Netease_Music` | 🎶 网易音乐 | DIRECT |
| `Game_Platform` | 🎮 游戏平台 | 直连 |
| `Youtube_Video` | 📹 油管视频 | 代理 |
| `Netfilx_Video` | 🎥 奈飞视频 | 代理 |
| `Bahamut` | 📺 巴哈姆特 | 代理 |
| `Bilibili` | 📺 哔哩哔哩 | 直连 |
| `Domestic_Media` | 🌏 国内媒体 | 直连 |
| `Foreign_Media` | 🌍 国外媒体 | 代理 |
| `Proxy_Selection_2` | 🚀 节点选择 | 代理 |
| `Global_Direct_3` | 🎯 全球直连 | 直连 |

主配置中的规则顺序（`mix.yaml` / `mix_merlin_clash.yaml`）：

```
Proxy_Selection_1 → Global_Direct_1 → Ad_Blocking → App_Purification → Google_FCM
→ Global_Direct_2 → MSN_Cloud → MSN_Service → Apple_Service → Telegram_Message
→ AI_Service → Netease_Music → Game_Platform → Youtube_Video → Netfilx_Video
→ Bahamut → Bilibili → Domestic_Media → Foreign_Media → Proxy_Selection_2
→ Global_Direct_3 → MATCH(🐟 漏网之鱼)
```

> `Proxy_Selection_2` / `Global_Direct_3`（体积最大的两个）放在最后，作为兜底；`MATCH` 命中后默认走 🐟 漏网之鱼 → 🚀 节点选择。

## 目录说明

| 文件 | 说明 |
| --- | --- |
| `Ad_Blocking` | 广告域名拦截（REJECT） |
| `App_Purification` | 应用内广告 / 追踪净化（REJECT 优先） |
| `AI_Service` | AI 服务：Google Gemini / AI Studio、OpenAI / ChatGPT、Claude、POE、Grok (xAI)、OpenRouter、Copilot、Dify 等，按服务分组注释 |
| `Apple_Service` | Apple 服务域名 |
| `Bahamut` | 巴哈姆特（动画疯） |
| `Bilibili` | 哔哩哔哩（含 Akamai 中国边缘 CDN、国际版入口域名） |
| `Domestic_Media` | 国内流媒体：爱奇艺(国内)、芒果TV、腾讯视频、优酷 |
| `Foreign_Media` | 国外流媒体：Netflix 之外的 Amazon / Prime Video / Disney 族 / ESPN / Marvel / StarWars / NatGeo / Hotstar、Bilibili 国际版等 |
| `Game_Platform` | 游戏平台：Steam / Epic 等 |
| `Global_Direct_1/2/3` | 全球直连兜底（1/2 精选，3 为超大兜底集） |
| `Google_FCM` | Google FCM 推送（直连优先） |
| `MSN_Cloud` | 微软云盘 (OneDrive 等) |
| `MSN_Service` | 微软服务：Microsoft 账号、开发者工具、Xbox、Bethesda 等 |
| `Netease_Music` | 网易云音乐 |
| `Netfilx_Video` | Netflix（含各区域主域） |
| `Proxy_Selection_1/2` | 需代理域名兜底（2 为超大集） |
| `Telegram_Message` | Telegram 主域 + 生态域名 |
| `Youtube_Video` | YouTube / Google Video 相关 |

## 命名 / 分流约定

- **国内服务与国际版拆分**：一个品牌若同时有国内版与国际版（如爱奇艺 `iQIYI` / `iQIYIIntl`、哔哩哔哩 `BiliBili` / `BiliBiliIntl`），国内域名留在国内直连规则集，国际版域名放进走代理的规则集（`Foreign_Media` / `Proxy_Selection_2`），避免海外 CDN 被误分到直连。
- **CDN 判定以国内解析为准**：Akamai 等 anycast / 边缘域名在国内外解析结果不同，判定是否直连时应以**国内 DNS 视角**（如 223.5.5.5）为准。
- **多服务规则集加注释**：`AI_Service` 等包含多个服务的规则集，按服务分组并加注释，便于后续增删。

## 使用

主配置示例（以 `mix.yaml` 为例）：

```yaml
rule-providers:
  Bilibili:
    behavior: "classical"
    type: http
    url: "https://raw.githubusercontent.com/nggezi/rules-provider/refs/heads/main/Bilibili"
    interval: 3600
    path: ./Bilibili.yaml

rules:
  - RULE-SET,Bilibili,📺 哔哩哔哩
```

`interval` 决定自动刷新周期；`path` 为本地缓存路径（相对 mihomo 工作目录）。

## 更新日志

见 [CHANGELOG.md](./CHANGELOG.md)。
