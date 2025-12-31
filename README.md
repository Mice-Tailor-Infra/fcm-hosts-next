# FCM Hosts Next

这是一个自动化维护的 Google Firebase Cloud Messaging (FCM) 优选 Hosts 项目。通过分布式边缘探测架构，实时筛选针对中国大陆运营商环境优化的优质边缘节点。

## ⚙️ 自动化架构

本项目采用 **"前店后厂"** 的分布式协同逻辑：

1.  **数据采集 (Harvest)**：利用 GitHub Actions (US) 配合 EDNS Client Subnet (ECS) 技术，模拟国内各省市运营商网段，向 Google 权威 DNS 诱捕亚太区优质 IP 池。
2.  **真机校验 (Sommelier)**：通过部署在阿里云（呼和浩特 BGP）的 Self-hosted Runner 进行真机 TCP 握手测试（Port 5228），通过动态延迟算法筛选出“特级园”级别的 IP。
3.  **负载均衡 (Balancing)**：对筛选出的 IP 进行 Round-Robin 轮询分配，生成具备故障转移能力的双栈 Hosts 文件。

## 📦 产物列表

本项目每 6 小时自动更新一次，产物通过 EdgeOne CDN 进行全球分发。

- [fcm_dual.hosts](fcm_dual.hosts): **推荐使用**。双栈负载均衡版本，具备最佳兼容性。
- [fcm_ipv6.hosts](fcm_ipv6.hosts): 纯 IPv6 版本，适用于拥有 IPv6 环境的移动端或教育网。
- [fcm_ipv4.hosts](fcm_ipv4.hosts): 纯 IPv4 版本，作为传统环境的补丁。

## 🛠️ 使用方式

数据源通过 `https://miceworld.top/fcm-hosts-next/fcm_dual.hosts` 分发。

- **系统层**：建议通过自动化脚本每 12-24 小时拉取一次内容并原子化覆盖 `/etc/hosts`。
- **代理层**：可在 Sing-box 或 Clash 配置中通过 `hosts` 模块直接引用下载路径。

---

_Status: Automated / Real-time Verified_
