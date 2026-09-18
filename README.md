# AWS Lightsail 自建 VPS 实践记录

> AWS Lightsail + sing-box + VLESS Reality

这套 VPS 是我自己在 AWS Lightsail 东京区搭出来的。最开始只是想给自己的几台设备用，所以方案一直保持得很简单：不装面板，不跑 Docker，也不额外开放 Web 管理端口。

服务器创建完成并打通 SSH 后，安装、加固和 sing-box 配置主要交给 Codex 一类的本地 AI Agent 通过 SSH 执行。我把实际走过的顺序、用到的检查方法和当时的提示词整理在这里，方便以后重装，也给想走同样路线的人一个参考。

## 为什么选 Lightsail

Lightsail 的实例、静态 IP 和防火墙都集中在一个界面里，价格和月度流量额度也比较直观。我的实例在 Tokyo，使用 $5/month 套餐：512 MB RAM、2 vCPU、20 GB SSD 和 1 TB Transfer。对个人轻量节点来说够用，维护成本也低。

## 当前方案

- Ubuntu 24.04 LTS
- sing-box
- VLESS + TCP + Reality + XTLS Vision
- TCP 443，由 systemd 管理
- UFW 只允许 SSH 22 和 Reality 443
- Shadowrocket、FlClash / Mihomo 客户端
- vnStat 本地月度流量统计

完成后的结构很朴素：客户端连接 Lightsail 的 TCP 443，sing-box 负责 Reality 入站；系统服务由 systemd 管理，UFW 控制服务器本机入口，vnStat 只在本机统计网卡流量。

## 文档目录

第一次搭建就从 01 开始按顺序往下看；已经有可用服务器的话，可以直接跳到对应章节。AI 提示词单独放在最后一篇。

1. [创建 Lightsail 实例](docs/01-lightsail.md)
2. [Windows SSH 连接与别名](docs/02-ssh.md)
3. [让本地 AI 分阶段配置服务器](docs/03-ai-server-setup.md)
4. [sing-box VLESS Reality](docs/04-sing-box-reality.md)
5. [Shadowrocket 与 FlClash / Mihomo](docs/05-client-config.md)
6. [vnStat 月度流量统计](docs/06-traffic.md)
7. [实际使用的 AI Prompts](docs/ai-prompts.md)

这套记录适合已经有 AWS 账号、愿意用 SSH 管理 Linux，并且只想搭一台个人轻量 VPS 的人。它不是通用面板教程，也不覆盖机场管理、多用户计费或大规模运维。

安全提醒：私钥、UUID、Reality Private Key 和完整节点链接都不要提交到 Git。公开前再搜一遍整个仓库。
