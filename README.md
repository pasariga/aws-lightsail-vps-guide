[English](README.md) | [简体中文](README.zh-CN.md)

# AWS Lightsail VPS field notes

A personal, chronological guide to a lightweight Tokyo-region VPS built with Ubuntu, SSH and sing-box. It records the setup sequence, verification habits and staged prompts used with a local AI coding agent; it is documentation, not an installable application or an automated provisioning framework.

## Recorded design

- AWS Lightsail instance and static IPv4, with separate cloud/local firewall considerations.
- Ubuntu 24.04 LTS and key-based Windows SSH aliases.
- sing-box using VLESS over TCP, Reality and XTLS Vision on TCP 443, managed by systemd.
- UFW restricted to the documented SSH and service ports.
- Shadowrocket and FlClash/Mihomo client examples, plus local vnStat traffic accounting.

The recorded setup avoids a management panel, Docker and extra web-management ports. These are choices from one small personal deployment, not a general security guarantee, latency benchmark or recommendation for every workload. Historical prices, quotas and software behavior must be rechecked before reuse.

## Read in order

The detailed walkthrough is currently in Chinese:

1. [Lightsail instance and static IP](docs/01-lightsail.md)
2. [Windows SSH keys and aliases](docs/02-ssh.md)
3. [Staged AI-assisted server setup](docs/03-ai-server-setup.md)
4. [sing-box / VLESS Reality](docs/04-sing-box-reality.md)
5. [Client configuration](docs/05-client-config.md)
6. [vnStat traffic records](docs/06-traffic.md)
7. [The original staged prompts](docs/ai-prompts.md)

The useful artifact here is the progression from connectivity through service setup to explicit checks, rather than a single script that changes everything at once. Review each command in its own context; reading this repository does not authorize an agent to execute the prompts against a server.

## Scope and reuse

You need your own cloud account, instance and authorized SSH access to follow the guide. Examples use placeholders; private keys, UUIDs, Reality secrets and complete node links do not belong in public documentation. No server configuration, paid resource creation or live connectivity test was performed for this documentation revision. The guide's existence does not establish the current state of its original instance.

The history preserves the initial notes and the completed Chinese-guide summary. This is a historical practice record, not a continuously maintained distribution. Before adapting it, consult the current official documentation for AWS, sing-box and your client.

## Attribution and license

The repository records pasariga's practice and AI-assisted workflow; the referenced platforms and tools remain their respective authors' work. No repository-level license file is present. This README does not assign a new license or supersede upstream software terms.
