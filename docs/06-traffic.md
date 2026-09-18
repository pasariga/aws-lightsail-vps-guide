# 06. vnStat 月度流量统计

节点稳定以后，我又加了一个很小的功能：查看这个月 VPS 总共用了多少流量。

没有用户面板，没有 Web Dashboard，没有公网 API，也没有因此开放新端口。统计工具用 Ubuntu 仓库里的 vnStat，监控实际公网接口 `ens5`。

本机只关心三个数：

- RX
- TX
- RX + TX

Lightsail 的数据传输计量会涉及 Network In 和 Network Out，所以本机显示的“本月已用”按 RX + TX 计算。

## 查看流量

服务器上可以直接运行：

```bash
traffic
```

通过 SSH alias 也可以在 Windows 查看：

```powershell
ssh tokyo-vps "traffic"
```

输出大致是：

```text
AWS Tokyo Traffic

本月上传：12.6 GB
本月下载：38.4 GB
本月已用：51.0 GB
月度额度：1 TB
使用率：5.1%
```

机器可读的月度汇总保存在：

```text
/home/ubuntu/traffic-status.json
```

文件只保存月份、RX、TX、合计和更新时间。vnStat 的保留策略也收敛成月度聚合，不保存域名、URL、目标 IP、DNS 查询、设备位置或访问内容。

四台设备共用同一台 VPS 时，这里看到的是四台设备经过该服务器的合计流量，不会按设备拆分。

还有两个容易忽略的点：

- vnStat 从安装完成后开始统计，不能倒推出安装前已经消耗的流量。
- 本机网卡统计和 AWS 的计费口径不保证完全一致，最终仍以 Lightsail / Billing 为准。
