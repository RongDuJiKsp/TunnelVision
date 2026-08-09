# XTU-TunnelVision

基于 DNS-over-HTTPS (DoH) 的隧道流量检测与分析研究项目。本项目聚焦于 DNS 隧道技术的流量特征采集、自动化测试与数据分析，旨在为隧道流量识别提供数据支撑和工具链。

## 项目结构

本仓库通过 Git Submodule 组织以下子项目：

### [Emerging-DoHExfil-Tunnel](https://github.com/RongDuJiKsp/Emerging-DoHExfil-Tunnel)

项目的数据集，托管在 Google Drive

### [SafeDoHCapture](https://github.com/RongDuJiKsp/SafeDoHCapture)

基于 Selenium WebDriver 的 DoH 安全流量采集工具。使用 TypeScript 编写，通过无头浏览器自动访问网站列表，并配置不同的 DoH 提供商（如 Cloudflare、Google 等）进行 DNS 解析，用于采集正常 DoH 流量作为对照基准数据。

- **技术栈**：TypeScript, Selenium WebDriver, Node.js
- **用途**：生成正常 DoH 浏览流量样本

### [dnstt-tester](https://github.com/RongDuJiKsp/dnstt-tester)

DNS 隧道工具的自动化测试框架。使用 Rust 编写，支持客户端/服务端双端自动化运行，能够自动管理隧道工具进程的生命周期（启动、重连、超时重启），并定时生成随机文件通过隧道传输，用于产生隧道流量数据。

- **技术栈**：Rust, Tokio (异步运行时), Clap (CLI)
- **用途**：自动化驱动 DNS 隧道工具（如 dnstt）生成隧道流量样本

### [GoDoHCaper](https://github.com/RongDuJiKsp/GoDoHCaper)

基于 [godoh](https://github.com/sensepost/godoh) C2 框架的 DoH 隧道流量自动化生成工具。使用 Go 编写，作为 godoh 的上层调度器，自动管理 C2 客户端连接并周期性执行文件传输命令，用于生成基于 DoH 的 C2 隧道流量数据。

- **技术栈**：Go, godoh C2 框架
- **用途**：自动化生成 DoH C2 隧道流量样本

### [pcapSpliter](https://github.com/RongDuJiKsp/pcapSpliter)

PCAP 网络抓包文件分割工具。使用 Go 编写，基于 gopacket 库对 PCAP 文件按网络流（flow）进行会话重组与分割，输出按提供商和时间命名的独立 PCAP 文件，便于后续逐流分析。

- **技术栈**：Go, gopacket
- **用途**：将大型 PCAP 抓包文件按流分割为独立文件，便于流量特征分析

## 快速开始

```bash
# 克隆仓库（含子模块）
git clone --recurse-submodules https://github.com/RongDuJiKsp/XTU-TunnelVision.git

# 如果已克隆但未拉取子模块
git submodule update --init --recursive
```

## 工作流程

```
┌─────────────────────────────────────────────────────────────────┐
│                        数据采集阶段                               │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  SafeDoHCapture ──→ 正常 DoH 浏览流量 (对照组)                   │
│                                                                 │
│  dnstt-tester   ──→ DNS 隧道流量 (dnstt)                        │
│                                                                 │
│  GoDoHCaper     ──→ DoH C2 隧道流量 (godoh)                     │
│                                                                 │
├─────────────────────────────────────────────────────────────────┤
│                        数据处理阶段                               │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  tcpdump/抓包   ──→ PCAP 文件 ──→ pcapSpliter ──→ 按流分割文件   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## License

MIT
