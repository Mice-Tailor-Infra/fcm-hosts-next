# FCM Hosts Next

自动维护 Google Firebase Cloud Messaging (FCM) 优选 Hosts。项目在中国大陆双栈机器上直接采集、实测、发布，目标是给 Android / microG / Google Play services 提供更稳定的 FCM 长连接入口。

## 架构

当前生产架构是单机流水线：

1. **Harvest**：使用 EDNS Client Subnet (ECS) 查询 `mtalk.google.com`，采集候选 IPv4/IPv6。
2. **Seed**：读取仓库内上一轮成功的 `fcm_*.hosts`，把历史可用 IP 继续作为种子。
3. **Verify**：在大陆双栈 self-hosted runner 上对候选 IP 执行 TCP `5228` 连接测速。
4. **Expand**：对已成功 IP 做小范围邻近扩展，再次实测。
5. **Publish**：生成并提交 `fcm_ipv4.hosts`、`fcm_ipv6.hosts`、`fcm_dual.hosts`。

核心实现是 Go 单二进制，无 Python runtime、无 pip 依赖、无跨 runner artifact 搬运。

## 产物

- [fcm_dual.hosts](fcm_dual.hosts)：推荐。双栈输出；若某次只选出单栈，会自动降级。
- [fcm_ipv6.hosts](fcm_ipv6.hosts)：纯 IPv6 输出。
- [fcm_ipv4.hosts](fcm_ipv4.hosts)：纯 IPv4 输出。

默认每 3 小时更新一次。

## 使用

GitHub Pages 分发地址：

```text
https://miceworld.top/fcm-hosts-next/fcm_dual.hosts
```

可用于：

- 系统 hosts 定时拉取并原子覆盖。
- sing-box / Clash 的 hosts 数据源。

## 本地运行

```bash
go test ./...
go run ./cmd/fcmhost run -workdir .
```

常用调试：

```bash
go run ./cmd/fcmhost run -workdir . -dns=false -v
```

`-dns=false` 只使用现有 hosts/raw seeds 做验证，适合快速确认当前网络能否连通 FCM `5228`。

---

_Status: Automated / Mainland dual-stack verified_
