# Clash / Mihomo 分流规则（3x-ui 订阅配套）

一份自用的 Clash/Mihomo 全局路由规则，配合 3x-ui 面板的「全局路由规则（固定 HTTPS URL）」功能使用。
放在这里是为了让面板能通过一个稳定的 raw URL 拉取，改规则只需改这个仓库。

## 文件

| 文件 | 说明 |
|---|---|
| `routing.yaml` | 代理分组 + 规则，面板直接拉取这一份 |

## 面板里的用法

3x-ui → 订阅设置 → 全局路由规则，填：

```
https://raw.githubusercontent.com/py2u/clash-routing/main/routing.yaml
```

并把「启用路由」打开。面板会在后台更新（缓存 10 分钟，重试间隔 30 秒），
只导入 `proxy-groups`、`rule-providers`、`rules`、`dns`、`sniffer` 这几个键，
代理节点和最终的 PROXY 组由面板自己生成。

## 规则顺序（自上而下）

1. DeepSeek 直连
2. 私有地址直连
3. AI 服务 → `🤖 AI 服务`
4. Telegram → `✈️ Telegram`
5. 流媒体 → `📺 流媒体`
6. DNS 泄露检测站 → `♻️ 自动选择`
7. 国内域名/IP 直连（GeoSite cn / GeoIP CN）
8. 其余 → `♻️ 自动选择`

## 分组

- `♻️ 自动选择`：url-test，按延迟在全部节点里选，`tolerance: 20`
- `🎬 Reality 专线`：url-test，只筛名字含 `Reality` 的节点
- `🤖 AI 服务`：select，默认 `🎬 Reality 专线`
- `📺 流媒体`：select，默认 `♻️ 自动选择`
- `✈️ Telegram`：select，默认 `♻️ 自动选择`

## 一个重要约束

面板会**先单独校验这份 YAML，再合并进订阅**。校验时节点还不存在，
所以这份文件里**不能出现节点名**（如 `🇺🇸 Lisa美国 | VLESS-Reality | 2054`），
否则会报 `unknown proxy or group` 并整份失效。

需要“指定某类节点”时，用 `include-all-proxies: true` + `filter` 先筛出子分组，
再由其他分组引用这个子分组（见 `🎬 Reality 专线`）。

## 修改流程

1. 直接改 `routing.yaml`
2. commit + push
3. 面板最多 10 分钟后自动生效（急的话重启 x-ui）

本地 Clash Verge 的 profile 会照常从订阅刷新，不需要手动同步。
