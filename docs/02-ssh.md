# 02. Windows SSH 连接与别名

Ubuntu Lightsail 镜像的默认用户是 `ubuntu`。在 PowerShell 里先直接连接：

```powershell
ssh ubuntu@YOUR_SERVER_IP
```

第一次连接会询问是否信任主机指纹。确认地址确实是刚创建的 Lightsail 实例后，输入：

```text
yes
```

登录后先做几项只读检查：

```bash
whoami
uname -a
free -h
df -h
```

- `whoami`：确认当前用户是 `ubuntu`
- `uname -a`：查看内核和系统架构
- `free -h`：查看内存和 Swap
- `df -h`：查看磁盘空间

## 设置 SSH alias

Windows OpenSSH 配置在：

```text
C:\Users\YOUR_WINDOWS_USER\.ssh\config
```

保留文件里已有的 Host，在末尾添加：

```sshconfig
Host tokyo-vps
  HostName YOUR_SERVER_IP
  User ubuntu
  IdentityFile C:/Users/YOUR_WINDOWS_USER/.ssh/id_ed25519
  IdentitiesOnly yes
  ServerAliveInterval 60
  ServerAliveCountMax 3
```

以后直接使用：

```powershell
ssh tokyo-vps
```

也可以不进入交互终端，直接执行一条远程命令：

```powershell
ssh tokyo-vps "traffic"
```

这个 alias 不只方便自己使用。Codex、OpenCode 等在本机运行的 Agent 也会读取同一份 SSH config，因此提示词里写 `ssh tokyo-vps` 就够了。

配置文件里只写私钥路径，不要粘贴私钥内容。
