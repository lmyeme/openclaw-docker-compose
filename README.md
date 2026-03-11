## Docker Compose 部署 OpenClaw 相关的网络配置参数（ENV）

---


**场景 A：用 Docker 部署，从本机或局域网访问**

让 Gateway 监听容器的 `eth0` 接口，Docker 的端口映射才能正常转发。无论是在 Mac 本机访问还是局域网其他设备访问，都需要在 `ALLOWED_ORIGINS` 中列出所有合法的访问来源（含协议 + IP/域名 + 端口）：

```bash
OPENCLAW_GATEWAY_BIND=lan
ALLOWED_ORIGINS=["http://localhost:18789", "http://<你的IP或域名>:18789"]
OPENCLAW_GATEWAY_TOKEN=YOUR_SECURE_TOKEN_HERE
OPENCLAW_CONFIG_DIR=./.openclaw
OPENCLAW_WORKSPACE_DIR=./.openclaw/workspace
```

> 只在 Mac 本机浏览器访问的话，`ALLOWED_ORIGINS` 只填 `["http://localhost:18789"]` 即可。

---

**场景 B：用 Docker 部署，跳过来源校验（有风险，不推荐）**

不配置 `ALLOWED_ORIGINS`，改用 fallback 模式。此模式存在 DNS 重绑定攻击风险，官方标记为危险配置，仅建议在内网测试环境或明确了解风险的情况下使用：

```bash
OPENCLAW_GATEWAY_BIND=lan
DANGEROUS_ALLOW_HOST_HEADER_FALLBACK=true
OPENCLAW_GATEWAY_TOKEN=YOUR_SECURE_TOKEN_HERE
OPENCLAW_CONFIG_DIR=./.openclaw
OPENCLAW_WORKSPACE_DIR=./.openclaw/workspace
```

---

**场景 C：直接在宿主机原生安装，仅本机访问**

`loopback` 让 Gateway 只监听容器/进程内部的 `127.0.0.1`。**在 Docker 环境下**，Docker 的端口映射是从宿主机转发到容器的 `eth0` 网络接口，不经过容器内部的 loopback，因此 Mac 浏览器通过 `localhost:18789` 实际上无法到达容器内的 Gateway。所以这个选项**不适合 Docker 部署**，仅适用于不经过 Docker、直接在宿主机原生安装 OpenClaw 的场景：

```bash
OPENCLAW_GATEWAY_BIND=loopback
OPENCLAW_GATEWAY_TOKEN=YOUR_SECURE_TOKEN_HERE
OPENCLAW_CONFIG_DIR=./.openclaw
OPENCLAW_WORKSPACE_DIR=./.openclaw/workspace
```

[相关链接](https://mp.weixin.qq.com/s/RW5pp1wYPoXJvw6R_x1GFQ)
