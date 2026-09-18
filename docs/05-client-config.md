# 05. Shadowrocket 与 FlClash / Mihomo

服务端正常运行后，我让 AI 读取 `/etc/sing-box/config.json` 的真实参数，再生成客户端配置。这里有一个很重要的限制：只读取现有 UUID 和 Reality Key，不重新生成，否则已经运行的节点会被直接改坏。

## Shadowrocket

Shadowrocket 可以导入 VLESS 分享链接。公开文档里不放完整链接，只记录它需要的字段：

- Server：`YOUR_SERVER_IP`
- Port：`443`
- UUID：`YOUR_UUID`
- Flow：`xtls-rprx-vision`
- Security：Reality
- SNI：`YOUR_SNI`
- Reality Public Key：`YOUR_REALITY_PUBLIC_KEY`
- Short ID：`YOUR_SHORT_ID`
- Fingerprint：`chrome`

实际分享链接由 AI 根据服务器当前配置生成。我只在自己的客户端文件中保存，不提交 Git。

## FlClash / Mihomo

Mihomo 兼容配置可以写成下面的结构：

```yaml
proxies:
  - name: AWS-Tokyo
    type: vless
    server: YOUR_SERVER_IP
    port: 443
    uuid: YOUR_UUID
    network: tcp
    tls: true
    udp: true
    flow: xtls-rprx-vision
    servername: YOUR_SNI
    reality-opts:
      public-key: YOUR_REALITY_PUBLIC_KEY
      short-id: YOUR_SHORT_ID
    client-fingerprint: chrome
```

Reality Private Key 只留在服务端，绝对不能写进 Shadowrocket 或 Mihomo 配置。

## 导入和验证

Shadowrocket 这边，把 AI 生成的私有分享链接复制到设备中导入，选择 `AWS-Tokyo` 节点并使用规则模式。链接只保存在自己的客户端资料里，不放进仓库或截图。

FlClash 这边，把生成的 YAML 保存为本地配置并导入，选择 `AWS-Tokyo`，模式设为 Rule。需要接管更多 Windows 应用流量时再开启 TUN / 虚拟网卡，不必为了测试节点先改系统代理以外的设置。

我先测试节点本身能否连接，再分别打开一个应走代理的服务和一个应直连的国内服务。两边都符合预期后，才继续增加白名单规则。

## 我使用的分流逻辑

节点跑通以后，我没有设置“所有国外网站都代理”，而是做海外服务白名单：指定服务走 `AWS-Tokyo`，其他全部 `DIRECT`。

例如：

```text
ChatGPT  -> PROXY
Claude   -> PROXY
Gemini   -> PROXY
Google   -> PROXY
GitHub   -> PROXY

Bilibili -> DIRECT
小红书    -> DIRECT
微信      -> DIRECT
淘宝      -> DIRECT
```

Shadowrocket 使用规则模式。Windows 上的 FlClash 可以开启 TUN / 虚拟网卡，再配合规则模式。

不要把 Chrome 整个程序强制走代理。Chrome 里既会打开 Google，也会打开国内网站，按域名和服务分流更合适。规则也没必要一开始就堆几百条，缺哪个服务再补它真正需要的依赖域名。
