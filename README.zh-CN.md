[English](README.md) | [简体中文](README.zh-CN.md)

# AWS Lightsail VPS 实践记录

按实际顺序记录的一次轻量东京 VPS 搭建实践，使用 Ubuntu、SSH 和 sing-box。内容包括配置次序、验证习惯和交给本地 AI 编程助手的分阶段提示词；这是文档项目，不是可安装应用，也不是自动化开通框架。

## 记录中的方案

- AWS Lightsail 实例与静态 IPv4，并区分云端防火墙和本机防火墙。
- Ubuntu 24.04 LTS，以及 Windows 密钥认证和 SSH 别名。
- sing-box：VLESS over TCP、Reality、XTLS Vision，TCP 443，由 systemd 管理。
- UFW 仅放行记录中的 SSH 和服务端口。
- Shadowrocket、FlClash／Mihomo 配置示例，以及本地 vnStat 流量记录。

原方案没有管理面板、Docker 或额外 Web 管理端口。这是一次个人轻量部署的取舍，不是通用安全保证、延迟基准或适合所有负载的建议。历史价格、流量额度和软件行为在复用前需要重新核实。

## 阅读顺序

详细教程目前为中文：

1. [Lightsail 实例与静态 IP](docs/01-lightsail.md)
2. [Windows SSH 密钥与别名](docs/02-ssh.md)
3. [分阶段 AI 辅助配置](docs/03-ai-server-setup.md)
4. [sing-box／VLESS Reality](docs/04-sing-box-reality.md)
5. [客户端配置](docs/05-client-config.md)
6. [vnStat 流量记录](docs/06-traffic.md)
7. [原始分阶段提示词](docs/ai-prompts.md)

这里的实践价值在于从连通、服务配置到明确检查的逐步推进，而不是一个一次性改动全部配置的脚本。每条命令都应结合上下文审阅，阅读仓库不等于授权助手对服务器执行提示词。

## 范围与复用

跟随教程需要自己的云账号、实例和合法 SSH 访问。示例使用占位符，私钥、UUID、Reality 私密参数和完整节点链接不应进入公开文档。本轮没有配置服务器、创建付费资源或执行实际连通测试；教程存在也不能证明原实例的当前状态。

历史保留了初始资料和中文指南完成记录。这是历史实践资料，不是承诺持续维护的发行项目。复用前应查阅 AWS、sing-box 和客户端的当前官方文档。

## 署名与许可

本仓库记录 pasariga 的个人实践与 AI 辅助工作流，相关平台和工具仍属于各自作者。当前没有仓库级许可证文件，本 README 不新增许可，也不替代上游软件条款。
