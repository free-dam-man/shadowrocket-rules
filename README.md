# Shadowrocket 三区制分流（美国 / 新加坡 / 直连）

> 全自动 · 零手动 · DNS 零泄漏

## 这是什么

一份开箱即用的 Shadowrocket 分流配置。跟常见的「国内直连、国外代理」两分法不同，
这份配置按**业务对 IP 归属地的真实要求**分成三个区：

| 分区 | 走哪条线路 | 包含 |
|---|---|---|
| 🇺🇸 美国区 | 美国节点 | 必须美国 IP 的业务：PayPal、Tello、WhatsApp、X、Meta 全家桶（Facebook / Instagram / Threads / Meta AI）、Claude |
| 🇸🇬 新加坡区 | 新加坡节点 | 其余**所有**非大陆流量：AI 应用、YouTube / Netflix / Disney+ / TikTok / Spotify、Telegram、加密交易所、香港 / 海外金融 App…… |
| 🇨🇳 直连区 | DIRECT | 中国大陆 App |

**核心取舍**：美国节点通常延迟高（实测 400ms+），新加坡节点延迟低（实测 90ms+）。
除非业务强制要求美国 IP，否则一律走新加坡——快，而且稳。

## 快速上手

1. 复制配置链接：
   ```
   https://raw.githubusercontent.com/free-dam-man/shadowrocket-rules/main/Shadowrocket-US-SG-Direct.conf
   ```
2. 打开 Shadowrocket → **配置** → 右上角 `+` → **从 URL 下载** → 粘贴上面的链接
3. 完成。不需要手动选节点，三个策略组全部自动测速。

## 设计细节

- **风控隔离**：WhatsApp / PayPal / Tello 走独立的「美国风控」组，低频测速（30 分钟一次）
  + 50ms 抖动容忍，尽量不跳 IP；X / Meta / Claude 走常规美国测速组。
- **FINAL 兜底走新加坡**：任何没被规则命中的境外流量自动进新加坡，不会漏。
- **香港 App 也走新加坡**：Wise、OCBC、ZA、HSBC HK、中银香港、IBKR、币安、Stripe 等已写显式规则。
- **DNS 零泄漏**：境外查询走代理隧道内的 Cloudflare / Google DoH；国内查询走阿里 / 腾讯加密 DoH；
  禁用系统 DNS 回退（`dns-direct-system = false`）；劫持硬编码的 8.8.8.8 / 1.1.1.1。
- **大陆补漏**：QQ、招商银行、兴业银行、电子税务局、夸克、钉钉、爱企查等显式直连，
  防止被 FINAL 误送进新加坡。

## 自定义

**节点名关键词**：策略组按节点名中的地区关键词自动归类。
美国组匹配 `美国 / US / United States / 洛杉矶 / 旧金山 / 圣何塞 / 硅谷 / 西雅图 / 纽约 / 芝加哥 / 达拉斯 / 凤凰城 / 丹佛 / 亚特兰大 / 迈阿密 / 波士顿 / 俄勒冈 / 弗吉尼亚 / 加州 / 德州`；
新加坡组匹配 `新加坡 / 狮城 / Singapore / SG`。节点名有其他写法，在配置文件的
`[Proxy Group]` 里 `policy-regex-filter=` 后面加词即可。

**没有新加坡节点**：把配置文件文末的 `FINAL,新加坡节点` 改成 `FINAL,自动选择`（一行）。

**想把风控组彻底钉死到某一个节点**：把「美国风控」那行改成
`美国风控 = select, 你的节点名`。

**给某个 App 指定线路**：打开 Shadowrocket 的「数据」页，看这个 App 实际请求的域名，
在 `[Rule]` 里加一行 `DOMAIN-SUFFIX,example.com,想走的组`，放在对应分区即可。

## 免责声明

- 规则集（RULE-SET）引用自第三方公开仓库，内容以其上游为准。
- 分流配置只决定流量走哪条线路，不构成任何合规承诺；请遵守当地法律法规与各平台条款。
- 欢迎提 Issue / PR 一起完善。
