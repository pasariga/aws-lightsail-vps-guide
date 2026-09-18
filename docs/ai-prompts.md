# 实际使用的 AI Prompts

这些 Prompt 不是唯一正确写法，只是我实际搭建时采用的“让 AI 分阶段执行”的方式。每次最好控制任务范围，不要一次让 AI 同时改 SSH、防火墙、节点协议和客户端配置。

使用前替换 `YOUR_SERVER_IP` 等占位符。涉及敏感参数时，让 AI 在服务器上读取或在本地安全位置引用，不要把秘密粘贴进公开仓库。

## Prompt 1：第一次检查和 SSH 安全加固

```text
我要配置一台刚创建好的 AWS Lightsail VPS。

服务器信息：
- Region：Tokyo
- Ubuntu 24.04 LTS
- SSH 用户：ubuntu
- Static IPv4：YOUR_SERVER_IP
- 本机 SSH Key 已经验证可用

本轮只完成：
1. 通过 SSH 登录，检查系统版本、CPU、内存、磁盘、网络、当前监听端口、SSH 配置和系统防火墙。
2. 执行 apt update，并安装当前必要的安全更新。不要升级整个 Ubuntu 发行版，不安装无关软件。
3. 在确认当前 SSH Key 登录正常、不会把我锁在服务器外的前提下，禁用 SSH 密码登录，禁止 root 远程 SSH 登录，保留 ubuntu 用户和当前 Key。修改后先验证 sshd 配置，再 reload，不要直接 restart。
4. 根据 512 MB RAM 的实际情况检查是否需要少量 Swap，并检查 UFW 状态。

约束：
- 不使用来源不明的一键脚本。
- 不删除或替换现有 SSH Key。
- 不修改可能导致 SSH 失联的网络设置。
- 暂时不要开放代理端口。
- 完成后列出实际修改的文件、软件包、服务和当前状态。
```

## Prompt 2：部署 sing-box + VLESS Reality

```text
继续配置已经完成 SSH 安全加固的 AWS Lightsail Tokyo VPS。

环境：Ubuntu 24.04 LTS，512 MB RAM。目标是给个人设备部署轻量、可维护的代理节点。

本轮只完成：
1. 选择维护活跃、资源占用低的服务端实现，采用 sing-box。
2. 部署 VLESS + TCP + Reality + XTLS Vision，监听 TCP 443，生成安全的客户端身份参数和 Reality Key。
3. 配置 systemd 服务和开机自启，合理限制服务端配置文件权限。
4. 修改 UFW 前先确认 TCP 22 始终允许，再只开放最终需要的 TCP 443。
5. 明确告诉我还需要在 AWS Lightsail 控制台手动增加 TCP 443 入站规则，不要尝试绕过控制台。
6. 检查配置语法、systemd 状态、监听端口、最近日志和 CPU / 内存占用。

约束：
- 不使用来源不明的一键脚本。
- 不关闭 SSH，不开放全部端口。
- 不安装 Docker、面板或图形桌面。
- 不改变 Static IPv4。
- 如果配置检查失败，先定位原因，不要反复盲目重启。
```

## Prompt 3：生成 Shadowrocket + FlClash 客户端配置

```text
当前 sing-box 节点已经部署完成，不要重新部署，也不要重新生成 UUID 或 Reality Key。

本轮只完成：
1. 读取 /etc/sing-box/config.json 的真实配置，核对 UUID、Short ID、SNI、flow 和端口；根据现有 Reality Private Key 推导并核对 Public Key，不生成新的 Key Pair；客户端 fingerprint 使用 chrome。
2. 生成 Shadowrocket 可导入的 VLESS 分享链接。
3. 生成 FlClash / Mihomo 可直接使用的 YAML。
4. 做一次最终检查：sing-box active、TCP 443 正常监听、最近日志无持续 ERROR/FATAL、SSH 22 正常、UFW 规则正确。

要求：
- 客户端参数必须与当前服务端一致。
- 不修改服务端配置。
- 不把 Reality Private Key 写入任何客户端文件。
- 客户端节点名称使用 AWS-Tokyo。
```

## Prompt 4：配置海外白名单分流

```text
当前 AWS-Tokyo 节点已经在 Shadowrocket 和 FlClash 中测试成功。不要修改服务端节点参数。

现在只做“海外白名单代理，其他全部直连”的客户端分流：

走 AWS-Tokyo：
- ChatGPT / OpenAI / Codex
- Claude / Anthropic
- Gemini / Google AI
- Google 基础服务
- GitHub
- YouTube

其他所有流量默认 DIRECT。

请分别生成：
1. Shadowrocket 规则模式配置思路。
2. FlClash / Mihomo YAML 规则。
3. 合理的 DNS 分流，海外白名单避免不必要的本地明文 DNS 泄漏，未命中的国内服务保持正常直连解析。

要求：
- 不要把 Chrome 整个应用强制代理，只按域名和服务分流。
- 不加入这次没有列出的无关海外服务。
- 必要依赖域名可以补充，但不要无意义扩大白名单。
- 用 ChatGPT、Claude、Gemini、Google、GitHub、YouTube 和几个国内站点验证最终路由结果。
```

## Prompt 5：vnStat 月度流量统计

```text
继续维护当前已经正常运行的 AWS Lightsail Tokyo VPS。不要修改 sing-box、UUID、Reality Key、端口或客户端配置。

本轮只增加极轻量的本地月度流量统计：
1. 检查默认公网接口并安装 Ubuntu 官方仓库中的 vnStat。
2. 统计本月 RX、TX 和 RX + TX，数据重启后保留。
3. 提供本地 traffic 命令，显示上传、下载、合计、固定 1 TB 月度额度和使用率。
4. 保留机器可读取的本地月度 JSON，只保存月份、RX、TX、合计和更新时间。
5. 检查 sing-box、TCP 443、SSH 22 和 UFW 仍然正常。

要求：
- 不安装 Docker、数据库、Grafana 或 Prometheus。
- 不创建 Web 页面、HTTP/HTTPS API 或公网订阅地址。
- 不开放任何新公网端口。
- 不记录域名、URL、DNS 查询、目标 IP、设备位置、访问内容或具体连接日志。
- 本机统计只是估算，最终计费以 AWS Lightsail / Billing 为准。
```
