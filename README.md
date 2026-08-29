# Loon 自适应分流配置

一份面向日常使用的 Loon 配置，目标是让大多数流量能够自动选择合适的节点，同时保留必要的手动切换能力。

如果你希望导入配置后就能直接使用，不想长期维护大量复杂策略组，这份配置会更适合你。

## 一键导入

<a href="https://www.nsloon.com/openloon/import?sub=https%3A%2F%2Fraw.githubusercontent.com%2Feeib%2FLoon-Adaptive-Routing%2Fmain%2FLoon_Adaptive_Routing_Config.lcf"><img src="https://img.shields.io/badge/Loon-一键导入-5A67D8?style=for-the-badge" alt="一键导入 Loon"></a>

使用已安装 Loon 的 iPhone 或 iPad 点击上方按钮即可导入配置。导入完成后，只需要在 Loon 中添加自己的节点或订阅即可使用。

> 本仓库不提供任何代理节点、机场订阅、账号或私有凭据。

## 这份配置能做什么

- 自动识别并归类香港、澳门、台湾、日本、韩国、新加坡、美国、英国、德国、澳大利亚节点。
- 每个地区同时提供自动优选和手动选择，平时可以交给 Loon 自动选择，需要时也可以自己指定节点。
- AI、TikTok、Netflix、流媒体和国际服务使用不同的地区优先顺序，避免所有服务都挤在同一个出口。
- YouTube、Apple、Speedtest、国内下载和国际下载分别使用独立策略。
- 没有单独分类的流量统一交给「通用代理」处理。
- 默认启用 IPv6 优先、IPv6 VIF、DNS 泄漏防护、HTTPDNS 阻断和 STUN 阻断。
- 代理节点不支持 UDP 时不会自动回落直连，减少非预期流量泄漏。
- 保留 Clash.Meta / sing-box Fake-IP 兼容规则。
- 集成 YouTube、Spotify 去广告，以及 Sub-Store、Script-Hub、BoxJs、节点检测等常用工具。

## 默认策略

| 服务 | 默认出口 |
| :--- | :--- |
| AI | 新加坡 → 美国 → 日本 → 全球 |
| TikTok | 韩国 → 日本 → 新加坡 → 美国 → 全球 |
| Netflix | 新加坡 → 日本 → 美国 → 香港 → 全球 |
| 流媒体 | 新加坡 → 日本 → 美国 → 香港 → 台湾 → 全球 |
| 国际服务 | 香港 → 日本 → 新加坡 → 台湾 → 韩国 → 美国 → 全球 |
| YouTube | 全球自动 |
| Apple | DIRECT |
| Speedtest | 全球自动 |
| 国内下载 CDN | DIRECT |
| 国际下载 CDN | DIRECT |
| 其他流量 | 全球自动 |

这些默认顺序只是日常使用的优先级，并不是强制固定节点。对应地区不可用时，策略会继续尝试后面的出口；如果某个服务存在地区解锁限制，也可以直接在 Loon 中切换到对应的手动策略。

## 自动选择是怎么工作的

配置先把订阅节点按地区归类，再在每个地区内部通过 `url-test` 自动选择连接质量更好的节点。AI、TikTok、Netflix 等业务再根据各自的地区优先顺序选择出口。

简单来说：

```text
订阅节点 → 地区归类 → 地区自动优选 → 业务分流 → 通用代理 → FINAL
```

需要注意的是，`url-test` 只能判断节点是否可用以及延迟表现，不能判断 ChatGPT、Netflix、TikTok 等服务是否真正具备地区解锁能力。如果遇到服务不可用或地区不正确，直接在对应策略中手动切换地区或节点即可。

## 使用前建议

首次导入后，建议先确认自己的节点已经正确显示在各地区策略组中。如果某些节点没有被识别，可以检查机场节点名称是否包含对应国家或地区名称、缩写或旗帜。

本配置默认使用 IPv6 优先，并始终开启 IPv6 VIF。如果你的网络或代理节点对 IPv6 支持不好，可以根据自己的网络环境进行调整。

`disable-stun=true` 用于减少 WebRTC / STUN 暴露真实网络地址的风险，因此少部分依赖 WebRTC 的网页或应用功能可能受到影响。

`udp-fallback-mode=REJECT` 用于避免代理节点不支持 UDP 时自动回落直连。如果某些应用明确依赖 UDP，而当前节点又不支持 UDP，可能需要更换节点。

YouTube、Spotify 去广告依赖 Rewrite、脚本、MITM 以及上游插件持续维护，可能随着应用接口变化出现阶段性失效，这属于正常现象。

## 适合哪些用户

这份配置更适合希望「导入后直接使用」的 Loon 用户：日常以自动选择为主，但又希望在节点状态、地区解锁或特殊需求出现时可以快速手动调整。

如果你更喜欢完全手动选择节点，或者希望自己搭建非常复杂的负载均衡、场景切换和多层策略，这份配置未必是最合适的模板。

## 资源与鸣谢

本配置使用了以下社区项目提供或维护的规则、插件、图标及相关资源：

- [iKeLee / ProxyResource](https://github.com/luestr/ProxyResource)
- [iKeLee / IconResource](https://github.com/luestr/IconResource)
- [Koolson / Qure](https://github.com/Koolson/Qure)
- [Sub-Store](https://github.com/sub-store-org/Sub-Store)

感谢相关作者与社区维护者。第三方资源的版权与许可归其各自作者所有。

## 开源许可

本项目采用 [MIT License](./LICENSE)。

## 免责声明

本项目仅用于网络配置学习、研究及个人使用。使用者应自行确认其使用方式符合所在地法律法规、网络服务条款及第三方资源许可。项目作者不对因使用本配置产生的网络故障、账号限制、服务不可用或其他损失承担责任。
