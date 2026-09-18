# 04. sing-box VLESS Reality

当前服务端方案是：

- sing-box
- VLESS
- TCP
- Reality
- XTLS Vision
- TCP 443
- systemd 管理

这台机器只有 512 MB RAM，所以没有再装面板、Docker 或额外数据库。sing-box 单服务足够轻，配置和日志位置也很明确。

## 部署前先确认

AI 开始安装前，我要求它检查：

```bash
sudo ss -lntp
sudo ufw status verbose
free -h
```

重点是 TCP 443 当时没有被其他服务占用，TCP 22 仍然允许。安装使用维护中的官方来源，不跑第三方一键脚本。

服务端配置位于：

```text
/etc/sing-box/config.json
```

文件里包含 UUID、Reality Private Key、Short ID 等敏感值，不能原样复制到公开仓库。

实际部署时，AI 根据服务器架构安装 sing-box，生成 UUID 和 Reality Key Pair，写入配置后先做语法检查：

```bash
sudo sing-box check -c /etc/sing-box/config.json
```

只有检查通过才交给 systemd 启动。版本号和官方下载地址会变化，我没有在仓库里固定一个可能过期的下载链接。

## 脱敏结构示例

下面只展示入站配置的大致关系，不是可以直接部署的完整文件：

```json
{
  "type": "vless",
  "listen": "::",
  "listen_port": 443,
  "users": [
    {
      "uuid": "YOUR_UUID",
      "flow": "xtls-rprx-vision"
    }
  ],
  "tls": {
    "enabled": true,
    "server_name": "YOUR_SNI",
    "reality": {
      "enabled": true,
      "handshake": {
        "server": "YOUR_SNI",
        "server_port": 443
      },
      "private_key": "YOUR_REALITY_PRIVATE_KEY",
      "short_id": ["YOUR_SHORT_ID"]
    }
  }
}
```

客户端使用的是由私钥对应生成的 `YOUR_REALITY_PUBLIC_KEY`，不是服务端 Private Key。

## systemd 与防火墙

安装完成后由 systemd 管理：

```bash
sudo systemctl enable --now sing-box
systemctl status sing-box --no-pager
```

UFW 的顺序不能反：先保证 SSH，再开放 Reality。

```bash
sudo ufw allow 22/tcp comment 'SSH'
sudo ufw allow 443/tcp comment 'VLESS Reality'
sudo ufw enable
sudo ufw status verbose
```

Lightsail 控制台防火墙也要手动增加 TCP 443 入站规则。服务器里的 UFW 和 AWS 控制台防火墙是两层配置，只改其中一层仍然可能连不上。

## 完成后的检查

```bash
systemctl status sing-box --no-pager
sudo ss -lntp
sudo journalctl -u sing-box -n 100 --no-pager
sudo sing-box check -c /etc/sing-box/config.json
```

我当时确认的是：sing-box 为 active，TCP 443 由 sing-box 监听，最近日志没有持续出现 ERROR/FATAL，SSH 22 仍然正常。

公开记录里统一使用这些占位符：

```text
YOUR_SERVER_IP
YOUR_UUID
YOUR_SNI
YOUR_REALITY_PUBLIC_KEY
YOUR_REALITY_PRIVATE_KEY
YOUR_SHORT_ID
```
