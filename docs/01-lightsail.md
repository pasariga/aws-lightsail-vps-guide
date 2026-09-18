# 01. 创建 Lightsail 实例

这里从 AWS 账号已经可以正常登录开始。控制台界面偶尔会改名字，但核心选项基本没变。

## 创建实例

进入 AWS Lightsail，点击 **Create instance**，按下面选择：

- Region：Tokyo
- Availability Zone：保持默认
- Platform：Linux/Unix
- Blueprint：Ubuntu 24.04 LTS
- Instance type：General purpose
- Networking：Dual-stack
- 套餐：$5/month

我创建时这个套餐是 512 MB RAM、2 vCPU、20 GB SSD，并包含每月 1 TB 数据传输额度。实际价格和额度以后可能调整，创建前以控制台显示为准。

没有开启 **Automatic snapshots**，也没有填写 **Launch script**。第一次搭建时让启动脚本同时改 SSH、装软件，出了问题反而不容易知道卡在哪一步。

## 准备 SSH Key

我在 Windows PowerShell 里创建 ed25519 Key：

```powershell
ssh-keygen -t ed25519 -f "$env:USERPROFILE\.ssh\id_ed25519"
```

如果这个路径已经有私钥，先停下来确认，不要覆盖。私钥一旦被覆盖，原来依赖它的服务器可能再也登不上去。

生成后会有两个文件：

- `id_ed25519`：私钥，只留在本机
- `id_ed25519.pub`：公钥，可以上传到 Lightsail

在创建实例页面选择更换 SSH Key，上传 `.pub` 公钥。不要上传没有 `.pub` 后缀的私钥文件。

确认实例名称后创建，等状态变成 Running。

## 绑定 Static IPv4

实例创建后，到 Lightsail 的 **Networking** 页面创建 Static IPv4，并绑定到刚才的实例。

后面的命令统一用：

```text
YOUR_SERVER_IP
```

Static IP 很有必要。普通公网地址可能在实例停止再启动后变化，一旦地址变化，SSH 配置、客户端节点和防火墙检查都要跟着改。固定地址后，这些配置可以长期保持不动。

此时先保留 Lightsail 默认 SSH 规则。TCP 443 等 sing-box 确定监听后再开放，不需要预先放开一堆端口。
